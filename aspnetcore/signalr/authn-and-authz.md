---
title: ASP.NET Core의 인증 및 권한 부여 SignalR
author: bradygaster
description: ASP.NET Core에서 인증 및 권한 부여를 사용 하는 방법에 대해 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
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
uid: signalr/authn-and-authz
ms.openlocfilehash: e16efa59a82d0f3cb1a2272ae0c07654ebec6a51
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491563"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>ASP.NET Core의 인증 및 권한 부여 SignalR

[Andrew Stanton-간호사](https://twitter.com/anurse)

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/)[(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>허브에 연결 하는 사용자 인증 SignalR

SignalR[ASP.NET Core 인증과](xref:security/authentication/identity) 함께 사용 하 여 사용자를 각 연결에 연결할 수 있습니다. 허브에서는 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 속성을 사용 하 여 인증 데이터에 액세스할 수 있습니다. 인증을 통해 허브는 사용자와 연결 된 모든 연결에서 메서드를 호출할 수 있습니다. 자세한 내용은 [에서 SignalR 사용자 및 그룹 관리 ](xref:signalr/groups)를 참조 하세요. 단일 사용자에 게 여러 연결을 연결할 수 있습니다.

다음은 `Startup.Configure` 인증을 사용 하 고 ASP.NET Core 하는 예제입니다 SignalR .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> 및 ASP.NET Core 인증 미들웨어를 등록 하는 순서는 SignalR 중요 합니다. 에 사용자가 있는를 항상 호출 `UseAuthentication` `UseSignalR` SignalR `HttpContext` 합니다.

::: moniker-end

### <a name="no-loccookie-authentication"></a>Cookie 인증

브라우저 기반 앱에서 cookie 인증을 사용 하면 기존 사용자 자격 증명이 자동으로 연결로 이동 SignalR 됩니다. 브라우저 클라이언트를 사용 하는 경우 추가 구성이 필요 하지 않습니다. 사용자가 앱에 로그인 한 경우에는 SignalR 연결이 자동으로이 인증을 상속 합니다.

Cookies는 브라우저 별로 액세스 토큰을 보낼 수 있지만 브라우저가 아닌 클라이언트는이를 보낼 수 있습니다. [.Net 클라이언트](xref:signalr/dotnet-client)를 사용 하는 경우 `Cookies` 호출에서 속성을 구성 `.WithUrl` 하 여를 제공할 수 있습니다 cookie . 그러나 cookie .net 클라이언트에서 인증을 사용 하려면 앱이에 대 한 인증 데이터를 교환 하는 API를 제공 해야 합니다 cookie .

### <a name="bearer-token-authentication"></a>전달자 토큰 인증

클라이언트는를 사용 하는 대신 액세스 토큰을 제공할 수 있습니다 cookie . 서버는 토큰의 유효성을 검사하고 사용자를 확인하는 데 사용합니다. 이 유효성 검사는 연결이 설정 된 경우에만 수행 됩니다. 연결 수명이 지속 되는 동안 서버는 토큰 해지 검사를 위해 자동으로 유효성을 다시 검사 하지 않습니다.

JavaScript 클라이언트에서 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 옵션을 사용 하 여 토큰을 제공할 수 있습니다.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

.NET 클라이언트에는 토큰을 구성 하는 데 사용할 수 있는 유사한 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 속성이 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 사용자가 제공 하는 액세스 토큰 함수는에서 수행 하는 **모든** HTTP 요청 전에 호출 됩니다 SignalR . 연결 중에 만료 될 수 있으므로 연결을 활성 상태로 유지 하기 위해 토큰을 갱신 해야 하는 경우이 함수 내에서이를 수행 하 고 업데이트 된 토큰을 반환 합니다.

표준 웹 Api에서 전달자 토큰은 HTTP 헤더에 전송 됩니다. 그러나에서 SignalR 일부 전송을 사용할 때 브라우저에서 이러한 헤더를 설정할 수 없습니다. Websocket 및 Server-Sent 이벤트를 사용 하는 경우 토큰은 쿼리 문자열 매개 변수로 전송 됩니다. 

#### <a name="built-in-jwt-authentication"></a>기본 제공 JWT 인증

서버에서 전달자 토큰 인증은 [JWT 전달자 미들웨어](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)를 사용 하 여 구성 됩니다.

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> 쿼리 문자열은 브라우저 API 제한으로 인해 Websocket 및 Server-Sent 이벤트를 사용 하 여 연결할 때 브라우저에서 사용 됩니다. HTTPS를 사용 하는 경우 쿼리 문자열 값은 TLS 연결을 통해 보안이 유지 됩니다. 그러나 많은 서버에서 쿼리 문자열 값을 기록 합니다. 자세한 내용은 [ASP.NET Core SignalR 의 보안 고려 사항 ](xref:signalr/security)을 참조 하세요. SignalR 는 헤더를 사용 하 여 토큰 (예: .NET 및 Java 클라이언트)을 지 원하는 환경에서 토큰을 전송 합니다.

#### <a name="no-locidentity-server-jwt-authentication"></a>Identity 서버 JWT 인증

서버를 사용 하는 경우 Identity <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> 프로젝트에 서비스를 추가 합니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

`Startup.ConfigureServices`인증에 대 한 서비스 ( <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ) 및 Identity 서버 ()의 인증 처리기를 추가한 후에 서비스를 등록 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookies와 전달자 토큰 비교 

Cookie는 브라우저에만 적용 됩니다. 다른 종류의 클라이언트에서 전송 하면 전달자 토큰 전송과 비교 하 여 복잡성이 증가 합니다. 따라서 cookie 앱이 브라우저 클라이언트에서 사용자를 인증 해야 하는 경우를 제외 하 고는 인증을 권장 하지 않습니다. 전달자 토큰 인증은 브라우저 클라이언트 이외의 클라이언트를 사용 하는 경우 권장 되는 방법입니다.

### <a name="windows-authentication"></a>Windows 인증

앱에서 [Windows 인증](xref:security/authentication/windowsauth) 을 구성 하는 경우에서 SignalR 해당 id를 사용 하 여 허브를 보호할 수 있습니다. 그러나 메시지를 개별 사용자에 게 보내려면 사용자 지정 사용자 ID 공급자를 추가 해야 합니다. Windows 인증 시스템이 "이름 식별자" 클레임을 제공 하지 않습니다. SignalR 클레임을 사용 하 여 사용자 이름을 결정 합니다.

을 구현 하 `IUserIdProvider` 고 사용자가 식별자로 사용할 클레임 중 하나를 검색 하는 새 클래스를 추가 합니다. 예를 들어 "이름" 클레임 (형식의 Windows 사용자 이름)을 사용 하려면 `[Domain]\[Username]` 다음 클래스를 만듭니다.

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

대신 `ClaimTypes.Name` 의 모든 값 `User` (예: Windows SID 식별자 등)을 사용할 수 있습니다.

> [!NOTE]
> 사용자가 선택 하는 값은 시스템의 모든 사용자에서 고유 해야 합니다. 그렇지 않으면 한 사용자를 대상으로 하는 메시지가 다른 사용자로 이동 될 수 있습니다.

메서드에이 구성 요소를 등록 `Startup.ConfigureServices` 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

.NET 클라이언트에서는 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 속성을 설정 하 여 Windows 인증을 사용 하도록 설정 해야 합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows 인증은 Internet Explorer 및 Microsoft Edge에서 지원 되지만 일부 브라우저에서는 지원 되지 않습니다. 예를 들어 Chrome 및 Safari에서는 Windows 인증 및 Websocket을 사용 하려는 시도가 실패 합니다. Windows 인증이 실패 하면 클라이언트는 작동할 수 있는 다른 전송으로 대체 하려고 시도 합니다.

### <a name="use-claims-to-customize-identity-handling"></a>클레임을 사용 하 여 id 처리 사용자 지정

사용자를 인증 하는 앱은 SignalR 사용자 클레임에서 사용자 id를 파생할 수 있습니다. SignalR에서 사용자 id를 만드는 방법을 지정 하려면 `IUserIdProvider` 구현을 구현 하 고 등록 합니다.

샘플 코드는 클레임을 사용 하 여 사용자의 이메일 주소를 식별 속성으로 선택 하는 방법을 보여 줍니다. 

> [!NOTE]
> 사용자가 선택 하는 값은 시스템의 모든 사용자에서 고유 해야 합니다. 그렇지 않으면 한 사용자를 대상으로 하는 메시지가 다른 사용자로 이동 될 수 있습니다.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

계정 등록은 `ClaimsTypes.Email` ASP.NET id 데이터베이스에 형식의 클레임을 추가 합니다.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

에서이 구성 요소를 등록 `Startup.ConfigureServices` 합니다.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>사용자에 게 허브 및 허브 메서드에 액세스할 수 있는 권한 부여

기본적으로 허브의 모든 메서드는 인증 되지 않은 사용자가 호출할 수 있습니다. 인증을 요구 하려면 [권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 특성을 허브에 적용 합니다.

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

`[Authorize]` 특성의 생성자 인수와 속성을 사용하여 특정 [권한 부여 정책](xref:security/authorization/policies)과 일치하는 사용자로만 액세스를 제한할 수 있습니다. 예를 들어, 라는 사용자 지정 권한 부여 정책을 사용 하는 경우 `MyAuthorizationPolicy` 다음 코드를 사용 하 여 해당 정책과 일치 하는 사용자만 허브에 액세스할 수 있도록 할 수 있습니다.

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

개별 허브 방법에 `[Authorize]` 도 특성을 적용할 수 있습니다. 현재 사용자가 메서드에 적용 된 정책과 일치 하지 않으면 호출자에 게 오류가 반환 됩니다.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>권한 부여 처리기를 사용 하 여 허브 메서드 권한 부여 사용자 지정

SignalR 허브 메서드에 권한 부여가 필요할 때 권한 부여 처리기에 사용자 지정 리소스를 제공 합니다. 리소스가 `HubInvocationContext`의 인스턴스인 경우 에는 `HubInvocationContext` `HubCallerContext` , 호출 되는 허브 메서드의 이름 및 허브 메서드에 대 한 인수가 포함 되어 있습니다.

Azure Active Directory를 통해 여러 조직 로그인을 허용 하는 대화방의 예를 생각해 보세요. Microsoft 계정 있는 사용자는 채팅에 로그인 할 수 있지만 소유 조직의 구성원만 사용자를 금지 하거나 사용자의 채팅 기록을 볼 수 있습니다. 또한 특정 사용자의 특정 기능을 제한 해야 할 수도 있습니다. ASP.NET Core 3.0의 업데이트 된 기능을 사용 하는 것이 가능 합니다. 을 `DomainRestrictedRequirement` 사용자 지정으로 사용 하는 방법을 확인 `IAuthorizationRequirement` 합니다. 이제 `HubInvocationContext` 리소스 매개 변수가 전달 되 고 있으므로 내부 논리는 허브가 호출 되는 컨텍스트를 검사 하 고 사용자가 개별 허브 메서드를 실행할 수 있도록 하는 결정을 내릴 수 있습니다.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

에서 `Startup.ConfigureServices` `DomainRestrictedRequirement` 정책 만들기에 대 한 매개 변수로 사용자 지정 요구 사항을 제공 하 여 새 정책을 추가 합니다 `DomainRestricted` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

앞의 예제에서 클래스는 `DomainRestrictedRequirement` `IAuthorizationRequirement` 및 해당 `AuthorizationHandler` 요구 사항에 대 한 고유한 클래스입니다. 이러한 두 구성 요소를 별도의 클래스로 분할 하 여 문제를 구분할 수 있습니다. 예제의 방법의 장점은 요구 사항과 처리기가 동일 하기 때문에 시작 하는 동안를 주입할 필요가 없다는 `AuthorizationHandler` 점입니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core에서 전달자 토큰 인증](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [리소스 기반 권한 부여](xref:security/authorization/resourcebased)
