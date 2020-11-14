---
title: ASP.NET Core Blazor WebAssembly에서 Graph API 사용
author: guardrex
description: 'Blazor WebAssemlby 앱에서 Graph API를 사용하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 569a88630f7b75e866d8ecda99605ebe3bc58db8
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234433"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="f1910-103">ASP.NET Core Blazor WebAssembly에서 Graph API 사용</span><span class="sxs-lookup"><span data-stu-id="f1910-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="f1910-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f1910-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f1910-105">[Microsoft Graph API](/graph/use-the-api)는 Blazor 및 기타 .NET Framework 앱이 Microsoft Cloud Service 리소스에 액세스할 수 있도록 하는 RESTful 웹 API입니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-105">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="f1910-106">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="f1910-106">Graph SDK</span></span>

<span data-ttu-id="f1910-107">[Microsoft Graph SDK](/graph/sdks/sdks-overview)는 Microsoft Graph에 액세스하는 고품질의 효율적이고 복원력 있는 애플리케이션 구축을 간소화하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="f1910-108">이 섹션의 예제에서는 독립 실행형의 프로젝트 파일이나 *`Client`* 앱의 프로젝트 파일에서 다음 패키지에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-108">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="f1910-109">이 문서의 다음 각 하위 섹션에서 사용되는 유틸리티 클래스와 구성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-109">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="f1910-110">Graph SDK를 사용하는 구성 요소에서 Graph API 호출</span><span class="sxs-lookup"><span data-stu-id="f1910-110">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="f1910-111">Graph SDK를 사용하여 사용자 클레임 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="f1910-111">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="f1910-112">Azure Portal의 AAD 영역에 Microsoft Graph API 범위를 추가한 후 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-112">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="f1910-113">독립 실행형 앱 또는 호스트된 Blazor 솔루션의 *`Client`* 앱에 다음 `GraphClientExtensions.cs` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-113">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="f1910-114">`AuthenticateRequestAsync` 메서드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions>의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> 속성에 필요한 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-114">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="f1910-115">다음 예제에서는 이 문서의 이후 섹션에 나오는 예제와 일치하도록 `User.Read` 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-115">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="f1910-116">위 코드의 `{STRING ARRAY OF SCOPES}` 자리 표시자는 허용되는 범위의 문자열 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-116">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="f1910-117">예를 들어 이 문서의 다음 섹션에 있는 예제의 `User.Read` 범위로 `Scopes`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-117">For example, set `Scopes` to the `User.Read` scope for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="f1910-118">`Program.Main`(`Program.cs`)에서 `AddGraphClient` 확장 메서드를 사용하여 Graph 클라이언트 서비스 및 구성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-118">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

