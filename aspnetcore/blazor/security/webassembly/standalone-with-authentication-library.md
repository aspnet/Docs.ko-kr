---
title: 인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: 인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280886"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호

‘AAD(Azure Active Directory) 및 AAD B2C(Azure Active Directory B2C)의 경우에는 이 항목의 지침을 따르지 않습니다. 목차 노드 테이블에서 AAD 및 AAD B2C 항목을 참조하세요.’

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리를 사용하는 [독립 실행형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)을 만들려면 선택한 도구에 대한 지침을 따르세요. 인증에 대한 지원을 추가하려면 이 문서의 다음 섹션에서 앱 설정 및 구성에 대한 지침을 참조하세요.

> [!NOTE]
> IP(Identity 공급자)는 [OIDC(OpenID Connect)](https://openid.net/connect/)를 사용해야 합니다. 예를 들어, Facebook의 IP는 OIDC 규격 공급자가 아니므로 Facebook IP에는 이 항목의 지침이 적용되지 않습니다. 자세한 내용은 <xref:blazor/security/webassembly/index#authentication-library>를 참조하세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면

1. **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor WebAssembly 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.

1. ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하려면 **사용자 계정 앱 내 저장** 옵션과 함께 **개별 사용자 계정** 을 선택합니다. 이렇게 선택하면 인증 지원이 추가되고 사용자가 데이터베이스에 저장되지 않습니다. 이 문서의 다음 섹션에서는 추가 세부 정보를 제공합니다.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

인증 메커니즘을 사용하여 빈 폴더에 새 Blazor WebAssembly 프로젝트를 만듭니다. ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하려면 `-au|--auth` 옵션과 함께 `Individual` 인증 메커니즘을 지정합니다. 이렇게 선택하면 인증 지원이 추가되고 사용자가 데이터베이스에 저장되지 않습니다. 이 문서의 다음 섹션에서는 추가 세부 정보를 제공합니다.

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| 자리표시자  | 예제        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.

자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면

1. **새 Blazor WebAssembly 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.

1. 앱이 ASP.NET Core [Identity](xref:security/authentication/identity)를 사용하도록 만들어지고 사용자가 데이터베이스에 저장되지 않습니다. 이 문서의 다음 섹션에서는 추가 세부 정보를 제공합니다.

---

## <a name="authentication-package"></a>인증 패키지

앱이 개별 사용자 계정을 사용하도록 만들어진 경우 해당 앱은 앱의 프로젝트 파일에서 자동으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지에 대한 패키지 참조를 받습니다. 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.

앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)의 패키지 **버전 기록** 에서 찾을 수 있습니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대한 지원은 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.

새 앱인 경우 다음 구성에서 `{AUTHORITY}` 및 `{CLIENT ID}` 자리 표시자에 대한 값을 제공합니다. 앱의 IP에서 사용하는 데 필요한 기타 구성 값을 제공합니다. 예제는 Google에 대한 것이며 `PostLogoutRedirectUri`, `RedirectUri` 및 `ResponseType`이 필요합니다. 앱에 인증을 추가하려면 자리 표시자에 대한 값과 기타 구성 값을 사용하여 다음 코드 및 구성을 앱에 수동으로 추가합니다.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Google OAuth 2.0 OIDC 예제:

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

리디렉션 URI(`https://localhost:5001/authentication/login-callback`)는 **자격 증명** >  **`{NAME}`**  > **권한 있는 리디렉션 URI** 의 [Google API 콘솔](https://console.developers.google.com/apis/dashboard)에 등록됩니다. 여기서 `{NAME}`은 Google API 콘솔의 **OAuth 2.0 클라이언트 ID** 앱 목록에 있는 앱의 클라이언트 이름입니다.

독립 실행형 앱에 대한 인증 지원은 OIDC(OpenID Connect)를 사용하여 제공됩니다. <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 메서드는 콜백을 받아서 OIDC를 사용하여 앱을 인증하는 데 필요한 매개 변수를 구성합니다. 앱을 구성하는 데 필요한 값은 OIDC 규격 IP에서 얻을 수 있습니다. 앱을 등록할 때 값을 확인하세요. 앱 등록은 보통 온라인 포털에서 진행됩니다.

## <a name="access-token-scopes"></a>액세스 토큰 범위

Blazor WebAssembly 템플릿은 `openid` 및 `profile`의 기본 범위를 자동으로 구성합니다.

Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다. 로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>의 기본 토큰 범위에 범위를 추가합니다. 앱에 인증을 추가하려면 다음 코드를 수동으로 추가하고 범위 URI를 구성합니다.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.

* [추가 액세스 토큰 요청](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Imports 파일

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>App 구성 요소

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.

* 인증된 사용자의 경우:
  * 현재 사용자 이름을 표시합니다.
  * 앱에서 로그아웃하는 단추를 제공합니다.
* 익명 사용자의 경우, 로그인 옵션을 제공합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a>Authentication 구성 요소

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/security/webassembly/additional-scenarios>
* [보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.
  * 전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.
  * 수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.
