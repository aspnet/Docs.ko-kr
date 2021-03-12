---
title: ASP.NET Core가 있는 OWIN(Open Web Interface for .NET)
author: ardalis
description: ASP.NET Core에서 웹앱이 웹 서버에서 분리될 수 있도록 하는 OWIN(Open Web Interface for .NET)을 지원하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: e476f3f62a250d960c809e5062b3bd8ca3a1940a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587238"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="732d2-103">ASP.NET Core가 있는 OWIN(Open Web Interface for .NET)</span><span class="sxs-lookup"><span data-stu-id="732d2-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="732d2-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="732d2-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="732d2-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="732d2-105">ASP.NET Core:</span></span>

* <span data-ttu-id="732d2-106">OWIN(Open Web Interface for .NET)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="732d2-107">`Microsoft.Owin.*`([Katana](/aspnet/aspnet/overview/owin-and-katana/)) 라이브러리에 대한 .NET Core 호환 대체 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="732d2-108">OWIN을 사용하면 웹 앱을 웹 서버에서 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="732d2-109">미들웨어를 파이프라인에서 사용하고 요청 및 관련된 응답을 처리하기 위한 표준 방법을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="732d2-110">ASP.NET Core 애플리케이션 및 미들웨어는 OWIN 기반 애플리케이션, 서버 및 미들웨어와 상호 운용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="732d2-111">OWIN은 서로 다른 개체 모델이 있는 두 프레임워크를 함께 사용할 수 있도록 허용하는 분리 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="732d2-112">`Microsoft.AspNetCore.Owin` 패키지는 두 개의 어댑터 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="732d2-113">ASP.NET Core에서 OWIN으로</span><span class="sxs-lookup"><span data-stu-id="732d2-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="732d2-114">OWIN에서 ASP.NET Core로</span><span class="sxs-lookup"><span data-stu-id="732d2-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="732d2-115">이렇게 하면 ASP.NET Core를 OWIN 호환 가능한 서버/호스트를 기반으로 호스팅하거나 다른 OWIN 호환 가능한 구성 요소를 ASP.NET Core를 기반으로 실행되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="732d2-116">이러한 어댑터를 사용하면 성능 비용이 수반됩니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="732d2-117">ASP.NET Core 구성 요소만을 사용하는 앱은 `Microsoft.AspNetCore.Owin` 패키지 또는 어댑터를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="732d2-118">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="732d2-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="732d2-119">ASP.NET Core 파이프라인에서 OWIN 미들웨어 실행</span><span class="sxs-lookup"><span data-stu-id="732d2-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="732d2-120">ASP.NET Core의 OWIN 지원은 `Microsoft.AspNetCore.Owin` 패키지의 일부로 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="732d2-121">이 패키지를 설치하여 OWIN 지원을 프로젝트로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="732d2-122">OWIN 미들웨어는 `Func<IDictionary<string, object>, Task>` 인터페이스 및 특정 키 설정(예: `owin.ResponseBody`)이 필요한 [OWIN 사양](https://owin.org/spec/spec/owin-1.0.0.html)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="732d2-123">다음과 같은 간단한 OWIN 미들웨어는 "Hello World"를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-123">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="732d2-124">샘플 서명은 `Task`를 반환하고 OWIN에 필요한 `IDictionary<string, object>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="732d2-125">다음 코드는 `UseOwin` 확장 메서드로 `OwinHello` 미들웨어(위에 표시된)를 ASP.NET Core 파이프라인에 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="732d2-126">OWIN 파이프라인 내에서 수행하도록 기타 작업을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="732d2-127">응답 헤더는 응답 스트림에 대한 최초 작성 전에 수정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="732d2-128">`UseOwin`에 대한 여러 번의 호출은 성능상의 이유로 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="732d2-129">OWIN 구성 요소는 함께 그룹화하는 경우 가장 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-129">OWIN components will operate best if grouped together.</span></span>

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="732d2-130">OWIN 기반 서버에서 ASP.NET Core 실행 및 해당 WebSocket 지원 사용</span><span class="sxs-lookup"><span data-stu-id="732d2-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="732d2-131">ASP.NET Core에서 OWIN 기반 서버 기능을 활용할 수 있는 방법의 또 다른 예는 WebSocket과 같은 기능에 대한 액세스입니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="732d2-132">이전 예제에서 사용되는 .NET OWIN 웹 서버에는 ASP.NET Core 애플리케이션에서 활용할 수 있는 기본 제공되는 웹 소켓에 대한 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="732d2-133">다음 예제에서는 웹 소켓을 지원하고 WebSocket을 통해 서버에 전송된 모든 항목을 다시 표시하는 단순한 웹앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

## <a name="owin-environment"></a><span data-ttu-id="732d2-134">OWIN 환경</span><span class="sxs-lookup"><span data-stu-id="732d2-134">OWIN environment</span></span>

<span data-ttu-id="732d2-135">`HttpContext`를 사용하여 OWIN 환경을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="732d2-136">OWIN 키</span><span class="sxs-lookup"><span data-stu-id="732d2-136">OWIN keys</span></span>

<span data-ttu-id="732d2-137">OWIN은 HTTP 요청/응답 교환 전체에서 정보를 전달하는 `IDictionary<string,object>` 개체에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="732d2-138">ASP.NET Core는 아래에 나열된 키를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="732d2-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="732d2-139">[기본 사양, 확장](https://owin.org/#spec) 및 [OWIN 키 지침 및 공통 키](https://owin.org/spec/spec/CommonKeys.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="732d2-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="732d2-140">요청 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="732d2-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="732d2-141">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-141">Key</span></span>               | <span data-ttu-id="732d2-142">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-142">Value (type)</span></span> | <span data-ttu-id="732d2-143">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-144">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="732d2-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="732d2-145">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="732d2-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="732d2-146">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="732d2-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="732d2-147">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="732d2-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="732d2-148">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="732d2-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="732d2-149">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="732d2-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="732d2-150">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="732d2-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="732d2-151">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="732d2-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="732d2-152">요청 데이터(OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="732d2-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="732d2-153">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-153">Key</span></span>               | <span data-ttu-id="732d2-154">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-154">Value (type)</span></span> | <span data-ttu-id="732d2-155">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-156">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="732d2-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="732d2-157">Optional</span><span class="sxs-lookup"><span data-stu-id="732d2-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="732d2-158">응답 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="732d2-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="732d2-159">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-159">Key</span></span>               | <span data-ttu-id="732d2-160">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-160">Value (type)</span></span> | <span data-ttu-id="732d2-161">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-162">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="732d2-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="732d2-163">Optional</span><span class="sxs-lookup"><span data-stu-id="732d2-163">Optional</span></span> |
| <span data-ttu-id="732d2-164">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="732d2-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="732d2-165">Optional</span><span class="sxs-lookup"><span data-stu-id="732d2-165">Optional</span></span> |
| <span data-ttu-id="732d2-166">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="732d2-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="732d2-167">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="732d2-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="732d2-168">기타 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="732d2-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="732d2-169">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-169">Key</span></span>               | <span data-ttu-id="732d2-170">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-170">Value (type)</span></span> | <span data-ttu-id="732d2-171">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-172">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="732d2-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="732d2-173">owin.Version</span><span class="sxs-lookup"><span data-stu-id="732d2-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="732d2-174">공통 키</span><span class="sxs-lookup"><span data-stu-id="732d2-174">Common keys</span></span>

| <span data-ttu-id="732d2-175">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-175">Key</span></span>               | <span data-ttu-id="732d2-176">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-176">Value (type)</span></span> | <span data-ttu-id="732d2-177">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-178">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="732d2-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="732d2-179">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="732d2-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="732d2-180">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="732d2-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="732d2-181">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="732d2-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="732d2-182">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="732d2-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="732d2-183">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="732d2-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="732d2-184">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="732d2-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="732d2-185">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="732d2-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="732d2-186">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="732d2-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="732d2-187">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-187">Key</span></span>               | <span data-ttu-id="732d2-188">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-188">Value (type)</span></span> | <span data-ttu-id="732d2-189">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-190">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="732d2-190">sendfile.SendAsync</span></span> | <span data-ttu-id="732d2-191">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="732d2-192">요청당</span><span class="sxs-lookup"><span data-stu-id="732d2-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="732d2-193">불투명 v0.3.0</span><span class="sxs-lookup"><span data-stu-id="732d2-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="732d2-194">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-194">Key</span></span>               | <span data-ttu-id="732d2-195">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-195">Value (type)</span></span> | <span data-ttu-id="732d2-196">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-197">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="732d2-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="732d2-198">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="732d2-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="732d2-199">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="732d2-200">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="732d2-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="732d2-201">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="732d2-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="732d2-202">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="732d2-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="732d2-203">Key</span><span class="sxs-lookup"><span data-stu-id="732d2-203">Key</span></span>               | <span data-ttu-id="732d2-204">값(형식)</span><span class="sxs-lookup"><span data-stu-id="732d2-204">Value (type)</span></span> | <span data-ttu-id="732d2-205">설명</span><span class="sxs-lookup"><span data-stu-id="732d2-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="732d2-206">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="732d2-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="732d2-207">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="732d2-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="732d2-208">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="732d2-209">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="732d2-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="732d2-210">비-사양</span><span class="sxs-lookup"><span data-stu-id="732d2-210">Non-spec</span></span> |
| <span data-ttu-id="732d2-211">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="732d2-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="732d2-212">[RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) 5.5단계 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="732d2-213">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="732d2-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="732d2-214">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="732d2-215">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="732d2-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="732d2-216">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="732d2-217">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="732d2-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="732d2-218">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="732d2-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="732d2-219">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="732d2-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="732d2-220">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="732d2-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="732d2-221">Optional</span><span class="sxs-lookup"><span data-stu-id="732d2-221">Optional</span></span> |
| <span data-ttu-id="732d2-222">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="732d2-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="732d2-223">Optional</span><span class="sxs-lookup"><span data-stu-id="732d2-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="732d2-224">추가 자료</span><span class="sxs-lookup"><span data-stu-id="732d2-224">Additional resources</span></span>

* [<span data-ttu-id="732d2-225">미들웨어</span><span class="sxs-lookup"><span data-stu-id="732d2-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="732d2-226">서버</span><span class="sxs-lookup"><span data-stu-id="732d2-226">Servers</span></span>](xref:fundamentals/servers/index)