<span data-ttu-id="f1910-119">위 코드의 `{STRING ARRAY OF SCOPES}` 자리 표시자는 허용되는 범위의 문자열 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-119">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="f1910-120">예를 들어 이 문서의 다음 섹션에 있는 예제의 `AddGraphClient`로 `User.Read` 범위를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-120">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="f1910-121">Graph SDK를 사용하는 구성 요소에서 Graph API 호출</span><span class="sxs-lookup"><span data-stu-id="f1910-121">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="f1910-122">이 섹션에서는 이 문서의 앞부분에서 설명한 [유틸리티 클래스(`GraphClientExtensions.cs`)](#graph-sdk)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-122">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="f1910-123">다음 `GraphExample` 구성 요소는 삽입된 `GraphServiceClient`를 사용하여 사용자의 AAD 프로필 데이터를 가져오고 휴대폰 번호를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-123">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="f1910-124">Graph SDK를 사용하여 사용자 클레임 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="f1910-124">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="f1910-125">이 섹션에서는 이 문서의 앞부분에서 설명한 [유틸리티 클래스(`GraphClientExtensions.cs`)](#graph-sdk)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-125">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="f1910-126">다음 예제에서 앱은 AAD 사용자 프로필의 휴대폰 번호에서 사용자에 대한 휴대폰 번호 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-126">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="f1910-127">앱에는 AAD에 구성된 `User.Read` Graph API 범위가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-127">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="f1910-128">다음 사용자 지정 사용자 계정 팩터리에서 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>는 사용자의 계정을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-128">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="f1910-129">앱에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스가 필요한 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-129">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="f1910-130">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f1910-130">`CustomAccountFactory.cs`:</span></span>

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
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
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
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
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

<span data-ttu-id="f1910-131">`Program.Main`(`Program.cs`)에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다. 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스를 사용하는 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-131">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a><span data-ttu-id="f1910-132">Graph API를 사용하여 명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="f1910-132">Named client with Graph API</span></span>

<span data-ttu-id="f1910-133">이 섹션의 예제에서는 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 통화를 처리할 사용자의 휴대폰 번호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-133">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="f1910-134">이 섹션의 예제에서는 독립 실행형의 프로젝트 파일이나 *`Client`* 앱의 프로젝트 파일에서 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-134">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="f1910-135">Graph API 작업을 위해 다음 클래스 및 프로젝트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-135">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="f1910-136">이 문서의 다음 각 하위 섹션에서 사용되는 클래스와 구성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-136">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="f1910-137">구성 요소에서 Graph API 호출</span><span class="sxs-lookup"><span data-stu-id="f1910-137">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="f1910-138">Graph API 및 명명된 클라이언트를 사용하여 사용자 클레임 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="f1910-138">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="f1910-139">Azure Portal의 AAD 영역에 Microsoft Graph API 범위를 추가한 후 Graph API에 대해 구성된 앱의 처리기에 필요한 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-139">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="f1910-140">다음 예제에서는 `User.Read` 범위에 대한 처리기를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-140">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="f1910-141">추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-141">Additional scopes can be added.</span></span>

<span data-ttu-id="f1910-142">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="f1910-142">`GraphAuthorizationMessageHandler.cs`:</span></span>

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
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="f1910-143">`Program.Main`(`Program.cs`)에서 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-143">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="f1910-144">구성 요소에서 Graph API 호출</span><span class="sxs-lookup"><span data-stu-id="f1910-144">Call Graph API from a component</span></span>

<span data-ttu-id="f1910-145">이 섹션에서는 이 문서의 앞부분에서 설명한 [앱의 Graph 권한 부여 메시지 처리기(`GraphAuthorizationMessageHandler.cs`) 및 `Program.Main` 추가 기능](#named-client-with-graph-api)을 사용하여 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-145">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="f1910-146">Razor 구성 요소에서:</span><span class="sxs-lookup"><span data-stu-id="f1910-146">In a Razor component:</span></span>

* <span data-ttu-id="f1910-147">Graph API에 대한 <xref:System.Net.Http.HttpClient>를 만들고 사용자의 프로필 데이터에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-147">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="f1910-148">`UserInfo.cs` 클래스는 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 특성과 해당 속성에 대해 AAD에서 사용하는 JSON 이름을 사용하여 필요한 사용자 프로필 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-148">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="f1910-149">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="f1910-149">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="f1910-150">위의 예제에서 개발자는 사용자 지정 `ICallProcessor`(`CallProcessor`)를 구현하여 큐에 넣은 다음 자동화된 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-150">In the preceding example, the developer implements the custom `ICallProcessor` (`CallProcessor`) to queue and then place automated calls.</span></span>

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="f1910-151">Graph API 및 명명된 클라이언트를 사용하여 사용자 클레임 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="f1910-151">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="f1910-152">이 섹션에서는 이 문서의 앞부분에서 설명한 [앱의 Graph 권한 부여 메시지 처리기(`GraphAuthorizationMessageHandler.cs`) 및 `Program.Main` 추가 기능](#named-client-with-graph-api)을 사용하여 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-152">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="f1910-153">다음 예제에서 앱은 AAD 사용자 프로필의 휴대폰 번호에서 사용자에 대한 휴대폰 번호 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-153">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="f1910-154">앱에는 AAD에 구성된 `User.Read` Graph API 범위가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-154">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="f1910-155">`UserInfo.cs` 클래스를 앱에 추가하고 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 특성 및 해당 속성에 대해 AAD에서 사용하는 JSON 이름을 사용하여 필요한 사용자 프로필 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-155">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="f1910-156">다음 사용자 지정 사용자 계정 팩터리에서 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>는 사용자의 계정을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-156">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="f1910-157">앱에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스가 필요한 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-157">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="f1910-158">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f1910-158">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="f1910-159">`Program.Main`(`Program.cs`)에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다. 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스를 사용하는 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-159">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="f1910-160">위의 예제는 MSAL에서 AAD 인증을 사용하는 앱에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-160">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="f1910-161">OIDC 및 API 인증에 유사한 패턴이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1910-161">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="f1910-162">자세한 내용은 [페이로드 클레임으로 사용자의 사용자 지정](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) 섹션에 있는 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1910-162">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>