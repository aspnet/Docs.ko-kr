---
title: ASP.NET Core가 있는 OWIN(Open Web Interface for .NET)
author: ardalis
description: ASP.NET Core에서 웹앱이 웹 서버에서 분리될 수 있도록 하는 OWIN(Open Web Interface for .NET)을 지원하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: 6085abc45137223d7a676107cf06dc2cf97a5c19
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060679"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="162c1-103">ASP.NET Core가 있는 OWIN(Open Web Interface for .NET)</span><span class="sxs-lookup"><span data-stu-id="162c1-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="162c1-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="162c1-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="162c1-105">ASP.NET Core는 OWIN(Open Web Interface for .NET)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="162c1-106">OWIN을 사용하면 웹 앱을 웹 서버에서 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="162c1-107">미들웨어를 파이프라인에서 사용하고 요청 및 관련된 응답을 처리하기 위한 표준 방법을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="162c1-108">ASP.NET Core 애플리케이션 및 미들웨어는 OWIN 기반 애플리케이션, 서버 및 미들웨어와 상호 운용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="162c1-109">OWIN은 서로 다른 개체 모델이 있는 두 프레임워크를 함께 사용할 수 있도록 허용하는 분리 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="162c1-110">`Microsoft.AspNetCore.Owin` 패키지는 두 개의 어댑터 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="162c1-111">ASP.NET Core에서 OWIN으로</span><span class="sxs-lookup"><span data-stu-id="162c1-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="162c1-112">OWIN에서 ASP.NET Core로</span><span class="sxs-lookup"><span data-stu-id="162c1-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="162c1-113">이렇게 하면 ASP.NET Core를 OWIN 호환 가능한 서버/호스트를 기반으로 호스팅하거나 다른 OWIN 호환 가능한 구성 요소를 ASP.NET Core를 기반으로 실행되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="162c1-114">이러한 어댑터를 사용하면 성능 비용이 수반됩니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="162c1-115">ASP.NET Core 구성 요소만을 사용하는 앱은 `Microsoft.AspNetCore.Owin` 패키지 또는 어댑터를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="162c1-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="162c1-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="162c1-117">ASP.NET Core 파이프라인에서 OWIN 미들웨어 실행</span><span class="sxs-lookup"><span data-stu-id="162c1-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="162c1-118">ASP.NET Core의 OWIN 지원은 `Microsoft.AspNetCore.Owin` 패키지의 일부로 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="162c1-119">이 패키지를 설치하여 OWIN 지원을 프로젝트로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="162c1-120">OWIN 미들웨어는 `Func<IDictionary<string, object>, Task>` 인터페이스 및 특정 키 설정(예: `owin.ResponseBody`)이 필요한 [OWIN 사양](https://owin.org/spec/spec/owin-1.0.0.html)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="162c1-121">다음과 같은 간단한 OWIN 미들웨어는 "Hello World"를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="162c1-122">샘플 서명은 `Task`를 반환하고 OWIN에 필요한 `IDictionary<string, object>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="162c1-123">다음 코드는 `UseOwin` 확장 메서드로 `OwinHello` 미들웨어(위에 표시된)를 ASP.NET Core 파이프라인에 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="162c1-124">OWIN 파이프라인 내에서 수행하도록 기타 작업을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="162c1-125">응답 헤더는 응답 스트림에 대한 최초 작성 전에 수정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="162c1-126">`UseOwin`에 대한 여러 번의 호출은 성능상의 이유로 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="162c1-127">OWIN 구성 요소는 함께 그룹화하는 경우 가장 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="162c1-128">OWIN 기반 서버에서 ASP.NET Core 실행 및 해당 WebSocket 지원 사용</span><span class="sxs-lookup"><span data-stu-id="162c1-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="162c1-129">ASP.NET Core에서 OWIN 기반 서버 기능을 활용할 수 있는 방법의 또 다른 예는 WebSocket과 같은 기능에 대한 액세스입니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="162c1-130">이전 예제에서 사용되는 .NET OWIN 웹 서버에는 ASP.NET Core 애플리케이션에서 활용할 수 있는 기본 제공되는 웹 소켓에 대한 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="162c1-131">다음 예제에서는 웹 소켓을 지원하고 WebSocket을 통해 서버에 전송된 모든 항목을 다시 표시하는 단순한 웹앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="162c1-132">OWIN 환경</span><span class="sxs-lookup"><span data-stu-id="162c1-132">OWIN environment</span></span>

<span data-ttu-id="162c1-133">`HttpContext`를 사용하여 OWIN 환경을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="162c1-134">OWIN 키</span><span class="sxs-lookup"><span data-stu-id="162c1-134">OWIN keys</span></span>

