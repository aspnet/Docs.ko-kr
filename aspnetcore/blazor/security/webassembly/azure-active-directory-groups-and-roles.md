---
title: Azure Active Directory 그룹 및 역할을 사용한 ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor WebAssembly에서 Azure Active Directory 그룹 및 역할을 사용하도록 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058261"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Azure Active Directory(AAD) 그룹, 관리자 역할, 사용자 정의 역할

작성자: [Luke Latham](https://github.com/guardrex) 및 [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> 이 문서는 Microsoft Identity 1.0을 사용하는 Blazor ASP.NET Core 앱 버전 3.1에 적용되며 Identity 2.0을 사용하는 5.0으로 업데이트될 예정입니다. 자세한 내용은 다음 GitHub 이슈 및 끌어오기 요청을 참조하세요. 끌어오기 요청의 **변경된 파일** 탭에는 문서 업데이트의 초안 텍스트와 예제가 포함됩니다. 검토 및 최종 업데이트 후에는 끌어오기 요청이 라이브 설명서 세트에 병합됩니다.
>
> * 문제: [AAD 그룹 및 역할을 사용하는 Blazor WASM(dotnet/AspNetCore #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)
> * 끌어오기 요청: [Blazor AAD 그룹 및 역할 항목 5.0(dotnet/AspNetCore #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)

AAD(Azure Active Directory)는 ASP.NET Core Identity와 결합할 수 있는 몇 가지 권한 부여 방법을 제공합니다.

* 사용자 정의 그룹
  * 보안
  * Microsoft 365
  * 분포
* 역할
  * AAD 관리자 역할
  * 사용자 정의 역할

이 문서의 지침은 다음 항목에서 설명하는 Blazor WebAssembly AAD 배포 시나리오에 적용됩니다.

* [Microsoft 계정을 사용하는 독립 실행형](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD를 사용하는 독립 실행형](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD를 사용하는 호스트형](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a>범위

5개 이상의 AAD 관리자 역할 및 보안 그룹 구성원 자격이 있는 앱 사용자에게는 [Microsoft Graph API](/graph/use-the-api) 호출이 필요합니다.

Graph API 호출을 허용하려면 Azure Portal에서 호스트된 Blazor 솔루션의 독립 실행형 또는 *`Client`* 앱에 다음 [Graph API 권한(범위)](/graph/permissions-reference)을 지정합니다.

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All`은 최소 권한 범위이며 이 문서에 설명된 예제에 사용되는 범위입니다.

## <a name="user-defined-groups-and-administrator-roles"></a>사용자 정의 그룹 및 관리자 역할

Azure Portal에서 `groups` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 다음 Azure 문서를 참조하세요. 사용자 정의 AAD 그룹 및 AAD 관리자 역할에 사용자를 할당합니다.

* [Azure AD 보안 그룹을 사용하는 역할](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` 특성](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

다음 예제에서는 사용자가 AAD 대금 청구 관리자 역할에 할당되었다고 가정합니다.

AAD에서 보낸 단일 `groups` 클레임은 사용자의 그룹 및 역할을 JSON 배열의 개체 ID(GUID)로 표시합니다. 앱은 그룹 및 역할의 JSON 배열을 앱이 기준으로 삼아 [정책](xref:security/authorization/policies)을 빌드할 수 있는 개별 `group` 클레임으로 변환해야 합니다.

할당된 AAD 관리자 역할 및 사용자 정의 그룹 수가 5개를 초과하면 AAD는 `groups` 클레임을 전송하는 대신 `true` 값으로 `hasgroups` 클레임을 전송합니다. 사용자에게 할당된 역할과 그룹이 5개 이상일 수 있는 모든 앱은 사용자의 역할 및 그룹을 가져오기 위해 별도의 Graph API 호출을 수행해야 합니다. 이 문서에서 제공하는 예제 구현은 이 시나리오를 다룹니다. `groups` 및 `hasgroups` 클레임 정보([Microsoft ID 플랫폼 액세스 토큰: 페이로드 클레임](/azure/active-directory/develop/access-tokens#payload-claims) 문서)를 참조하세요.

그룹 및 역할의 배열 속성을 포함하도록 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장합니다. 나중에 `foreach` 루프에서 속성을 사용할 때 `null` 확인이 필요하지 않도록 각 속성에 빈 배열을 할당합니다.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

다음 방법 중 **하나** 를 사용하여 AAD 그룹 및 역할에 대한 클레임을 만듭니다.

* [Graph SDK 사용](#use-the-graph-sdk)
* [명명된 `HttpClient` 사용](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a>Graph SDK 사용

[`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)에 대해 호스트된 Blazor 솔루션의 독립 실행형 앱 또는 *`Client`* 앱에 패키지 참조를 추가합니다.

<xref:blazor/security/webassembly/graph-api#graph-sdk> 문서의 *Graph SDK* 섹션에서 Graph SDK 유틸리티 클래스 및 구성을 추가합니다.

호스트된 Blazor 솔루션(`CustomAccountFactory.cs`)의 독립 실행형 앱 또는 *`Client`* 앱에 다음 사용자 지정 사용자 계정 팩터리를 추가합니다. 사용자 지정 사용자 팩터리는 역할 및 그룹 클레임을 처리하는 데 사용됩니다. `roles` 클레임 배열은 [사용자 정의 역할](#user-defined-roles) 섹션에서 설명합니다. `hasgroups` 클레임이 있는 경우 Graph SDK를 사용하여 사용자의 역할 및 그룹을 가져올 수 있도록 Graph API에 권한 있는 요청을 합니다.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

위의 코드에는 전이적 멤버 자격이 포함되어 있지 않습니다. 앱에 직접 및 전이적 그룹 멤버 자격 클레임이 필요한 경우 다음을 수행합니다.

* `groupsAndAzureRoles`에 대한 `IUserMemberOfCollectionWithReferencesPage` 형식을 `IUserTransitiveMemberOfCollectionWithReferencesPage`로 변경합니다.
* 사용자의 그룹 및 역할을 요청하는 경우 `MemberOf` 속성을 `TransitiveMemberOf`로 바꿉니다.

`Program.Main`(`Program.cs`)에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다. 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스를 사용하는 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a>명명된 `HttpClient` 사용

::: moniker-end

호스트된 Blazor 솔루션의 독립 실행형 앱 또는 *`Client`* 앱에서 사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 만듭니다. 역할 및 그룹 정보를 가져오는 Graph API 호출에 올바른 범위를 사용합니다.

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

`Program.Main`(`Program.cs`)에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 구현 서비스를 추가하고 Graph API 요청을 위한 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다. 다음 예제에서는 클라이언트의 이름을 `GraphAPI`로 지정합니다.

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Graph API 호출에서 OData(개방형 데이터 프로토콜) 역할 및 그룹을 수신하는 AAD 디렉터리 개체 클래스를 만듭니다. OData는 JSON 형식으로 도착하고 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 호출은 `DirectoryObjects` 클래스의 인스턴스를 채웁니다.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

역할 및 그룹 클레임을 처리하는 사용자 지정 사용자 팩터리를 만듭니다. 다음 예제 구현에서는 [사용자 정의 역할](#user-defined-roles) 섹션에서 설명하는 `roles` 클레임 배열도 처리합니다. `hasgroups` 클레임이 있는 경우 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 사용자의 역할 및 그룹을 가져올 수 있도록 Graph API에 권한 있는 요청을 합니다. 이 구현에서는 Microsoft Identity Platform v1.0 엔드포인트`https://graph.microsoft.com/v1.0/me/memberOf`([API 설명서](/graph/api/user-list-memberof))를 사용합니다.

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

원래 `groups` 클레임이 있다면 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.

> [!NOTE]
> 이 예제의 방법은 다음과 같습니다.
>
> * 사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 추가하여 나가는 요청에 액세스 토큰을 연결합니다.
> * 안전한 외부 웹 API 엔드포인트에 API 요청을 하기 위해 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다.
> * 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 권한 있는 요청을 합니다.
>
> 이 방법에 대한 일반적인 설명은 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> 문서에 있습니다.

호스트된 Blazor 솔루션의 독립 실행형 앱 또는 *`Client`* 앱의 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다. 앱의 추가 범위로 `Directory.Read.All` 범위에 동의합니다.

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a>권한 부여 구성

`Program.Main`의 그룹 또는 역할에 대해 [정책](xref:security/authorization/policies)을 만듭니다. 다음 예제에서는 AAD 대금 청구 관리자 역할에 대한 정책을 만듭니다.

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

AAD 역할 개체 ID의 전체 목록은 [AAD 관리자 역할 개체 ID](#aad-administrator-role-object-ids) 섹션을 참조하세요.

다음 예제에서 앱은 앞에 나온 정책을 사용하여 사용자에게 권한을 부여합니다.

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

[절차적 논리](xref:blazor/security/index#procedural-logic)를 사용하여 코드에서 정책 검사를 수행할 수도 있습니다.

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>사용자 정의 그룹 및 관리자 역할에 서버 API 액세스 권한 부여

서버 API는 클라이언트 쪽 WebAssembly 앱의 사용자에게 페이지와 리소스에 대한 액세스 권한을 부여하는 것 외에도 보안 API 엔드포인트에 대한 액세스 권한을 사용자에게 부여할 수 있습니다. 서버 앱이 사용자 액세스 토큰의 유효성을 검사한 후:

* 서버 API 앱은 액세스 토큰에서 사용자의 변경 불가능한 [개체 식별자 클레임(`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)을 사용하여 Graph API의 액세스 토큰을 가져옵니다.
* Graph API 호출은 사용자에 대해 [`memberOf`](/graph/api/user-list-memberof)를 호출하여 사용자의 Azure 사용자 정의 보안 그룹 및 관리자 역할 멤버 자격을 가져옵니다.
* 멤버 자격은 `group` 클레임을 설정하는 데 사용됩니다.
* [권한 부여 정책](xref:security/authorization/policies)을 사용하여 앱 전체에서 서버 API 엔드포인트에 대한 사용자 액세스를 제한할 수 있습니다.

> [!NOTE]
> 이 지침에는 현재 [AAD 사용자 정의 역할](#user-defined-roles)을 기준으로 한 사용자 권한 부여가 포함되지 않습니다.

이 섹션의 지침에서는 서버 API 앱을 Microsoft Graph API 호출에 대한 [‘디먼 앱’](/azure/active-directory/develop/scenario-daemon-overview)으로 구성합니다. 이 방법은 다음과 같은 특징이 있습니다.

* `access_as_user` 범위가 필요하지 않습니다.
* API를 요청하는 사용자/클라이언트를 대신하여 Graph API에 액세스하지 않습니다.

서버 API 앱에서 Graph API를 호출하려면 Azure Portal의 `Directory.Read.All`에 대한 서버 API 앱 **애플리케이션** Graph API 범위만 필요합니다. 이 방법을 사용하면 클라이언트 앱이 서버 API에서 명시적으로 허용하지 않는 디렉터리 데이터에 액세스할 수 없습니다. 클라이언트 앱은 서버 API 앱의 컨트롤러 엔드포인트에만 액세스할 수 있습니다.

### <a name="azure-configuration"></a>Azure 구성

* ‘서버’ 앱 등록에 보안 그룹의 최소 권한 액세스 수준인 `Directory.Read.All`에 대한 **애플리케이션**(**위임** 이 아님) Graph API 범위가 지정되었는지 확인합니다. 범위 할당을 수행한 후 관리자 동의가 범위에 적용되는지 확인합니다.
* 서버 앱에 새 클라이언트 암호를 할당합니다. [앱 설정](#app-settings) 섹션에서 앱 구성을 위한 암호를 확인합니다.

### <a name="app-settings"></a>앱 설정

앱 설정 파일(`appsettings.json` 또는 `appsettings.Production.json`)에서 Azure Portal의 서버 앱 클라이언트 암호를 사용하여 `ClientSecret` 항목을 만듭니다.

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

예를 들면 다음과 같습니다.

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 테넌트 게시자 도메인이 확인되지 않으면 사용자/클라이언트 액세스에 대한 서버 API 범위에서 `https://` 기반 URI를 사용합니다. 이 시나리오에서 서버 API 앱에는 `appsettings.json` 파일의 `Audience` 구성이 필요합니다. 다음 구성에서 `Audience` 값의 끝에는 기본 범위 `/{DEFAULT SCOPE}`가 포함되지 **않습니다**. 여기서 `{DEFAULT SCOPE}` 자리 표시자는 기본 범위입니다.
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> 위의 예제 구성은 다음과 같습니다.
>
> * 테넌트 도메인은 `contoso.onmicrosoft.com`입니다.
> * 서버 API 앱 `ClientId`는 `41451fa7-82d9-4673-8fa5-69eff5a761fd`입니다.
>
> > [!NOTE]
> > 명시적으로 `Audience`를 구성하면 `api://` 기반 API 범위가 있는 확인된 게시자 도메인이 앱에 필요하지 **않습니다**.
>
> 자세한 내용은 <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>를 참조하세요.

::: moniker-end

### <a name="authorization-policies"></a>네임스페이스 수준에서 구성하는 권한 부여 정책도

그룹 개체 ID와 [AAD 관리자 역할 개체 ID](#aad-administrator-role-object-ids)를 기준으로 서버 앱의 `Startup.ConfigureServices`(`Startup.cs`)에서 AAD 보안 그룹 및 AAD 관리자 역할의 [권한 부여 정책](xref:security/authorization/policies)을 만듭니다.

예를 들어 Azure 대금 청구 관리자 역할 정책의 구성은 다음과 같습니다.

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

자세한 내용은 <xref:security/authorization/policies>를 참조하세요.

### <a name="controller-access"></a>컨트롤러 액세스

서버 앱 컨트롤러에 대한 정책이 필요합니다.

다음 예제는 [권한 부여 정책](#authorization-policies) 섹션에서 구성된 대로 정책 이름 `BillingAdmin`을 사용하여 `BillingDataController`의 청구 데이터에 대한 액세스를 Azure 대금 청구 관리자로 제한합니다.

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a>패키지

다음 패키지의 서버 앱에 패키지 참조를 추가합니다.

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)

### <a name="services"></a>서비스

‘서버’ 앱의 `Startup.ConfigureServices` 메서드에서, ‘서버’ 앱의 `Startup` 클래스에 있는 코드에는 추가 네임스페이스가 필요합니다.  다음 네임스페이스를 `Startup.cs`에 추가합니다.

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>를 구성하는 경우:

* 필요한 경우 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>에 대한 처리를 포함합니다. 예를 들어 앱은 실패한 인증을 로그할 수 있습니다.
* <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>에서 Graph API 호출을 실행하여 사용자의 그룹과 역할을 얻습니다.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType>는 로깅 메시지에 PII(개인 식별 정보)를 제공합니다. 테스트 사용자 계정을 사용하여 디버깅하는 경우에만 PII를 활성화하세요.

`Startup.ConfigureServices`의 경우

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

위의 코드에서 다음 토큰 오류 처리는 선택 사항입니다.

* `MsalUiRequiredException`: 앱에 충분한 권한(범위)이 없습니다.
  * Azure Portal의 서버 API 앱 범위에 `Directory.Read.All`에 대한 **애플리케이션** 권한이 포함되어 있는지 확인합니다.
  * 테넌트 관리자가 앱에 대한 사용 권한을 부여했는지 확인합니다.
* `MsalServiceException`(`AADSTS70011`): 범위가 `https://graph.microsoft.com/.default`인지 확인합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a>패키지

다음 패키지의 서버 앱에 패키지 참조를 추가합니다.

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="service-configuration"></a>서비스 구성

서버 앱의 `Startup.ConfigureServices` 메서드는 Graph API 호출을 실행하는 논리를 추가하고 사용자의 보안 그룹 및 역할에 대한 사용자 `group` 클레임을 설정합니다.

> [!NOTE]
> 이 섹션의 예제 코드에서는 Microsoft Identity Platform v1.0을 기반으로 하는 ADAL(Active Directory 인증 라이브러리)을 사용합니다.

서버 앱의 `Startup` 클래스에 있는 코드에는 추가 네임스페이스가 필요합니다. 다음 `using` 문 집합에는 이 섹션 뒷부분에 나오는 코드의 필수 네임스페이스가 포함되어 있습니다.

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>를 구성하는 경우:

* 필요한 경우 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>에 대한 처리를 포함합니다. 예를 들어 앱은 실패한 인증을 로그할 수 있습니다.
* <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>에서 Graph API 호출을 실행하여 사용자의 그룹과 역할을 얻습니다.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType>는 로깅 메시지에 PII(개인 식별 정보)를 제공합니다. 테스트 사용자 계정을 사용하여 디버깅하는 경우에만 PII를 활성화하세요.

`Startup.ConfigureServices`의 경우

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

앞의 예제에서:

* 액세스 토큰이 ADAL 토큰 캐시에 이미 저장되어 있을 수 있으므로 자동 토큰 획득(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>)이 먼저 시도됩니다. 새 토큰을 요청하는 것보다 캐시에서 토큰을 얻는 것이 더 빠릅니다.
* 캐시에서 액세스 토큰을 획득하지 않는 경우(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> 또는 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>이 throw 됨) 클라이언트 자격 증명(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>)을 사용한 사용자 어설션(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>)을 통해 사용자를 대신하여 토큰을 얻습니다(<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>). 다음으로 `Microsoft.Graph.GraphServiceClient`가 토큰을 사용하여 Graph API 호출을 실행할 수 있습니다. 이 토큰은 ADAL 토큰 캐시에 저장됩니다. 향후 동일한 사용자에 대한 Graph API 호출의 경우 토큰은 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>와 함께 캐시에서 자동으로 획득됩니다.

::: moniker-end

<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated>의 코드는 전이적 멤버 자격을 얻지 못합니다. 직접 및 전이적 멤버 자격을 얻도록 코드를 변경하려면 다음을 수행합니다.

* 다음 코드 줄의 경우

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  위 줄을 다음으로 바꿉니다.

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* 다음 코드 줄의 경우

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  위 줄을 다음으로 바꿉니다.

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated>의 코드는 클레임을 만들 때 AAD 보안 그룹과 AAD 관리자 역할을 구분하지 않습니다. 앱이 그룹과 역할을 구분하도록 그룹과 역할을 반복할 때 `entry.ODataType`을 확인하세요. 별도의 보안 그룹 및 역할 클레임을 만들려면 다음과 유사한 코드를 사용합니다.

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a>사용자 정의 역할

AAD에 등록된 앱은 사용자 정의 역할을 사용하도록 구성될 수도 있습니다.

Azure Portal에서 `roles` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 Azure 설명서에서 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)를 참조하세요.

다음 예제에서는 앱이 두 개의 역할로 구성되어 있다고 가정합니다.

* `admin`
* `developer`

> [!NOTE]
> Azure AD Premium 계정을 사용하지 않고서는 보안 그룹에 역할을 할당할 수 없지만, 표준 Azure 계정을 사용하여 사용자를 역할에 할당하고 사용자에 대한 `roles` 클레임을 받을 수 있습니다. 이 섹션의 지침에는 Azure AD Premium 계정이 필요하지 않습니다.
>
> Azure Portal에서 각 추가 역할 할당에 대해 **‘사용자를 다시 추가’** 함으로써 여러 역할이 할당됩니다.

AAD에서 보낸 단일 `roles` 클레임은 사용자 정의 역할을 JSON 배열의 `appRoles`의 `value`로 표시합니다. 앱은 역할의 JSON 배열을 개별 `role` 클레임으로 변환해야 합니다.

[사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션에 표시된 `CustomUserFactory`는 JSON 배열 값을 사용하여 `roles` 클레임에서 작동하도록 설정되었습니다. [사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션에 나와 있는 것처럼 호스트된 Blazor 솔루션의 독립 실행형 앱 또는 *`Client`* 앱에 `CustomUserFactory`를 추가하고 등록합니다. 원래 `roles` 클레임은 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.

호스트된 Blazor 솔루션의 독립 실행형 앱 또는 *`Client`* 앱의 `Program.Main`에서 "`role`"이라는 클레임을 역할 클레임으로 지정합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

이 시점에서는 구성 요소 권한 부여 방식이 작동합니다. 구성 요소의 모든 권한 부여 메커니즘에서 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.

* [`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)(예: `<AuthorizeView Roles="admin">`)
* [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)(예: `@attribute [Authorize(Roles = "admin")]`)
* [절차적 논리](xref:blazor/security/index#procedural-logic)(예: `if (user.IsInRole("admin")) { ... }`)

  여러 역할 테스트가 지원됩니다.

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a>AAD 관리자 역할 개체 ID

다음 표에 나와 있는 개체 ID는 `group` 클레임에 대한 [정책](xref:security/authorization/policies)을 만드는 데 사용됩니다. 정책은 앱이 앱의 여러 활동에 대해 사용자에게 권한을 부여하도록 허용합니다. 자세한 내용은 [사용자 정의 그룹 및 AAD 관리자 역할](#user-defined-groups-and-administrator-roles) 섹션을 참조하세요.

AAD 관리자 역할 | 개체 ID입니다.
--- | ---
애플리케이션 관리자 | fa11557b-4f15-4ddd-85d5-313c7cd74047
애플리케이션 개발자 | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
인증 관리자 | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps 관리자 | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection 관리자 | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C IEF 키 세트 관리자 | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C IEF 정책 관리자 | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C 사용자 흐름 관리자 | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C 사용자 흐름 특성 관리자 | dd0baca0-a535-48c1-b871-8431abe16452
대금 청구 관리자 | 69ff516a-b57d-4697-a429-9de4af7b5609
클라우드 애플리케이션 관리자 | 250b5fe3-b553-458d-9a53-b782c13c34bf
클라우드 디바이스 관리자 | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
규정 준수 관리자 | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
규정 준수 데이터 관리자 | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
조건부 액세스 관리자 | 8f71a611-137d-49af-87ad-e97f1fd5da76
고객 LockBox 액세스 승인자 | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Desktop Analytics 관리자 | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
디렉터리 읽기 권한자 | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 관리자 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange 관리자 | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
외부 Identity 공급자 관리자 | febfaeb4-e478-407a-b4b3-f4d9716618a2
전역 관리자 | a45ba61b-44db-462c-924b-3b2719152588
글로벌 읽기 권한자 | f6903b21-6aba-4124-b44c-76671796b9d5
그룹 관리자 | 158b3e5a-d89d-460b-92b5-3b34985f0197
게스트 초대자 | 4c730a1d-cc22-44af-8f9f-4eec635c7502
기술 지원팀 관리자 | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune 관리자 | 79950741-23fa-4189-b2cb-46640601c497
Kaizala 관리자 | d6322af2-48e7-42e0-8c68-0bbe31af3412
라이선스 관리자 | 3355458a-e423-44bf-8b98-4ac5e572cea5
메시지 센터 개인 정보 읽기 권한자 | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
메시지 센터 읽기 권한자 | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office 앱 관리자 | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
암호 관리자 | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI 관리자 | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform 관리자 | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
권한 있는 인증 관리자 | 0829f731-b46d-419f-9742-aeb122367d11
권한 있는 역할 관리자 | f20a725a-d1c8-4107-83ea-1171c97d00c7
보고서 읽기 권한자 | 54635450-e8ed-4f2d-9632-07db2517b4de
Search 관리자 | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Search 편집자 | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
보안 관리자 | 20fa50e3-6531-44d8-bd39-b251420568ad
보안 운영자 | 43aae017-8e51-4188-91ab-e6debd572800
보안 판독기 | 45035cd3-fd97-4250-8197-3a53d3562d9b
서비스 지원 관리자 | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint 관리자 | e1c32229-875e-461d-ae24-3cb99116e86c
비즈니스용 Skype 관리자 | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Teams 통신 관리자 | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Teams 통신 지원 엔지니어 | 802dd94e-d717-46f6-af98-b9167071e9fc
Teams 커뮤니케이션 전문가 | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams 서비스 관리자 | 8846a0be-197b-443a-b13c-11192691fa24
사용자 관리자 | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
