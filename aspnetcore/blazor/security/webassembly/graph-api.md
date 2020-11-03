---
title: ASP.NET Core Blazor WebAssembly에서 Graph API 사용
author: guardrex
description: Blazor WebAssemlby 앱에서 Graph API를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 3c77a8b39562c0145d1441cfdfe1dcf57aa3a613
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92692073"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly에서 Graph API 사용

작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

[Microsoft Graph API](/graph/use-the-api)는 Blazor 및 기타 .NET Framework 앱이 Microsoft Cloud Service 리소스에 액세스할 수 있도록 하는 RESTful 웹 API입니다.

## <a name="graph-sdk"></a>Graph SDK

[Microsoft Graph SDK](/graph/sdks/sdks-overview)는 Microsoft Graph에 액세스하는 고품질의 효율적이고 복원력 있는 애플리케이션 구축을 간소화하도록 설계되었습니다.

이 섹션의 예제에서는 독립 실행형의 프로젝트 파일이나 *`Client`* 앱의 프로젝트 파일에서 다음 패키지에 대한 패키지 참조가 필요합니다.

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

이 문서의 다음 각 하위 섹션에서 사용되는 유틸리티 클래스와 구성은 다음과 같습니다.

* [Graph SDK를 사용하는 구성 요소에서 Graph API 호출](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Graph SDK를 사용하여 사용자 클레임 사용자 지정](#customize-user-claims-with-the-graph-sdk)

Azure Portal의 AAD 영역에 Microsoft Graph API 범위를 추가한 후 다음을 수행합니다.

* 독립 실행형 앱 또는 호스트된 Blazor 솔루션의 *`Client`* 앱에 다음 `GraphClientExtensions.cs` 클래스를 추가합니다.
* `AuthenticateRequestAsync` 메서드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions>의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> 속성에 필요한 범위를 제공합니다. 다음 예제에서는 이 문서의 이후 섹션에 나오는 예제와 일치하도록 `User.Read` 범위를 지정합니다.

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

        public IAccessTokenProvider Provider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await Provider.RequestAccessToken(
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
        private readonly HttpClient client;

        public HttpClientHttpProvider(HttpClient client)
        {
            this.client = client;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return client.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return client.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

위 코드의 `{STRING ARRAY OF SCOPES}` 자리 표시자는 허용되는 범위의 문자열 배열입니다. 예를 들어 이 문서의 다음 섹션에 있는 예제의 `User.Read` 범위로 `Scopes`를 설정합니다.

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

`Program.Main`(`Program.cs`)에서 `AddGraphClient` 확장 메서드를 사용하여 Graph 클라이언트 서비스 및 구성을 추가합니다.

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

위 코드의 `{STRING ARRAY OF SCOPES}` 자리 표시자는 허용되는 범위의 문자열 배열입니다. 예를 들어 이 문서의 다음 섹션에 있는 예제의 `AddGraphClient`로 `User.Read` 범위를 전달합니다.

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Graph SDK를 사용하는 구성 요소에서 Graph API 호출

이 섹션에서는 이 문서의 앞부분에서 설명한 [유틸리티 클래스(`GraphClientExtensions.cs`)](#graph-sdk)를 사용합니다. 다음 `GraphExample` 구성 요소는 삽입된 `GraphServiceClient`를 사용하여 사용자의 AAD 프로필 데이터를 가져오고 휴대폰 번호를 표시합니다.

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

### <a name="customize-user-claims-with-the-graph-sdk"></a>Graph SDK를 사용하여 사용자 클레임 사용자 지정

이 섹션에서는 이 문서의 앞부분에서 설명한 [유틸리티 클래스(`GraphClientExtensions.cs`)](#graph-sdk)를 사용합니다.

다음 예제에서 앱은 AAD 사용자 프로필의 휴대폰 번호에서 사용자에 대한 휴대폰 번호 클레임을 만듭니다. 앱에는 AAD에 구성된 `User.Read` Graph API 범위가 있어야 합니다.

다음 사용자 지정 사용자 계정 팩터리에서 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>는 사용자의 계정을 나타냅니다. 앱에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스가 필요한 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.

`CustomAccountFactory.cs`:

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

`Program.Main`(`Program.cs`)에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다. 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스를 사용하는 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.

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

## <a name="named-client-with-graph-api"></a>Graph API를 사용하여 명명된 클라이언트

이 섹션의 예제에서는 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 통화를 처리할 사용자의 휴대폰 번호를 가져옵니다.

이 섹션의 예제에서는 독립 실행형의 프로젝트 파일이나 *`Client`* 앱의 프로젝트 파일에서 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)에 대한 패키지 참조가 필요합니다.

Graph API 작업을 위해 다음 클래스 및 프로젝트 구성을 만듭니다. 이 문서의 다음 각 하위 섹션에서 사용되는 클래스와 구성은 다음과 같습니다.

* [구성 요소에서 Graph API 호출](#call-graph-api-from-a-component)
* [Graph API 및 명명된 클라이언트를 사용하여 사용자 클레임 사용자 지정](#customize-user-claims-with-graph-api-and-a-named-client)

Azure Portal의 AAD 영역에 Microsoft Graph API 범위를 추가한 후 Graph API에 대해 구성된 앱의 처리기에 필요한 범위를 제공합니다. 다음 예제에서는 `User.Read` 범위에 대한 처리기를 구성합니다. 추가 범위를 추가할 수 있습니다.

`GraphAuthorizationMessageHandler.cs`:

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

`Program.Main`(`Program.cs`)에서 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 구성합니다.

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>구성 요소에서 Graph API 호출

이 섹션에서는 이 문서의 앞부분에서 설명한 [앱의 Graph 권한 부여 메시지 처리기(`GraphAuthorizationMessageHandler.cs`) 및 `Program.Main` 추가 기능](#named-client-with-graph-api)을 사용하여 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 제공합니다.

Razor 구성 요소에서:

* Graph API에 대한 <xref:System.Net.Http.HttpClient>를 만들고 사용자의 프로필 데이터에 대한 요청을 실행합니다.
* `UserInfo.cs` 클래스는 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 특성과 해당 속성에 대해 AAD에서 사용하는 JSON 이름을 사용하여 필요한 사용자 프로필 속성을 지정합니다.

`Pages/CallUser.razor`:

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
> 위의 예제에서 개발자는 사용자 지정 `ICallProcessor`(`CallProcessor`)를 구현하여 큐에 넣은 다음 자동화된 호출을 수행합니다.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Graph API 및 명명된 클라이언트를 사용하여 사용자 클레임 사용자 지정

이 섹션에서는 이 문서의 앞부분에서 설명한 [앱의 Graph 권한 부여 메시지 처리기(`GraphAuthorizationMessageHandler.cs`) 및 `Program.Main` 추가 기능](#named-client-with-graph-api)을 사용하여 Graph API에 대해 명명된 <xref:System.Net.Http.HttpClient>를 제공합니다.

다음 예제에서 앱은 AAD 사용자 프로필의 휴대폰 번호에서 사용자에 대한 휴대폰 번호 클레임을 만듭니다. 앱에는 AAD에 구성된 `User.Read` Graph API 범위가 있어야 합니다.

`UserInfo.cs` 클래스를 앱에 추가하고 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 특성 및 해당 속성에 대해 AAD에서 사용하는 JSON 이름을 사용하여 필요한 사용자 프로필 속성을 지정합니다.

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

다음 사용자 지정 사용자 계정 팩터리에서 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>는 사용자의 계정을 나타냅니다. 앱에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스가 필요한 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.

`CustomAccountFactory.cs`:

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

`Program.Main`(`Program.cs`)에서 사용자 지정 사용자 계정 팩터리를 사용하도록 MSAL 인증을 구성합니다. 앱에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장하는 사용자 지정 사용자 계정 클래스를 사용하는 경우 다음 코드에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>에 대한 사용자 지정 사용자 계정 클래스를 바꿉니다.

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

위의 예제는 MSAL에서 AAD 인증을 사용하는 앱에 대한 것입니다. OIDC 및 API 인증에 유사한 패턴이 있습니다. 자세한 내용은 [페이로드 클레임으로 사용자의 사용자 지정](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) 섹션에 있는 예제를 참조하세요.