<span data-ttu-id="162c1-135">OWIN은 HTTP 요청/응답 교환 전체에서 정보를 전달하는 `IDictionary<string,object>` 개체에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="162c1-136">ASP.NET Core는 아래에 나열된 키를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="162c1-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="162c1-137">[기본 사양, 확장](https://owin.org/#spec) 및 [OWIN 키 지침 및 공통 키](https://owin.org/spec/spec/CommonKeys.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="162c1-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="162c1-138">요청 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="162c1-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="162c1-139">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-139">Key</span></span>               | <span data-ttu-id="162c1-140">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-140">Value (type)</span></span> | <span data-ttu-id="162c1-141">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-142">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="162c1-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="162c1-143">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="162c1-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="162c1-144">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="162c1-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="162c1-145">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="162c1-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="162c1-146">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="162c1-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="162c1-147">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="162c1-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="162c1-148">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="162c1-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="162c1-149">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="162c1-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="162c1-150">요청 데이터(OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="162c1-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="162c1-151">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-151">Key</span></span>               | <span data-ttu-id="162c1-152">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-152">Value (type)</span></span> | <span data-ttu-id="162c1-153">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-154">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="162c1-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="162c1-155">Optional</span><span class="sxs-lookup"><span data-stu-id="162c1-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="162c1-156">응답 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="162c1-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="162c1-157">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-157">Key</span></span>               | <span data-ttu-id="162c1-158">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-158">Value (type)</span></span> | <span data-ttu-id="162c1-159">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-160">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="162c1-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="162c1-161">Optional</span><span class="sxs-lookup"><span data-stu-id="162c1-161">Optional</span></span> |
| <span data-ttu-id="162c1-162">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="162c1-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="162c1-163">Optional</span><span class="sxs-lookup"><span data-stu-id="162c1-163">Optional</span></span> |
| <span data-ttu-id="162c1-164">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="162c1-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="162c1-165">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="162c1-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="162c1-166">기타 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="162c1-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="162c1-167">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-167">Key</span></span>               | <span data-ttu-id="162c1-168">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-168">Value (type)</span></span> | <span data-ttu-id="162c1-169">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-170">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="162c1-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="162c1-171">owin.Version</span><span class="sxs-lookup"><span data-stu-id="162c1-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="162c1-172">공통 키</span><span class="sxs-lookup"><span data-stu-id="162c1-172">Common keys</span></span>

| <span data-ttu-id="162c1-173">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-173">Key</span></span>               | <span data-ttu-id="162c1-174">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-174">Value (type)</span></span> | <span data-ttu-id="162c1-175">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-176">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="162c1-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="162c1-177">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="162c1-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="162c1-178">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="162c1-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="162c1-179">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="162c1-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="162c1-180">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="162c1-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="162c1-181">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="162c1-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="162c1-182">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="162c1-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="162c1-183">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="162c1-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="162c1-184">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="162c1-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="162c1-185">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-185">Key</span></span>               | <span data-ttu-id="162c1-186">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-186">Value (type)</span></span> | <span data-ttu-id="162c1-187">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-188">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="162c1-188">sendfile.SendAsync</span></span> | <span data-ttu-id="162c1-189">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="162c1-190">요청당</span><span class="sxs-lookup"><span data-stu-id="162c1-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="162c1-191">불투명 v0.3.0</span><span class="sxs-lookup"><span data-stu-id="162c1-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="162c1-192">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-192">Key</span></span>               | <span data-ttu-id="162c1-193">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-193">Value (type)</span></span> | <span data-ttu-id="162c1-194">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-195">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="162c1-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="162c1-196">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="162c1-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="162c1-197">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="162c1-198">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="162c1-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="162c1-199">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="162c1-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="162c1-200">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="162c1-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="162c1-201">Key</span><span class="sxs-lookup"><span data-stu-id="162c1-201">Key</span></span>               | <span data-ttu-id="162c1-202">값(형식)</span><span class="sxs-lookup"><span data-stu-id="162c1-202">Value (type)</span></span> | <span data-ttu-id="162c1-203">설명</span><span class="sxs-lookup"><span data-stu-id="162c1-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="162c1-204">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="162c1-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="162c1-205">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="162c1-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="162c1-206">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="162c1-207">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="162c1-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="162c1-208">비-사양</span><span class="sxs-lookup"><span data-stu-id="162c1-208">Non-spec</span></span> |
| <span data-ttu-id="162c1-209">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="162c1-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="162c1-210">[RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) 5.5단계 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="162c1-211">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="162c1-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="162c1-212">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="162c1-213">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="162c1-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="162c1-214">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="162c1-215">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="162c1-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="162c1-216">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="162c1-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="162c1-217">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="162c1-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="162c1-218">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="162c1-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="162c1-219">Optional</span><span class="sxs-lookup"><span data-stu-id="162c1-219">Optional</span></span> |
| <span data-ttu-id="162c1-220">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="162c1-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="162c1-221">Optional</span><span class="sxs-lookup"><span data-stu-id="162c1-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="162c1-222">추가 자료</span><span class="sxs-lookup"><span data-stu-id="162c1-222">Additional resources</span></span>

* [<span data-ttu-id="162c1-223">미들웨어</span><span class="sxs-lookup"><span data-stu-id="162c1-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="162c1-224">서버</span><span class="sxs-lookup"><span data-stu-id="162c1-224">Servers</span></span>](xref:fundamentals/servers/index)
