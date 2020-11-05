---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: 추가 보안 시나리오'
author: guardrex
description: 추가 보안 시나리오에 대해 :::no-loc(Blazor Server):::를 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234440"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="954fa-103">ASP.NET Core :::no-loc(Blazor Server)::: 추가 보안 시나리오</span><span class="sxs-lookup"><span data-stu-id="954fa-103">ASP.NET Core :::no-loc(Blazor Server)::: additional security scenarios</span></span>

<span data-ttu-id="954fa-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="954fa-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="954fa-105">:::no-loc(Blazor Server)::: 앱으로 토큰 전달</span><span class="sxs-lookup"><span data-stu-id="954fa-105">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="954fa-106">:::no-loc(Blazor Server)::: 앱의 :::no-loc(Razor)::: 구성 요소 외부에서 사용할 수 있는 토큰은 이 섹션에서 설명하는 방법을 통해 구성 요소로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-106">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="954fa-107">일반 :::no-loc(Razor)::: Pages나 MVC 앱을 인증하는 것처럼 :::no-loc(Blazor Server)::: 앱을 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-107">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="954fa-108">토큰을 프로비저닝하고 인증 :::no-loc(cookie):::에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-108">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="954fa-109">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="954fa-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="954fa-110">필요에 따라 `options.Scope.Add("{SCOPE}");`를 사용하여 추가 범위가 추가됩니다. 여기서 자리 표시자 `{SCOPE}`는 추가할 추가 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="954fa-111">:::no-loc(Blazor)::: 앱 내에서 [DI(종속성 주입)](xref:blazor/fundamentals/dependency-injection)로부터 토큰을 확인하는 데 사용할 수 있는 **범위가 지정된** 토큰 공급자 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-111">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="954fa-112">`Startup.ConfigureServices`에서 다음 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="954fa-113">액세스 토큰 및 새로 고침 토큰과 함께 초기 앱 상태를 전달하는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="954fa-114">`_Host.cshtml` 파일에서 `InitialApplicationState`의 인스턴스를 만들어 앱에 매개 변수로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="954fa-115">`App` 구성 요소(`App.razor`)에서 서비스를 확인하고 매개 변수로 받은 데이터를 사용하여 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="954fa-116">[`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet 패키지용 앱에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="954fa-117">보안 API 요청을 만드는 서비스에서 토큰 공급자를 삽입하고 API 요청의 토큰을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="954fa-118">인증 체계 설정</span><span class="sxs-lookup"><span data-stu-id="954fa-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="954fa-119">여러 인증 미들웨어를 사용하여 인증 체계가 두 개 이상인 앱의 경우 :::no-loc(Blazor):::가 사용하는 체계를 `Startup.Configure`의 엔드포인트 구성에서 명시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="954fa-120">다음 예제에서는 Azure Active Directory 체계를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="954fa-121">:::no-loc(Blazor Server)::: 앱으로 토큰 전달</span><span class="sxs-lookup"><span data-stu-id="954fa-121">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="954fa-122">:::no-loc(Blazor Server)::: 앱의 :::no-loc(Razor)::: 구성 요소 외부에서 사용할 수 있는 토큰은 이 섹션에서 설명하는 방법을 통해 구성 요소로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-122">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="954fa-123">일반 :::no-loc(Razor)::: Pages나 MVC 앱을 인증하는 것처럼 :::no-loc(Blazor Server)::: 앱을 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-123">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="954fa-124">토큰을 프로비저닝하고 인증 :::no-loc(cookie):::에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-124">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="954fa-125">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="954fa-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="954fa-126">필요에 따라 `options.Scope.Add("{SCOPE}");`를 사용하여 추가 범위가 추가됩니다. 여기서 자리 표시자 `{SCOPE}`는 추가할 추가 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="954fa-127">필요에 따라 리소스는 `options.Resource = "{RESOURCE}";`를 사용하여 지정됩니다. 여기서 자리 표시자 `{RESOURCE}`는 리소스입니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="954fa-128">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="954fa-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="954fa-129">액세스 토큰 및 새로 고침 토큰과 함께 초기 앱 상태를 전달하는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="954fa-130">:::no-loc(Blazor)::: 앱 내에서 [DI(종속성 주입)](xref:blazor/fundamentals/dependency-injection)로부터 토큰을 확인하는 데 사용할 수 있는 **범위가 지정된** 토큰 공급자 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-130">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="954fa-131">`Startup.ConfigureServices`에서 다음 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="954fa-132">`_Host.cshtml` 파일에서 `InitialApplicationState`의 인스턴스를 만들어 앱에 매개 변수로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="954fa-133">`App` 구성 요소(`App.razor`)에서 서비스를 확인하고 매개 변수로 받은 데이터를 사용하여 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="954fa-134">[`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet 패키지용 앱에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="954fa-135">보안 API 요청을 만드는 서비스에서 토큰 공급자를 삽입하고 API 요청의 토큰을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="954fa-136">인증 체계 설정</span><span class="sxs-lookup"><span data-stu-id="954fa-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="954fa-137">여러 인증 미들웨어를 사용하여 인증 체계가 두 개 이상인 앱의 경우 :::no-loc(Blazor):::가 사용하는 체계를 `Startup.Configure`의 엔드포인트 구성에서 명시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="954fa-138">다음 예제에서는 Azure Active Directory 체계를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="954fa-139">OIDC(OpenID Connect) v2.0 엔드포인트 사용</span><span class="sxs-lookup"><span data-stu-id="954fa-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="954fa-140">5\.0 이전 버전의 ASP.NET Core에서 인증 라이브러리 및 :::no-loc(Blazor)::: 템플릿은 OIDC(OpenID Connect) v1.0 엔드포인트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-140">In versions of ASP.NET Core prior to 5.0, the authentication library and :::no-loc(Blazor)::: templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="954fa-141">5\.0 이전 버전의 ASP.NET Core에서 v2.0 엔드포인트를 사용하려면 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>에서 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="954fa-142">또는 앱 설정(`:::no-loc(appsettings.json):::`) 파일에서 설정을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-142">Alternatively, the setting can be made in the app settings (`:::no-loc(appsettings.json):::`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="954fa-143">인증 기관에 대한 세그먼트의 추적이 앱의 OIDC 공급자에 적합하지 않은 경우(예: 비 AAD 공급자) <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 속성을 직접 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="954fa-144"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 또는 앱 설정 파일에서 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 키를 사용하여 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="954fa-145">코드 변경 내용</span><span class="sxs-lookup"><span data-stu-id="954fa-145">Code changes</span></span>

* <span data-ttu-id="954fa-146">ID 토큰의 클레임 목록은 v2.0 엔드포인트의 경우 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="954fa-147">자세한 내용은 Azure 설명서에서 [Microsoft ID 플랫폼(v2.0)으로 업데이트하는 이유](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)를</span><span class="sxs-lookup"><span data-stu-id="954fa-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="954fa-148">참조하세요.</span><span class="sxs-lookup"><span data-stu-id="954fa-148">in the Azure documentation.</span></span>
* <span data-ttu-id="954fa-149">v2.0 엔드포인트의 경우 범위 URI에 리소스가 지정되어 있으므로 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>에서 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 속성 설정을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="954fa-150">자세한 내용은 Azure 설명서의 [리소스가 아닌 범위](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="954fa-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="954fa-151">앱 ID URI</span><span class="sxs-lookup"><span data-stu-id="954fa-151">App ID URI</span></span>

* <span data-ttu-id="954fa-152">v2.0 엔드포인트를 사용할 경우 API는 API의 고유 식별자를 나타내는 *`App ID URI`* 를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-152">When using v2.0 endpoints, APIs define an *`App ID URI`* , which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="954fa-153">모든 범위에는 앱 ID URI가 접두어로 포함되며 v2.0 엔드포인트는 앱 ID URI를 대상으로 하는 액세스 토큰을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="954fa-154">V2.0 엔드포인트를 사용할 경우 서버 API에 구성된 클라이언트 ID가 API 애플리케이션 ID(클라이언트 ID)에서 앱 ID URI로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="954fa-155">`:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="954fa-155">`:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="954fa-156">OIDC 공급자 앱 등록 설명에서 사용해야 하는 앱 ID URI를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="954fa-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
