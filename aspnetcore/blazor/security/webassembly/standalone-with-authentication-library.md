---
title: 인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: 인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱을 보호하는 방법을 알아봅니다.
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 3da9ea045de996602ead052f6f13ffc999273a50
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252489"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="43ead-103">인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="43ead-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="43ead-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43ead-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="43ead-105">‘AAD(Azure Active Directory) 및 AAD B2C(Azure Active Directory B2C)의 경우에는 이 항목의 지침을 따르지 않습니다. 목차 노드 테이블에서 AAD 및 AAD B2C 항목을 참조하세요.’</span><span class="sxs-lookup"><span data-stu-id="43ead-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="43ead-106">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리를 사용하는 [독립 실행형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)을 만들려면 선택한 도구에 대한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="43ead-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

> [!NOTE]
> <span data-ttu-id="43ead-107">IP(Identity 공급자)는 [OIDC(OpenID Connect)](https://openid.net/connect/)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="43ead-108">예를 들어, Facebook의 IP는 OIDC 규격 공급자가 아니므로 Facebook IP에는 이 항목의 지침이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="43ead-109">자세한 내용은 <xref:blazor/security/webassembly/index#authentication-library>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43ead-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43ead-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43ead-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43ead-111">인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="43ead-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="43ead-112">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor WebAssembly 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="43ead-113">**개별 사용자 계정** 을 **사용자 계정 앱 내 저장** 옵션과 함께 선택하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="43ead-114">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43ead-114">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="43ead-115">인증 메커니즘을 사용하여 빈 폴더에 새 Blazor WebAssembly 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-115">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="43ead-116">ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하여 앱 내에 사용자를 저장하려면 `-au|--auth` 옵션을 사용하여 `Individual` 인증 메커니즘을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-116">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="43ead-117">자리표시자</span><span class="sxs-lookup"><span data-stu-id="43ead-117">Placeholder</span></span>  | <span data-ttu-id="43ead-118">예제</span><span class="sxs-lookup"><span data-stu-id="43ead-118">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="43ead-119">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-119">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="43ead-120">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43ead-120">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="43ead-121">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43ead-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="43ead-122">인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="43ead-122">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="43ead-123">**새 Blazor WebAssembly 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-123">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="43ead-124">ASP.NET Core [Identity](xref:security/authentication/identity)를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-124">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="43ead-125">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="43ead-125">Authentication package</span></span>

<span data-ttu-id="43ead-126">앱이 개별 사용자 계정을 사용하도록 만들어진 경우 해당 앱은 앱의 프로젝트 파일에서 자동으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-126">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="43ead-127">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="43ead-128">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="43ead-129">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-129">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="43ead-130">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="43ead-130">Authentication service support</span></span>

<span data-ttu-id="43ead-131">사용자 인증에 대한 지원은 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-131">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="43ead-132">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-132">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="43ead-133">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="43ead-133">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="43ead-134">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-134">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="43ead-135">Google OAuth 2.0 OIDC 예제:</span><span class="sxs-lookup"><span data-stu-id="43ead-135">Google OAuth 2.0 OIDC example:</span></span>

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

<span data-ttu-id="43ead-136">리디렉션 URI(`https://localhost:5001/authentication/login-callback`)는 **자격 증명** >  **`{NAME}`**  > **권한 있는 리디렉션 URI** 의 [Google API 콘솔](https://console.developers.google.com/apis/dashboard)에 등록됩니다. 여기서 `{NAME}`은 Google API 콘솔의 **OAuth 2.0 클라이언트 ID** 앱 목록에 있는 앱의 클라이언트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-136">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="43ead-137">독립 실행형 앱에 대한 인증 지원은 OIDC(OpenID Connect)를 사용하여 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-137">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="43ead-138"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 메서드는 콜백을 받아서 OIDC를 사용하여 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-138">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="43ead-139">앱을 구성하는 데 필요한 값은 OIDC 규격 IP에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-139">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="43ead-140">앱을 등록할 때 값을 확인하세요. 앱 등록은 보통 온라인 포털에서 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-140">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="43ead-141">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="43ead-141">Access token scopes</span></span>

<span data-ttu-id="43ead-142">Blazor WebAssembly 템플릿은 `openid` 및 `profile`의 기본 범위를 자동으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-142">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="43ead-143">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-143">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="43ead-144">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>의 기본 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-144">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="43ead-145">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43ead-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="43ead-146">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="43ead-146">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="43ead-147">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="43ead-147">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="43ead-148">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="43ead-148">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="43ead-149">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="43ead-149">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="43ead-150">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="43ead-150">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="43ead-151">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="43ead-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="43ead-152">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="43ead-152">LoginDisplay component</span></span>

<span data-ttu-id="43ead-153">`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-153">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="43ead-154">인증된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="43ead-154">For authenticated users:</span></span>
  * <span data-ttu-id="43ead-155">현재 사용자 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-155">Displays the current username.</span></span>
  * <span data-ttu-id="43ead-156">앱에서 로그아웃하는 단추를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-156">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="43ead-157">익명 사용자의 경우, 로그인 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-157">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="43ead-158">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="43ead-158">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="43ead-159">추가 자료</span><span class="sxs-lookup"><span data-stu-id="43ead-159">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="43ead-160">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="43ead-160">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="43ead-161"><xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="43ead-161"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="43ead-162">전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.</span><span class="sxs-lookup"><span data-stu-id="43ead-162">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="43ead-163">수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.</span><span class="sxs-lookup"><span data-stu-id="43ead-163">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
