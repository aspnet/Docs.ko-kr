---
title: 'ASP.NET Core의 보안 고려 사항 :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core에서 인증 및 권한 부여를 사용 하는 방법에 대해 알아봅니다 :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/security
ms.openlocfilehash: 5ecbf07b1527e9c68443870f7fce77adc29a5416
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050838"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="b586b-103">ASP.NET Core의 보안 고려 사항 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="b586b-103">Security considerations in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="b586b-104">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b586b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b586b-105">이 문서에서는 보안에 대 한 정보를 제공 :::no-loc(SignalR)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-105">This article provides information on securing :::no-loc(SignalR):::.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="b586b-106">크로스-원본 자원 공유</span><span class="sxs-lookup"><span data-stu-id="b586b-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="b586b-107">[CORS (크로스-원본 자원 공유)](https://www.w3.org/TR/cors/) 를 사용 하 여 브라우저에서 크로스-원본 연결을 허용할 수 있습니다 :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="b586b-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin :::no-loc(SignalR)::: connections in the browser.</span></span> <span data-ttu-id="b586b-108">JavaScript 코드가 앱과 다른 도메인에서 호스트 되는 경우 :::no-loc(SignalR)::: javascript가 앱에 연결할 수 있도록 [CORS 미들웨어](xref:security/cors) 를 사용 하도록 설정 해야 합니다 :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="b586b-108">If JavaScript code is hosted on a different domain from the :::no-loc(SignalR)::: app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the :::no-loc(SignalR)::: app.</span></span> <span data-ttu-id="b586b-109">신뢰 하거나 제어 하는 도메인 에서만 원본 간 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="b586b-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-110">For example:</span></span>

* <span data-ttu-id="b586b-111">사이트는에서 호스트 됩니다. `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="b586b-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="b586b-112">:::no-loc(SignalR):::앱이에서 호스팅됩니다.`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="b586b-112">Your :::no-loc(SignalR)::: app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="b586b-113">CORS를 :::no-loc(SignalR)::: 앱에서 원본만 허용 하도록 구성 해야 합니다 `www.example.com` .</span><span class="sxs-lookup"><span data-stu-id="b586b-113">CORS should be configured in the :::no-loc(SignalR)::: app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="b586b-114">CORS를 구성 하는 방법에 대 한 자세한 내용은 [cors (원본 간 요청) 사용](xref:security/cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b586b-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="b586b-115">:::no-loc(SignalR):::다음 CORS 정책이 **필요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-115">:::no-loc(SignalR)::: **requires** the following CORS policies:</span></span>

* <span data-ttu-id="b586b-116">필요한 특정 원본을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-116">Allow the specific expected origins.</span></span> <span data-ttu-id="b586b-117">모든 원본을 허용 하는 것은 가능 하지만 안전 하거나 권장 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="b586b-118">HTTP 메서드 `GET` 및가 `POST` 허용 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="b586b-119">:::no-loc(cookie):::기반 고정 세션이 제대로 작동 하려면 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-119">Credentials must be allowed in order for :::no-loc(cookie):::-based sticky sessions to work correctly.</span></span> <span data-ttu-id="b586b-120">인증을 사용 하지 않는 경우에도 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b586b-121">그러나 5.0에서는 자격 증명을 사용 하지 않도록 TypeScript 클라이언트에서 옵션을 제공 했습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="b586b-122">자격 증명을 사용 하지 않도록 하는 옵션은 응용 프로그램에서와 같은 자격 증명이 필요 하지 않은 100%를 알고 있는 경우에만 사용 해야 합니다 :::no-loc(Cookie)::: ( :::no-loc(cookie)::: 고정 세션에 여러 서버를 사용 하는 경우 azure app service에서 사용 됨).</span><span class="sxs-lookup"><span data-stu-id="b586b-122">The option to not use credentials should only be used when you know 100% that credentials like :::no-loc(Cookie):::s are not needed in your app (:::no-loc(cookie):::s are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="b586b-123">예를 들어, 다음 CORS 정책은에서 호스팅되는 :::no-loc(SignalR)::: 브라우저 클라이언트가 `https://example.com` 에서 호스트 되는 앱에 액세스할 수 있도록 허용 합니다 :::no-loc(SignalR)::: `https://signalr.example.com` .</span><span class="sxs-lookup"><span data-stu-id="b586b-123">For example, the following CORS policy allows a :::no-loc(SignalR)::: browser client hosted on `https://example.com` to access the :::no-loc(SignalR)::: app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of :::no-loc(SignalR):::.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="b586b-124">WebSocket 원본 제한</span><span class="sxs-lookup"><span data-stu-id="b586b-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b586b-125">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b586b-126">Websocket에 대 한 원본 제한의 경우 [websocket 원본 제한](xref:fundamentals/websockets#websocket-origin-restriction)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b586b-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b586b-127">CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b586b-128">브라우저는 다음을 수행하지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="b586b-128">Browsers do **not** :</span></span>

* <span data-ttu-id="b586b-129">CORS pre-flight 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="b586b-130">WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="b586b-131">그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="b586b-132">애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="b586b-133">ASP.NET Core 2.1 이상에서 헤더 유효성 검사는 이전에 배치 된 사용자 지정 미들웨어 **및의 `Use:::no-loc(SignalR):::` 인증 미들웨어** 를 사용 하 여 달성할 수 있습니다 `Configure` .</span><span class="sxs-lookup"><span data-stu-id="b586b-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `Use:::no-loc(SignalR):::`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="b586b-134">`Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="b586b-135">이러한 헤더는 인증 메커니즘으로 **사용 하면 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="b586b-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="b586b-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="b586b-136">ConnectionId</span></span>

<span data-ttu-id="b586b-137">`ConnectionId` :::no-loc(SignalR)::: 서버 또는 클라이언트 버전이 2.2 ASP.NET Core이 하 이면 악성 가장을 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-137">Exposing `ConnectionId` can lead to malicious impersonation if the :::no-loc(SignalR)::: server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="b586b-138">:::no-loc(SignalR):::서버 및 클라이언트 버전이 3.0 이상 ASP.NET Core 경우이 아닌은 `ConnectionToken` `ConnectionId` 비밀로 유지 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-138">If the :::no-loc(SignalR)::: server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="b586b-139">는 `ConnectionToken` 어떠한 API 에서도 의도적으로 노출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="b586b-140">이전 클라이언트가 서버에 연결 되지 않도록 하는 것이 어려울 수 :::no-loc(SignalR)::: 있으므로 :::no-loc(SignalR)::: 서버 버전이 3.0 이상 ASP.NET Core 경우에는이 `ConnectionId` 노출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-140">It can be difficult to ensure that older :::no-loc(SignalR)::: clients aren't connecting to the server, so even if your :::no-loc(SignalR)::: server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="b586b-141">액세스 토큰 로깅</span><span class="sxs-lookup"><span data-stu-id="b586b-141">Access token logging</span></span>

<span data-ttu-id="b586b-142">Websocket 또는 Server-Sent 이벤트를 사용 하는 경우 브라우저 클라이언트는 쿼리 문자열에 액세스 토큰을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="b586b-143">쿼리 문자열을 통해 액세스 토큰을 받는 것은 일반적으로 표준 헤더를 사용 하는 것 만큼 안전 `Authorization` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="b586b-144">항상 HTTPS를 사용 하 여 클라이언트와 서버 간의 안전한 종단 간 연결을 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="b586b-145">많은 웹 서버는 쿼리 문자열을 포함 하 여 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="b586b-146">Url을 기록 하면 액세스 토큰이 기록 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="b586b-147">ASP.NET Core은 기본적으로 쿼리 문자열을 포함 하는 각 요청에 대 한 URL을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="b586b-148">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="b586b-149">서버 로그를 사용 하 여이 데이터를 기록 하는 데 문제가 있는 경우로 거를 수준 이상으로 구성 하 여이 로깅을 완전히 사용 하지 않도록 설정할 수 있습니다 `Microsoft.AspNetCore.Hosting` `Warning` . 이러한 메시지는 수준에서 기록 됩니다 `Info` .</span><span class="sxs-lookup"><span data-stu-id="b586b-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="b586b-150">자세한 내용은 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b586b-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="b586b-151">여전히 특정 요청 정보를 기록 하려는 경우 [미들웨어를 작성](xref:fundamentals/middleware/write) 하 여 필요한 데이터를 기록 하 고 `access_token` 쿼리 문자열 값 (있는 경우)을 필터링 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="b586b-152">예외</span><span class="sxs-lookup"><span data-stu-id="b586b-152">Exceptions</span></span>

<span data-ttu-id="b586b-153">일반적으로 예외 메시지는 클라이언트에 공개되지 않아야 하는 중요한 데이터로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="b586b-154">기본적으로는 :::no-loc(SignalR)::: 허브 메서드에서 throw 되는 예외에 대 한 세부 정보를 클라이언트에 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-154">By default, :::no-loc(SignalR)::: doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="b586b-155">대신, 클라이언트는 오류가 발생했음을 나타내는 일반 메시지를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="b586b-156">[EnableDetailedErrors](xref:signalr/configuration#configure-server-options)를 사용 하 여 클라이언트에 대 한 예외 메시지 배달 (예: 개발 또는 테스트)을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="b586b-157">예외 메시지는 프로덕션 앱에서 클라이언트에 노출 되 면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="b586b-158">버퍼 관리</span><span class="sxs-lookup"><span data-stu-id="b586b-158">Buffer management</span></span>

<span data-ttu-id="b586b-159">:::no-loc(SignalR)::: 는 연결당 버퍼를 사용 하 여 들어오고 나가는 메시지를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-159">:::no-loc(SignalR)::: uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="b586b-160">기본적으로에서는 :::no-loc(SignalR)::: 이러한 버퍼를 32 KB로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-160">By default, :::no-loc(SignalR)::: limits these buffers to 32 KB.</span></span> <span data-ttu-id="b586b-161">클라이언트 또는 서버에서 보낼 수 있는 최대 메시지는 32 KB입니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="b586b-162">메시지에 대 한 연결에서 사용 되는 최대 메모리는 32 KB입니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="b586b-163">메시지가 항상 32 KB 보다 작은 경우 다음 제한을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="b586b-164">클라이언트에서 더 큰 메시지를 보낼 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="b586b-165">서버는 메시지를 수락 하기 위해 대량 버퍼를 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="b586b-166">메시지가 32 KB 보다 큰 경우 제한을 늘릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="b586b-167">이 제한을 늘리려면 다음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-167">Increasing this limit means:</span></span>

* <span data-ttu-id="b586b-168">클라이언트는 서버에서 많은 메모리 버퍼를 할당 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="b586b-169">대량 버퍼를 서버 할당 하면 동시 연결 수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="b586b-170">들어오는 메시지와 보내는 메시지에 대 한 제한이 있습니다. 둘 다에서 구성 된 [Httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) 개체에 대해 구성할 수 있습니다 `MapHub` .</span><span class="sxs-lookup"><span data-stu-id="b586b-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="b586b-171">`ApplicationMaxBufferSize` 서버에서 버퍼링 하는 클라이언트의 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="b586b-172">클라이언트에서이 한도 보다 큰 메시지를 보내려고 시도 하면 연결이 닫힐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="b586b-173">`TransportMaxBufferSize` 서버에서 보낼 수 있는 최대 바이트 수를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="b586b-174">서버에서이 한도 보다 큰 메시지 (허브 메서드에서 반환 값 포함)를 보내려고 시도 하면 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="b586b-175">제한을 설정 하면 `0` 제한이 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="b586b-176">제한을 제거 하면 클라이언트가 모든 크기의 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="b586b-177">큰 메시지를 보내는 악의적인 클라이언트는 과도 한 메모리를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="b586b-178">메모리를 과도 하 게 사용 하면 동시 연결 수를 상당히 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b586b-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
