---
title: Identity 서버를 사용하여 호스트된 ASP.NET Core Blazor WebAssembly 앱 보호
author: guardrex
description: Identity Server를 사용하여 호스트된 ASP.NET Core Blazor WebAssembly 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: c74711c10fe399718600f879c3d9151bfb1abd42
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281000"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Identity 서버를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호

이 문서에서는 [IdentityServer](https://identityserver.io/)를 사용하여 사용자와 API 호출을 인증하는 ‘[호스트형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)’을 만드는 방법을 설명합니다.

> [!NOTE]
> 독립형 또는 호스트형 Blazor WebAssembly 앱을 구성하여 기존의 외부 Identity 서버 인스턴스를 사용하려면 <xref:blazor/security/webassembly/standalone-with-authentication-library>의 지침을 따르세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면

1. **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor WebAssembly 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.

1. **개별 사용자 계정** 을 **사용자 계정 앱 내 저장** 옵션과 함께 선택하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.

1. **고급**  섹션에서 **ASP.NET Core 호스트형** 확인란을 선택합니다.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

인증 메커니즘을 사용하여 빈 폴더에 새 Blazor WebAssembly 프로젝트를 만들려면 `-au|--auth` 옵션으로 `Individual` 인증 메커니즘을 지정하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| 자리표시자  | 예제        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.

자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면

1. **새 Blazor WebAssembly 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.

1. ASP.NET Core [Identity](xref:security/authentication/identity)를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.

1. **ASP.NET Core 호스트형** 확인란을 선택합니다.

---

## <a name="server-app-configuration"></a>*`Server`* 앱 구성

다음 섹션에서는 인증 지원이 포함된 경우 프로젝트의 추가 사항에 대해 설명합니다.

### <a name="startup-class"></a>시작 클래스

`Startup` 클래스에는 다음과 같은 추가 사항이 있습니다.

* `Startup.ConfigureServices`의 경우

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer 및 IdentityServer 위에 기본 ASP.NET Core 규칙을 설정하는 추가 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer에 의해 생성된 JWT 토큰의 유효성을 검사하도록 앱을 구성하는 추가 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드를 사용한 인증:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`의 경우

  * IdentityServer 미들웨어가 OIDC(OpenID Connect) 엔드포인트를 노출합니다.

    ```csharp
    app.UseIdentityServer();
    ```

  * 인증 미들웨어는 요청 자격 증명의 유효성을 검사하고 요청 컨텍스트에서 사용자를 설정하는 작업을 담당합니다.

    ```csharp
    app.UseAuthentication();
    ```

  * 인증 미들웨어가 인증 기능을 사용하도록 설정합니다.

    ```csharp
    app.UseAuthorization();
    ```

### <a name="azure-app-service-on-linux"></a>Linux의 Azure App Service

Azure App Service on Linux에 배포할 때 발급자를 명시적으로 지정합니다. 자세한 내용은 <xref:security/authentication/identity/spa#azure-app-service-on-linux>를 참조하세요.

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드는 ASP.NET Core 시나리오에 대해 [IdentityServer](https://identityserver.io/)를 구성합니다. IdentityServer는 앱 보안 문제를 처리하는 강력하고 확장성 있는 프레임워크입니다. IdentityServer는 대부분의 일반적인 시나리오에서 불필요한 복잡성을 노출합니다. 따라서 좋은 출발점이라고 생각되는 몇 가지 규칙과 구성 옵션 세트가 제공됩니다. 인증에 변경이 필요할 경우 IdentityServer의 모든 기능을 사용하여 앱의 요구 사항에 맞게 인증을 사용자 지정할 수 있습니다.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드는 앱의 정책 체계를 기본 인증 처리기로서 구성합니다. 정책은 Identity가 Identity URL 공간 `/Identity`에 있는 모든 하위 경로로 라우팅된 모든 요청을 처리할 수 있도록 구성됩니다. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>는 다른 모든 요청을 처리합니다. 이 메서드는 또한

* 기본 범위 `{APPLICATION NAME}API`를 사용하여 `{APPLICATION NAME}API` API 리소스를 IdentityServer에 등록합니다.
* JWT 전달자 토큰 미들웨어가 앱에 대해 IdentityServer에서 발행한 토큰의 유효성을 검사하도록 구성합니다.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController`(`Controllers/WeatherForecastController.cs`)에서는 [`[Authorize]` 특성](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)이 클래스에 적용됩니다. 이 특성은 사용자가 리소스에 액세스하려면 기본 정책을 기준으로 인증되어야 함을 나타냅니다. 기본 인증 정책은 기본 인증 체계를 사용하도록 구성되었으며, 기본 인증 체계는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>에 의해 설정됩니다. 도우미 메서드는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>를 앱에 대한 요청의 기본 처리기로 구성합니다.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext`(`Data/ApplicationDbContext.cs`)에서 <xref:Microsoft.EntityFrameworkCore.DbContext>는 IdentityServer에 대한 스키마를 포함하도록 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>를 확장합니다. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.

데이터베이스 스키마에 대한 모든 권한을 얻으려면 사용 가능한 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속하고 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출하여 Identity 스키마를 포함하도록 컨텍스트를 구성합니다.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController`(`Controllers/OidcConfigurationController.cs`)에서 클라이언트 엔드포인트가 OIDC 매개 변수를 제공하도록 프로비저닝됩니다.

### <a name="app-settings"></a>앱 설정

프로젝트 루트에 있는 앱 설정 파일(`appsettings.json`)의 `IdentityServer` 섹션은 구성된 클라이언트 목록을 설명합니다. 다음 예제에는 단일 클라이언트가 있습니다. 클라이언트 이름은 앱 이름에 대응되며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다. 프로필은 구성되는 앱 유형을 나타냅니다. 프로필은 서버에 대한 구성 프로세스를 간소화하는 규칙을 구현하기 위해 내부적으로 사용됩니다. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.Client`).

## <a name="client-app-configuration"></a>*`Client`* 앱 구성

### <a name="authentication-package"></a>인증 패키지

앱이 개별 사용자 계정을 사용하도록 만들어진 경우(`Individual`) 해당 앱은 앱의 프로젝트 파일에서 자동으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지에 대한 패키지 참조를 받습니다. 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.

앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)의 패키지 **버전 기록** 에서 찾을 수 있습니다.

### <a name="httpclient-configuration"></a>`HttpClient` 구성

`Program.Main`(`Program.cs`)에서 명명된 <xref:System.Net.Http.HttpClient>(`HostIS.ServerAPI`)는 서버 API에 요청할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스를 제공하도록 구성됩니다.

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> 호스트형 Blazor 솔루션의 일부가 아닌 기존 Identity 서버 인스턴스를 사용하도록 Blazor WebAssembly 앱을 구성하는 경우, <xref:System.Net.Http.HttpClient> 기준 주소 등록을 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`builder.HostEnvironment.BaseAddress`)에서 서버 앱의 API 권한 부여 엔드포인트 URL로 변경합니다.

### <a name="api-authorization-support"></a>API 권한 부여 지원

사용자 인증에 대한 지원은 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지 내에서 제공하는 확장 메서드를 통해 서비스 컨테이너에 연결되어 있습니다. 이 메서드는 앱이 기존 인증 시스템과 상호 작용하는 데 필요한 서비스를 설정합니다.

```csharp
builder.Services.AddApiAuthorization();
```

기본적으로 앱에 대한 구성은 규칙에 따라 `_configuration/{client-id}`에서 로드됩니다. 규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정됩니다. 이 URL은 옵션을 사용하여 오버로드를 호출함으로써 별도의 엔드포인트를 가리키도록 변경될 수 있습니다.

### <a name="imports-file"></a>Imports 파일

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a>App 구성 요소

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.

* 인증된 사용자의 경우:
  * 현재 사용자 이름을 표시합니다.
  * ASP.NET Core Identity에 있는 사용자 프로필 페이지로 연결되는 링크를 제공합니다.
  * 앱에서 로그아웃하는 단추를 제공합니다.
* 익명 사용자의 경우:
  * 등록 옵션을 제공합니다.
  * 로그인 옵션을 제공합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Authentication 구성 요소

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행합니다. Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.

* 도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.
* **솔루션 탐색기** 에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.

## <a name="name-and-role-claim-with-api-authorization"></a>API 권한 부여를 사용한 이름 및 역할 클레임

### <a name="custom-user-factory"></a>사용자 지정 사용자 팩터리

*`Client`* 앱에서 사용자 지정 사용자 팩터리를 만듭니다. Identity 서버가 단일 `role` 클레임에서 여러 역할을 JSON 배열로 보냅니다. 클레임에서 단일 역할이 문자열 값으로 전송됩니다. 팩터리가 각 사용자 역할에 대해 개별 `role` 클레임을 만듭니다.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

*`Client`* 앱에서 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다.

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

*`Server`* 앱에서 Identity 작성기에 대해 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*>를 호출하여 역할 관련 서비스를 추가합니다.

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Identity Server 구성

다음 방법 중 **하나** 를 사용합니다.

* [API 권한 부여 옵션](#api-authorization-options)
* [프로필 서비스](#profile-service)

#### <a name="api-authorization-options"></a>API 권한 부여 옵션

*`Server`* 앱에서:

* Identity 서버가 `name` 및 `role` 클레임을 ID 토큰 및 액세스 토큰에 배치하도록 구성합니다.
* JWT 토큰 처리기에서 역할의 기본 매핑을 방지합니다.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>프로필 서비스

*`Server`* 앱에서 `ProfileService` 구현을 만듭니다.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

*`Server`* 앱에서 `Startup.ConfigureServices`에 프로필 서비스를 등록합니다.

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>권한 부여 메커니즘 사용

이 시점에서는 *`Client`* 앱의 구성 요소 권한 부여 방식이 작동합니다. 구성 요소의 모든 권한 부여 메커니즘에서 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.

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

*`Client`* 앱에서 `User.Identity.Name`에 사용자의 사용자 이름이 채워집니다. 이는 보통 사용자의 로그인 전자 메일 주소입니다.

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain-and-certificate"></a>사용자 지정 도메인과 인증서를 사용하여 Azure App Service에서 호스트

다음 지침에서 설명하는 것은 아래와 같습니다.

* Identity 서버와 함께 호스트형 Blazor WebAssembly 앱을 사용자 지정 도메인을 사용하는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포하는 방법.
* 브라우저와의 HTTPS 프로토콜 통신을 위해 TLS 인증서를 만들고 사용하는 방법. 이 지침은 사용자 지정 도메인에 이 인증서를 사용하는 방법을 중점적으로 설명하지만 `contoso.azurewebsites.net`와 같은 기본 Azure 앱 도메인을 사용하는 경우에도 똑같이 적용할 수 있습니다.

이 호스팅 시나리오에서는 [Identity 서버의 토큰 서명 키](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) 및 브라우저에 대한 사이트의 HTTPS 보안 통신에 동일한 인증서를 사용하지 **않습니다**.

* 두 가지 해당 요구 사항에 서로 다른 인증서를 사용하는 것은 각 목적에 맞게 프라이빗 키를 격리하기 때문에 좋은 보안 사례입니다.
* 브라우저와 통신하기 위한 TLS 인증서는 Identity 서버의 토큰 서명에 영향을 주지 않고 독립적으로 관리됩니다.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 사용자 지정 도메인 바인딩을 위해 App Service 앱에 인증서를 제공하는 경우 Identity 서버는 토큰 서명을 위해 Azure Key Vault에서 동일한 인증서를 가져올 수 없습니다. 실제 경로에서 동일한 TLS 인증서를 사용하도록 Identity 서버를 구성할 수 있지만 보안 인증서를 소스 제어에 배치하는 것은 **좋지 않은 사례이며 대부분의 시나리오에서 피해야 합니다**.

다음 지침에서 자체 서명된 인증서는 Identity 서버 토큰 서명을 위해서만 Azure Key Vault에 생성됩니다. Identity 서버 구성은 앱의 `My` > `CurrentUser` 인증서 저장소를 통해 키 자격 증명 모음 인증서를 사용합니다. 사용자 지정 도메인을 사용하는 HTTPS 트래픽에 사용되는 기타 인증서는 Identity 서버 서명 인증서와 별도로 생성 및 구성됩니다.

사용자 지정 도메인 및 HTTPS를 사용하여 호스트하도록 앱, Azure App Service 및 Azure Key Vault를 구성하려면:

1. `Basic B1` 이상의 계획 수준을 사용하여 [App Service 요금제](/azure/app-service/overview-hosting-plans)를 만듭니다. App Service에서는 사용자 지정 도메인을 사용하려면 `Basic B1` 이상 서비스 계층이 필요합니다.
1. 조직이 제어하는 사이트 FQDN(정규화된 도메인 이름)의 일반 이름(예: `www.contoso.com`)을 사용하여 사이트 보안 브라우저 통신(HTTPS 프로토콜)용 PFX 인증서를 만듭니다. 다음을 사용하여 인증서를 만듭니다.
   * 키 사용
     * 디지털 시그니처 유효성 검사(`digitalSignature`)
     * 키 암호화(`keyEncipherment`)
   * 확장 키 사용
     * 클라이언트 인증(1.3.6.1.5.5.7.3.2)
     * 서버 인증(1.3.6.1.5.5.7.3.1)

   인증서를 만들려면 다음 접근 방식 중 하나를 사용하거나 다른 적합한 도구나 온라인 서비스를 사용합니다.

   * [Azure Key Vault](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [Windows의 MakeCert](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   나중에 Azure Key Vault로 인증서를 가져오는 데 사용되는 암호를 기록해 둡니다.

   Azure Key Vault 인증서에 관한 자세한 내용은 [Azure Key Vault: 인증서](/azure/key-vault/certificates/)를 선택합니다.
1. 새 Azure Key Vault를 만들거나 Azure 구독에서 기존 키 자격 증명 모음을 사용합니다.
1. 키 자격 증명 모음의 **인증서** 영역에서 PFX 사이트 인증서를 가져옵니다. 나중에 앱 구성에서 사용되는 인증서 지문을 기록합니다.
1. Azure Key Vault에서 Identity 서버 토큰 서명을 위한 새로운 자체 서명된 인증서를 생성합니다. 인증서에 **인증서 이름** 및 **주체** 를 지정합니다. **주체** 는 `CN={COMMON NAME}`으로 지정됩니다. 여기서 `{COMMON NAME}` 자리 표시자는 인증서의 일반 이름입니다. 일반 이름은 영숫자 문자열일 수 있습니다. 예를 들어 `CN=IdentityServerSigning`은 유효한 인증서 **주체** 입니다. 기본 **고급 정책 구성** 설정을 사용합니다. 나중에 앱 구성에서 사용되는 인증서 지문을 기록합니다.
1. Azure Portal에서 Azure App Service로 이동하고 다음 구성을 사용하여 새 App Service를 만듭니다.
   * **게시** 를 `Code`로 설정합니다.
   * **런타임 스택** 을 앱 런타임으로 설정합니다.
   * **Sku 및 크기** 의 경우 App Service 계층이 `Basic B1` 이상인지 확인합니다.  App Service에서는 사용자 지정 도메인을 사용하려면 `Basic B1` 이상 서비스 계층이 필요합니다.
1. Azure가 App Service를 만든 후 앱 **구성** 을 열고 이전에 기록된 인증서 지문을 지정하는 새 애플리케이션 설정을 추가합니다. 앱 설정 키는 `WEBSITE_LOAD_CERTIFICATES`입니다. 다음 예제와 같이 앱 설정 값의 인증서 지문을 쉼표로 구분합니다.
   * 키: `WEBSITE_LOAD_CERTIFICATES`
   * 값: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   Azure Portal에서 앱 설정을 저장하는 작업은 2단계 프로세스입니다. `WEBSITE_LOAD_CERTIFICATES` 키-값 설정을 저장한 다음, 블레이드 위쪽에서 **저장** 단추를 선택합니다.
1. 앱의 **TLS/SSL 설정** 을 선택합니다. **프라이빗 키 인증서(.pfx)** 를 선택합니다. **키 자격 증명 모음 인증서 가져오기** 프로세스를 두 번 사용하여 HTTPS 통신용 사이트 인증서 및 사이트의 자체 서명된 Identity 서버 토큰 서명 인증서를 둘 다 가져옵니다.
1. **사용자 지정 도메인** 블레이드로 이동합니다. 도메인 등록 기관의 웹 사이트에서 **IP 주소** 및 **사용자 지정 도메인 확인 ID** 를 사용하여 도메인을 구성합니다. 일반적인 도메인 구성에는 다음이 포함됩니다.
   * `@`의 **호스트** 및 Azure Portal의 IP 주소 값이 포함된 **A 레코드**.
   * `asuid`의 **호스트** 및 Azure에서 생성하고 Azure Portal에서 제공하는 확인 ID 값이 포함된 **TXT 레코드**.

   도메인 등록 기관의 웹 사이트에서 변경 내용을 올바르게 저장해야 합니다. 일부 등록 기관 웹 사이트에서는 도메인 레코드를 저장하려면 2단계 프로세스가 필요합니다. 하나 이상의 레코드를 개별적으로 저장한 다음, 별도 단추를 사용하여 도메인 등록을 업데이트합니다.
1. Azure Portal에서 **사용자 지정 도메인** 블레이드로 돌아갑니다. **사용자 지정 도메인 추가** 를 선택합니다. **A 레코드** 옵션을 선택합니다. 도메인을 제공하고 **유효성 검사** 를 선택합니다. 도메인 레코드가 올바르고 인터넷에서 전파되는 경우 포털에서 **사용자 지정 도메인 추가** 단추를 선택할 수 있습니다.

   도메인 등록 변경이 도메인 등록 기관에서 처리된 후 인터넷 DNS(도메인 이름 서버)에서 전파되는 데 며칠이 걸릴 수 있습니다. 도메인 레코드가 영업일 기준 3일 이내에 업데이트되지 않는 경우 도메인 등록 기관을 통해 레코드가 올바르게 설정되었는지 확인하고 고객 지원에 문의하세요.
1. **사용자 지정 도메인** 블레이드에서 도메인의 **SSL 상태** 가 `Not Secure`로 표시됩니다. **바인딩 추가** 링크를 선택합니다. 사용자 지정 도메인 바인딩을 위해 키 자격 증명 모음에서 사이트 HTTPS 인증서를 선택합니다.
1. Visual Studio에서 ‘서버’ 프로젝트의 앱 설정 파일(`appsettings.json` 또는 `appsettings.Production.json`)을 엽니다. Identity 서버 구성에서 다음 `Key` 섹션을 추가합니다. `Name` 키의 자체 서명된 인증서 **주체** 를 지정합니다. 다음 예제에서는 키 자격 증명 모음에 할당된 인증서의 일반 이름이 `IdentityServerSigning`이며 이 이름에서 `CN=IdentityServerSigning`의 **주체** 가 생성됩니다.

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. Visual Studio에서 ‘서버’ 프로젝트의 Azure App Service [게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)을 만듭니다. 메뉴 모음에서 다음을 선택합니다. **빌드** > **게시** > **새로 만들기** > **Azure** > **Azure App Service**(Windows 또는 Linux). Visual Studio가 Azure 구독에 연결된 경우 **리소스 종류** 별로 Azure 리소스의 **뷰** 를 설정할 수 있습니다. **웹앱** 목록 내에서 탐색하여 앱의 App Service를 찾고 선택합니다. **마침** 을 선택합니다.
1. Visual Studio가 **게시** 창으로 돌아가면 키 자격 증명 모음 및 SQL Server 데이터베이스 서비스 종속성이 자동으로 검색됩니다.

   키 자격 증명 모음 서비스에는 기본 설정에 대한 구성 변경이 필요하지 않습니다.

   테스트를 위해 기본적으로 Blazor 템플릿을 통해 구성되는 앱의 로컬 [SQLite](https://www.sqlite.org/index.html) 데이터베이스는 추가 구성 없이 앱과 함께 배포할 수 있습니다. 프로덕션 환경에서 Identity 서버에 대해 다른 데이터베이스를 구성하는 작업은 이 문서의 범위를 벗어납니다. 자세한 내용은 다음 설명서 세트의 데이터베이스 리소스를 참조하세요.
   * [App Service](/azure/app-service/)
   * [Identity서버](https://identityserver4.readthedocs.io/en/latest/)

1. 창 위쪽에 있는 배포 프로필 이름에서 **편집** 링크를 선택합니다. 대상 URL을 사이트의 사용자 지정 도메인 URL(예: `https://www.contoso.com`)로 변경합니다. 설정을 저장합니다.
1. 앱을 게시합니다. Visual Studio가 브라우저 창을 열고 해당 사용자 지정 도메인에서 사이트를 요청합니다.

Azure 설명서에는 A 레코드 대신 CNAME 레코드를 사용하는 방법에 관한 정보를 포함하여 App Service에서 TLS 바인딩과 함께 Azure 서비스 및 사용자 지정 도메인을 사용하는 방법에 관한 추가적인 세부 정보가 있습니다. 자세한 내용은 다음 자료를 참조하세요.

* [App Service 설명서](/azure/app-service/)
* [자습서: Azure App Service에 기존 사용자 지정 DNS 이름 매핑](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](/azure/app-service/configure-ssl-bindings)
* [Azure Key Vault](/azure/key-vault/)

Azure Portal에서 앱, 앱 구성 또는 Azure 서비스를 변경한 후 각 앱 테스트 실행을 위해 새로운 InPrivate 또는 incognito 브라우저 창을 사용하는 것이 좋습니다. 이전 테스트 실행에서 느린 cookie로 인해 사이트의 구성이 올바르더라도 사이트를 테스트할 때 인증 또는 권한 부여에 실패할 수 있습니다. 각 테스트 실행을 위해 새로운 InPrivate 또는 incognito 브라우저 창을 열도록 Visual Studio를 구성하는 방법에 관한 자세한 내용은 [Cookie 및 사이트 데이터](#cookies-and-site-data) 섹션을 참조하세요.

Azure Portal에서 App Service 구성이 변경되면 업데이트는 일반적으로 신속하게 적용되지만 즉시 적용되지는 않습니다. 경우에 따라 구성 변경 내용을 적용하려면 App Service가 다시 시작할 때까지 잠시 기다려야 합니다.

인증서 로드 문제를 해결하는 경우 Azure Portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell 명령 셸에서 다음 명령을 실행합니다. 명령은 앱이 `My` > `CurrentUser` 인증서 저장소에서 액세스할 수 있는 인증서 목록을 제공합니다. 출력에는 앱을 디버그할 때 유용한 인증서 주체 및 지문이 포함됩니다.

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 자료

* [Azure App Service에 배포](xref:security/authentication/identity/spa#deploy-to-production)
* [Key Vault에서 인증서 가져오기(Azure 설명서)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.
  * 전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.
  * 수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.
