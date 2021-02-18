---
title: Azure Active Directory 그룹 및 역할을 사용한 ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor WebAssembly에서 Azure Active Directory 그룹 및 역할을 사용하도록 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: c180580ec56313e444f2daf2b7d08c4d909b498a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280522"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>AAD(Azure Active Directory) 그룹, 관리자 역할 및 앱 역할

AAD(Azure Active Directory)는 ASP.NET Core Identity와 결합할 수 있는 몇 가지 권한 부여 방법을 제공합니다.

* 그룹
  * 보안
  * Microsoft 365
  * 분포
* 역할
  * AAD 관리자 역할
  * 앱 역할

이 문서의 지침은 다음 항목에서 설명하는 Blazor WebAssembly AAD 배포 시나리오에 적용됩니다.

* [Microsoft 계정을 사용하는 독립 실행형](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD를 사용하는 독립 실행형](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD를 사용하는 호스트형](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

이 문서에서는 클라이언트 앱과 서버 앱에 대한 지침을 제공합니다.

* **CLIENT**: 독립 실행형 Blazor WebAssembly 앱 또는 호스트된 Blazor 솔루션의 *`Client`* 앱입니다.
* **서버**: 독립 실행형 ASP.NET Core 서버 API/웹 API 앱 또는 호스트된 Blazor 솔루션의 *`Server`* 앱입니다.

## <a name="scopes"></a>범위

사용자 프로필, 역할 할당, 그룹 멤버 자격에 대한 [Microsoft Graph API](/graph/use-the-api) 호출을 허용하기 위해 Azure Portal에서 (`https://graph.microsoft.com/User.Read`) [Graph API 사용 권한(범위)](/graph/permissions-reference)을 사용하여 **CLIENT** 가 구성됩니다.

역할 및 그룹 멤버 자격 데이터를 위해 Graph API를 호출하는 **SERVER** 앱에 Azure Portal에서 `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API 사용 권한(범위)](/graph/permissions-reference)가 제공됩니다.

이러한 범위는 이 문서 첫 번째 섹션에 나열된 주제에서 설명하는 AAD 배포 시나리오에 필요한 범위 외에 필요합니다.

> [!NOTE]
> "사용 권한" 단어와 "범위" 단어는 Azure Portal과 다양한 Microsoft 및 외부 설명서에서 서로 바꿔 사용됩니다. 이 문서에서는 Azure Portal에서 앱에 할당된 사용 권한에 "범위"라는 단어를 계속 사용합니다.

## <a name="group-membership-claims-attribute"></a>그룹 멤버 자격 클레임 특성

Azure Portal에서 **CLIENT** 및 **SERVER** 앱의 앱 매니페스트에서 [`groupMembershipClaims` 특성](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)을 `All`로 설정합니다. `All` 값은 로그인한 사용자가 멤버인 모든 보안 그룹, 배포 그룹 및 역할을 가져옵니다.

1. 앱의 Azure Portal 등록을 엽니다.
1. 사이드바에서 **관리** > **매니페스트** 를 선택합니다.
1. `groupMembershipClaims` 특성을 찾습니다.
1. 값을 `All`로 설정합니다.
1. **저장** 단추를 선택합니다.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>사용자 지정 사용자 계정

Azure Portal에서 사용자를 AAD 보안 그룹 및 AAD 관리자 역할에 할당합니다.

이 문서의 예제는 다음과 같습니다.

* 서버 API 데이터에 엑세스할 수 있는 권한 부여를 위해 사용자가 Azure Portal AAD 테넌트의 AAD 대금 청구 관리자 역할에 할당되었다고 가정합니다.
* [권한 부여 정책](xref:security/authorization/policies)을 사용하여 **CLIENT** 및 **SERVER** 앱 내의 액세스를 제어합니다.

**CLIENT** 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하여 다음 속성을 포함합니다.

* `Roles`: AAD 앱 역할 배열([앱 역할](#app-roles) 섹션에서 설명)
* `Wids`: [잘 알려진 ID 클레임(`wids`)의 AAD 관리자 역할](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`: 변경할 수 없는 [개체 식별자 클레임(`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)(여러 테넌트와 테넌트 내에서 사용자를 고유하게 식별)

`foreach` 루프에서 배열 속성을 사용할 때 `null` 확인이 필요하지 않도록 각 배열 속성에 빈 배열을 할당합니다.

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

[`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)용 **CLIENT** 앱의 프로젝트 파일에 패키지 참조를 추가합니다.

<xref:blazor/security/webassembly/graph-api#graph-sdk> 문서의 *Graph SDK* 섹션에서 Graph SDK 유틸리티 클래스 및 구성을 추가합니다. `GraphClientExtensions` 클래스에서 `AuthenticateRequestAsync` 메서드에 있는 액세스 토큰의 `User.Read` 범위를 지정합니다.

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

**CLIENT** 앱에 다음 사용자 지정 사용자 계정 팩터리를 추가합니다. 사용자 지정 사용자 팩터리는 다음을 설정하는 데 사용됩니다.

* 앱 역할 클레임(`appRole`)([앱 역할](#app-roles) 섹션에서 설명)
* AAD 관리자 역할 클레임(`directoryRole`)
* 사용자의 휴대폰 번호에 대한 예제 사용자 프로필 데이터 클레임(`mobilePhone`)
* AAD 그룹 클레임(`directoryGroup`)

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

위의 코드에는 전이적 멤버 자격이 포함되어 있지 않습니다. 앱에 직접 및 전이적 그룹 멤버 자격 클레임이 필요한 경우 `MemberOf` 속성(`IUserMemberOfCollectionWithReferencesRequestBuilder`)을 `TransitiveMemberOf`(`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`)로 바꿉니다.

Microsoft Identity Platform 2.0에서 반환된 GUID 값이 AAD 관리자 역할 **엔터티 ID** 이고 [**역할 템플릿 ID**](/azure/active-directory/roles/permissions-reference#role-template-ids)가 아니므로 앞의 코드는 AAD 관리자 역할(`#microsoft.graph.directoryRole` 형식)인 그룹 멤버 자격 클레임(`groups`)을 무시합니다. 엔터티 ID는 Microsoft Identity Platform 2.0의 테넌트에서 안정적이지 않으며 앱에서 사용자에 대한 권한 부여 정책을 만드는 데 사용할 수 없습니다. **`wids` 클레임에서 제공하는** AAD 관리자 역할에 항상 **역할 템플릿 ID** 를 사용하세요.

**CLIENT** 앱의 `Program.Main`에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다.

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>권한 부여 구성

**CLIENT** 앱에서 `Program.Main`의 각 [앱 역할](#app-roles), AAD 관리자 역할 또는 보안 그룹의 [정책](xref:security/authorization/policies)을 만듭니다. 다음 예제에서는 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

AAD 관리자 역할 ID의 전체 목록은 Azure 설명서에서 [역할 템플릿 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)를 참조하세요. 권한 부여 정책에 대한 자세한 내용은 <xref:security/authorization/policies>를 참조하세요.

다음 예제에서 **CLIENT** 앱은 앞의 정책을 사용하여 사용자에게 권한을 부여합니다.

[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)는 이 정책을 준수합니다.

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

전체 구성 요소에 대한 액세스는 [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)을 사용하는 정책을 기반으로 할 수 있습니다.

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

사용자가 로그인되어 있지 않은 경우 AAD 로그인 페이지로 리디렉션되고 로그인한 후에 구성 요소로 다시 리디렉션됩니다.

[절차 논리](xref:blazor/security/index#procedural-logic)를 사용하여 코드에서 정책 검사를 수행할 수도 있습니다.

`Pages/CheckPolicy.razor`:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a>서버 API/웹 API 액세스 권한 부여

**SERVER** API 앱은 액세스 토큰에 `groups`, `wids` 및 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 클레임이 포함된 경우 보안 그룹, AAD 관리자 역할 및 앱 역할의 [권한 부여 정책](xref:security/authorization/policies)을 사용하여 보안 API 엔드포인트에 액세스할 수 있는 권한을 사용자에게 부여할 수 있습니다. 다음 예제는 `wids`(잘 알려진 ID/역할 템플릿 ID) 클레임을 사용하여 `Startup.ConfigureServices`에서 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

AAD 관리자 역할 ID의 전체 목록은 Azure 설명서에서 [역할 템플릿 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)를 참조하세요. 권한 부여 정책에 대한 자세한 내용은 <xref:security/authorization/policies>를 참조하세요.

**SERVER** 앱의 컨트롤러에 대한 액세스는 정책 이름과 함께 [`[Authorize]` 특성](xref:security/authorization/simple) 사용을 기반으로 할 수 있습니다(API 설명서: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).

다음 예제는 정책 이름 `BillingAdministrator`를 사용하여 `BillingDataController`의 청구 데이터에 대한 액세스를 Azure 대금 청구 관리자로 제한합니다.

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

자세한 내용은 <xref:security/authorization/policies>를 참조하세요.

## <a name="app-roles"></a>앱 역할

Azure Portal에서 Azure 역할 멤버 자격 클레임을 제공하도록 앱을 구성하려면 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)를 참조하세요.

다음 예제에서는 **CLIENT** 및 **SERVER** 앱이 두 개의 역할로 구성되고 이 역할이 테스트 사용자에게 할당된다고 가정합니다.

* `admin`
* `developer`

> [!NOTE]
> 호스트된 Blazor WebAssembly 앱 또는 독립 실행형 앱의 클라이언트-서버 쌍(독립 실행형 Blazor WebAssembly 앱 및 독립 실행형 ASP.NET Core 서버 API/웹 API 앱)을 개발하는 경우 클라이언트 및 서버 Azure Portal 앱 등록 `appRoles` 매니페스트 속성은 구성된 동일한 역할을 포함해야 합니다. 클라이언트 앱의 매니페스트에 역할을 설정한 후 역할 전체를 서버 앱의 매니페스트에 복사합니다. 클라이언트 앱 등록과 서버 앱 등록 간에 매니페스트 `appRoles`를 미러링하지 않는 경우 액세스 토큰에 올바른 역할 클레임이 있는 경우에도 서버 API/웹 API의 인증된 사용자에 대해 역할 클레임이 설정되지 않습니다.

> [!NOTE]
> Azure AD Premium 계정을 사용하지 않고서는 그룹에 역할을 할당할 수 없지만 표준 Azure 계정을 사용하여 역할을 사용자에게 할당하고 사용자에 대한 역할 클레임을 받을 수 있습니다. 이 섹션의 지침에는 AAD Premium 계정이 필요하지 않습니다.
>
> Azure Portal에서 각 추가 역할 할당에 대해 **‘사용자를 다시 추가’** 함으로써 여러 역할이 할당됩니다.

[사용자 지정 사용자 계정](#custom-user-account) 섹션에 표시된 `CustomAccountFactory`는 JSON 배열 값을 사용하여 `roles` 클레임에 대해 작동하도록 설정됩니다. [사용자 지정 사용자 계정](#custom-user-account) 섹션에 표시된 대로 **CLIENT** 앱에 `CustomAccountFactory`를 추가하고 등록합니다. 원래 `roles` 클레임은 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.

**CLIENT** 앱의 `Program.Main`에서 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 검사를 위한 역할 클레임으로 "`appRole`"이라는 클레임을 지정합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> `directoryRoles` 클레임(ADD 관리자 역할)을 사용하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>에 "`directoryRoles`"를 할당합니다.

**SERVER** 앱의 `Startup.ConfigureServices`에서 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 검사를 위한 역할 클레임으로 "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`"이라는 클레임을 지정합니다.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> `wids` 클레임(ADD 관리자 역할)을 사용하려면 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>에 "`wids`"를 할당합니다.

이 시점에서는 구성 요소 권한 부여 방식이 작동합니다. **CLIENT** 앱의 구성 요소에 있는 권한 부여 메커니즘은 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.

* [`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [절차적 논리](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

여러 역할 테스트가 지원됩니다.

* 사용자는 `AuthorizeView` 구성 요소가 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* 사용자는 `AuthorizeView` 구성 요소가 있는 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* 사용자는 `[Authorize]` 특성이 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* 사용자는 `[Authorize]` 특성이 있는 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* 사용자는 프로시저 코드가 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* 앞의 예제에서 [조건부 OR(`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 를 [조건부 AND(`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)로 변경하면 사용자가 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

**SERVER** 앱의 구성 요소에 있는 권한 부여 메커니즘은 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.

* [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [절차적 논리](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

여러 역할 테스트가 지원됩니다.

* 사용자는 `[Authorize]` 특성이 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* 사용자는 `[Authorize]` 특성이 있는 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* 사용자는 프로시저 코드가 있는 `admin` **또는** `developer` 역할 중 **하나** 에 있어야 합니다.

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* 앞의 예제에서 [조건부 OR(`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 를 [조건부 AND(`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)로 변경하면 사용자가 `admin` **및** `developer` 역할 **모두** 에 있어야 합니다.

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>추가 리소스

* [역할 템플릿 ID(Azure 설명서)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` 특성(Azure 설명서)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기(Azure 설명서)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [애플리케이션 역할(Azure 설명서)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
