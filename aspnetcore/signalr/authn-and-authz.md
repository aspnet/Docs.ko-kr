---
title: 'ASP.NET Core의 인증 및 권한 부여 :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core에서 인증 및 권한 부여를 사용 하는 방법에 대해 알아봅니다 :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
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
uid: signalr/authn-and-authz
ms.openlocfilehash: 0e220d72fe9ef4ada402b449ef20e31324f7bcd2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060120"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="55ab7-103">ASP.NET Core의 인증 및 권한 부여 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="55ab7-103">Authentication and authorization in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="55ab7-104">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="55ab7-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="55ab7-105">[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/)[(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="55ab7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a><span data-ttu-id="55ab7-106">허브에 연결 하는 사용자 인증 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="55ab7-106">Authenticate users connecting to a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="55ab7-107">:::no-loc(SignalR):::[ASP.NET Core 인증과](xref:security/authentication/identity) 함께 사용 하 여 사용자를 각 연결에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-107">:::no-loc(SignalR)::: can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="55ab7-108">허브에서는 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 속성을 사용 하 여 인증 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="55ab7-109">인증을 통해 허브는 사용자와 연결 된 모든 연결에서 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="55ab7-110">자세한 내용은 [에서 :::no-loc(SignalR)::: 사용자 및 그룹 관리 ](xref:signalr/groups)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="55ab7-110">For more information, see [Manage users and groups in :::no-loc(SignalR):::](xref:signalr/groups).</span></span> <span data-ttu-id="55ab7-111">단일 사용자에 게 여러 연결을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="55ab7-112">다음은 `Startup.Configure` 인증을 사용 하 고 ASP.NET Core 하는 예제입니다 :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="55ab7-112">The following is an example of `Startup.Configure` which uses :::no-loc(SignalR)::: and ASP.NET Core authentication:</span></span>

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

    app.Use:::no-loc(SignalR):::(hubs =>
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
> <span data-ttu-id="55ab7-113">및 ASP.NET Core 인증 미들웨어를 등록 하는 순서는 :::no-loc(SignalR)::: 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-113">The order in which you register the :::no-loc(SignalR)::: and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="55ab7-114">에 사용자가 있는를 항상 호출 `UseAuthentication` `Use:::no-loc(SignalR):::` :::no-loc(SignalR)::: `HttpContext` 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-114">Always call `UseAuthentication` before `Use:::no-loc(SignalR):::` so that :::no-loc(SignalR)::: has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="no-loccookie-authentication"></a><span data-ttu-id="55ab7-115">:::no-loc(Cookie)::: 인증</span><span class="sxs-lookup"><span data-stu-id="55ab7-115">:::no-loc(Cookie)::: authentication</span></span>

<span data-ttu-id="55ab7-116">브라우저 기반 앱에서 :::no-loc(cookie)::: 인증을 사용 하면 기존 사용자 자격 증명이 자동으로 연결로 이동 :::no-loc(SignalR)::: 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-116">In a browser-based app, :::no-loc(cookie)::: authentication allows your existing user credentials to automatically flow to :::no-loc(SignalR)::: connections.</span></span> <span data-ttu-id="55ab7-117">브라우저 클라이언트를 사용 하는 경우 추가 구성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="55ab7-118">사용자가 앱에 로그인 한 경우에는 :::no-loc(SignalR)::: 연결이 자동으로이 인증을 상속 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-118">If the user is logged in to your app, the :::no-loc(SignalR)::: connection automatically inherits this authentication.</span></span>

<span data-ttu-id="55ab7-119">:::no-loc(Cookie):::s는 브라우저 별로 액세스 토큰을 보낼 수 있지만 브라우저가 아닌 클라이언트는이를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-119">:::no-loc(Cookie):::s are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="55ab7-120">[.Net 클라이언트](xref:signalr/dotnet-client)를 사용 하는 경우 `:::no-loc(Cookie):::s` 호출에서 속성을 구성 `.WithUrl` 하 여를 제공할 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="55ab7-120">When using the [.NET Client](xref:signalr/dotnet-client), the `:::no-loc(Cookie):::s` property can be configured in the `.WithUrl` call to provide a :::no-loc(cookie):::.</span></span> <span data-ttu-id="55ab7-121">그러나 :::no-loc(cookie)::: .net 클라이언트에서 인증을 사용 하려면 앱이에 대 한 인증 데이터를 교환 하는 API를 제공 해야 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="55ab7-121">However, using :::no-loc(cookie)::: authentication from the .NET client requires the app to provide an API to exchange authentication data for a :::no-loc(cookie):::.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="55ab7-122">전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="55ab7-122">Bearer token authentication</span></span>

<span data-ttu-id="55ab7-123">클라이언트는를 사용 하는 대신 액세스 토큰을 제공할 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="55ab7-123">The client can provide an access token instead of using a :::no-loc(cookie):::.</span></span> <span data-ttu-id="55ab7-124">서버는 토큰의 유효성을 검사하고 사용자를 확인하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="55ab7-125">이 유효성 검사는 연결이 설정 된 경우에만 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="55ab7-126">연결 수명이 지속 되는 동안 서버는 토큰 해지 검사를 위해 자동으로 유효성을 다시 검사 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="55ab7-127">JavaScript 클라이언트에서 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 옵션을 사용 하 여 토큰을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="55ab7-128">.NET 클라이언트에는 토큰을 구성 하는 데 사용할 수 있는 유사한 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-128">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="55ab7-129">사용자가 제공 하는 액세스 토큰 함수는에서 수행 하는 **모든** HTTP 요청 전에 호출 됩니다 :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="55ab7-129">The access token function you provide is called before **every** HTTP request made by :::no-loc(SignalR):::.</span></span> <span data-ttu-id="55ab7-130">연결 중에 만료 될 수 있으므로 연결을 활성 상태로 유지 하기 위해 토큰을 갱신 해야 하는 경우이 함수 내에서이를 수행 하 고 업데이트 된 토큰을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="55ab7-131">표준 웹 Api에서 전달자 토큰은 HTTP 헤더에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="55ab7-132">그러나에서 :::no-loc(SignalR)::: 일부 전송을 사용할 때 브라우저에서 이러한 헤더를 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-132">However, :::no-loc(SignalR)::: is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="55ab7-133">Websocket 및 Server-Sent 이벤트를 사용 하는 경우 토큰은 쿼리 문자열 매개 변수로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> 

#### <a name="built-in-jwt-authentication"></a><span data-ttu-id="55ab7-134">기본 제공 JWT 인증</span><span class="sxs-lookup"><span data-stu-id="55ab7-134">Built-in JWT authentication</span></span>

<span data-ttu-id="55ab7-135">서버에서 전달자 토큰 인증은 [JWT 전달자 미들웨어](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-135">On the server, bearer token authentication is configured using the [JWT Bearer middleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="55ab7-136">쿼리 문자열은 브라우저 API 제한으로 인해 Websocket 및 Server-Sent 이벤트를 사용 하 여 연결할 때 브라우저에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="55ab7-137">HTTPS를 사용 하는 경우 쿼리 문자열 값은 TLS 연결을 통해 보안이 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="55ab7-138">그러나 많은 서버에서 쿼리 문자열 값을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-138">However, many servers log query string values.</span></span> <span data-ttu-id="55ab7-139">자세한 내용은 [ASP.NET Core :::no-loc(SignalR)::: 의 보안 고려 사항 ](xref:signalr/security)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="55ab7-139">For more information, see [Security considerations in ASP.NET Core :::no-loc(SignalR):::](xref:signalr/security).</span></span> <span data-ttu-id="55ab7-140">:::no-loc(SignalR)::: 는 헤더를 사용 하 여 토큰 (예: .NET 및 Java 클라이언트)을 지 원하는 환경에서 토큰을 전송 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-140">:::no-loc(SignalR)::: uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

#### <a name="no-locidentity-server-jwt-authentication"></a><span data-ttu-id="55ab7-141">:::no-loc(Identity)::: 서버 JWT 인증</span><span class="sxs-lookup"><span data-stu-id="55ab7-141">:::no-loc(Identity)::: Server JWT authentication</span></span>

<span data-ttu-id="55ab7-142">서버를 사용 하는 경우 :::no-loc(Identity)::: <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> 프로젝트에 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-142">When using :::no-loc(Identity)::: Server, add a <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service to the project:</span></span>

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

<span data-ttu-id="55ab7-143">`Startup.ConfigureServices`인증에 대 한 서비스 ( <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ) 및 :::no-loc(Identity)::: 서버 ()의 인증 처리기를 추가한 후에 서비스를 등록 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="55ab7-143">Register the service in `Startup.ConfigureServices` after adding services for authentication (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) and the authentication handler for :::no-loc(Identity)::: Server (<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>):</span></span>

```csharp
services.AddAuthentication()
    .Add:::no-loc(Identity):::ServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a><span data-ttu-id="55ab7-144">:::no-loc(Cookie):::s와 전달자 토큰 비교</span><span class="sxs-lookup"><span data-stu-id="55ab7-144">:::no-loc(Cookie):::s vs. bearer tokens</span></span> 

<span data-ttu-id="55ab7-145">:::no-loc(Cookie):::는 브라우저에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-145">:::no-loc(Cookie):::s are specific to browsers.</span></span> <span data-ttu-id="55ab7-146">다른 종류의 클라이언트에서 전송 하면 전달자 토큰 전송과 비교 하 여 복잡성이 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-146">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="55ab7-147">따라서 :::no-loc(cookie)::: 앱이 브라우저 클라이언트에서 사용자를 인증 해야 하는 경우를 제외 하 고는 인증을 권장 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-147">Consequently, :::no-loc(cookie)::: authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="55ab7-148">전달자 토큰 인증은 브라우저 클라이언트 이외의 클라이언트를 사용 하는 경우 권장 되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-148">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="55ab7-149">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="55ab7-149">Windows authentication</span></span>

<span data-ttu-id="55ab7-150">앱에서 [Windows 인증](xref:security/authentication/windowsauth) 을 구성 하는 경우에서 :::no-loc(SignalR)::: 해당 id를 사용 하 여 허브를 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-150">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, :::no-loc(SignalR)::: can use that identity to secure hubs.</span></span> <span data-ttu-id="55ab7-151">그러나 메시지를 개별 사용자에 게 보내려면 사용자 지정 사용자 ID 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-151">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="55ab7-152">Windows 인증 시스템이 "이름 식별자" 클레임을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-152">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="55ab7-153">:::no-loc(SignalR)::: 클레임을 사용 하 여 사용자 이름을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-153">:::no-loc(SignalR)::: uses the claim to determine the user name.</span></span>

<span data-ttu-id="55ab7-154">을 구현 하 `IUserIdProvider` 고 사용자가 식별자로 사용할 클레임 중 하나를 검색 하는 새 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-154">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="55ab7-155">예를 들어 "이름" 클레임 (형식의 Windows 사용자 이름)을 사용 하려면 `[Domain]\[Username]` 다음 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-155">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="55ab7-156">대신 `ClaimTypes.Name` 의 모든 값 `User` (예: Windows SID 식별자 등)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-156">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="55ab7-157">사용자가 선택 하는 값은 시스템의 모든 사용자에서 고유 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-157">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="55ab7-158">그렇지 않으면 한 사용자를 대상으로 하는 메시지가 다른 사용자로 이동 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-158">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="55ab7-159">메서드에이 구성 요소를 등록 `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-159">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.Add:::no-loc(SignalR):::();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="55ab7-160">.NET 클라이언트에서는 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 속성을 설정 하 여 Windows 인증을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-160">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="55ab7-161">Windows 인증은 Internet Explorer 및 Microsoft Edge에서 지원 되지만 일부 브라우저에서는 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-161">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="55ab7-162">예를 들어 Chrome 및 Safari에서는 Windows 인증 및 Websocket을 사용 하려는 시도가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-162">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="55ab7-163">Windows 인증이 실패 하면 클라이언트는 작동할 수 있는 다른 전송으로 대체 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-163">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="55ab7-164">클레임을 사용 하 여 id 처리 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="55ab7-164">Use claims to customize identity handling</span></span>

<span data-ttu-id="55ab7-165">사용자를 인증 하는 앱은 :::no-loc(SignalR)::: 사용자 클레임에서 사용자 id를 파생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-165">An app that authenticates users can derive :::no-loc(SignalR)::: user IDs from user claims.</span></span> <span data-ttu-id="55ab7-166">:::no-loc(SignalR):::에서 사용자 id를 만드는 방법을 지정 하려면 `IUserIdProvider` 구현을 구현 하 고 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-166">To specify how :::no-loc(SignalR)::: creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="55ab7-167">샘플 코드는 클레임을 사용 하 여 사용자의 이메일 주소를 식별 속성으로 선택 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-167">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="55ab7-168">사용자가 선택 하는 값은 시스템의 모든 사용자에서 고유 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-168">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="55ab7-169">그렇지 않으면 한 사용자를 대상으로 하는 메시지가 다른 사용자로 이동 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-169">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="55ab7-170">계정 등록은 `ClaimsTypes.Email` ASP.NET id 데이터베이스에 형식의 클레임을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-170">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="55ab7-171">에서이 구성 요소를 등록 `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-171">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="55ab7-172">사용자에 게 허브 및 허브 메서드에 액세스할 수 있는 권한 부여</span><span class="sxs-lookup"><span data-stu-id="55ab7-172">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="55ab7-173">기본적으로 허브의 모든 메서드는 인증 되지 않은 사용자가 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-173">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="55ab7-174">인증을 요구 하려면 [권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 특성을 허브에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-174">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="55ab7-175">`[Authorize]` 특성의 생성자 인수와 속성을 사용하여 특정 [권한 부여 정책](xref:security/authorization/policies)과 일치하는 사용자로만 액세스를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-175">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="55ab7-176">예를 들어, 라는 사용자 지정 권한 부여 정책을 사용 하는 경우 `MyAuthorizationPolicy` 다음 코드를 사용 하 여 해당 정책과 일치 하는 사용자만 허브에 액세스할 수 있도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-176">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="55ab7-177">개별 허브 방법에 `[Authorize]` 도 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-177">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="55ab7-178">현재 사용자가 메서드에 적용 된 정책과 일치 하지 않으면 호출자에 게 오류가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-178">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="55ab7-179">권한 부여 처리기를 사용 하 여 허브 메서드 권한 부여 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="55ab7-179">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="55ab7-180">:::no-loc(SignalR)::: 허브 메서드에 권한 부여가 필요할 때 권한 부여 처리기에 사용자 지정 리소스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-180">:::no-loc(SignalR)::: provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="55ab7-181">리소스가 `HubInvocationContext`의 인스턴스인 경우</span><span class="sxs-lookup"><span data-stu-id="55ab7-181">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="55ab7-182">에는 `HubInvocationContext` `HubCallerContext` , 호출 되는 허브 메서드의 이름 및 허브 메서드에 대 한 인수가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-182">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="55ab7-183">Azure Active Directory를 통해 여러 조직 로그인을 허용 하는 대화방의 예를 생각해 보세요.</span><span class="sxs-lookup"><span data-stu-id="55ab7-183">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="55ab7-184">Microsoft 계정 있는 사용자는 채팅에 로그인 할 수 있지만 소유 조직의 구성원만 사용자를 금지 하거나 사용자의 채팅 기록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-184">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="55ab7-185">또한 특정 사용자의 특정 기능을 제한 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-185">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="55ab7-186">ASP.NET Core 3.0의 업데이트 된 기능을 사용 하는 것이 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-186">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="55ab7-187">을 `DomainRestrictedRequirement` 사용자 지정으로 사용 하는 방법을 확인 `IAuthorizationRequirement` 합니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-187">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="55ab7-188">이제 `HubInvocationContext` 리소스 매개 변수가 전달 되 고 있으므로 내부 논리는 허브가 호출 되는 컨텍스트를 검사 하 고 사용자가 개별 허브 메서드를 실행할 수 있도록 하는 결정을 내릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-188">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.:::no-loc(Identity):::.Name) && 
            context.User.:::no-loc(Identity):::.Name.EndsWith("@microsoft.com"))
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

<span data-ttu-id="55ab7-189">에서 `Startup.ConfigureServices` `DomainRestrictedRequirement` 정책 만들기에 대 한 매개 변수로 사용자 지정 요구 사항을 제공 하 여 새 정책을 추가 합니다 `DomainRestricted` .</span><span class="sxs-lookup"><span data-stu-id="55ab7-189">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="55ab7-190">앞의 예제에서 클래스는 `DomainRestrictedRequirement` `IAuthorizationRequirement` 및 해당 `AuthorizationHandler` 요구 사항에 대 한 고유한 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-190">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="55ab7-191">이러한 두 구성 요소를 별도의 클래스로 분할 하 여 문제를 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-191">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="55ab7-192">예제의 방법의 장점은 요구 사항과 처리기가 동일 하기 때문에 시작 하는 동안를 주입할 필요가 없다는 `AuthorizationHandler` 점입니다.</span><span class="sxs-lookup"><span data-stu-id="55ab7-192">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="55ab7-193">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="55ab7-193">Additional resources</span></span>

* [<span data-ttu-id="55ab7-194">ASP.NET Core에서 전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="55ab7-194">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="55ab7-195">리소스 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="55ab7-195">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
