---
title: .NET 클라이언트를 사용하여 gRPC 서비스 호출
author: jamesnk
description: .NET gRPC 클라이언트를 사용하여 gRPC 서비스를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 9322020083ce25b00b2979633ae8a692cfd4da4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060965"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="f32b2-103">.NET 클라이언트를 사용하여 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="f32b2-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="f32b2-104">.NET gRPC 클라이언트 라이브러리는 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="f32b2-105">이 문서에서는 다음을 수행하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-105">This document explains how to:</span></span>

* <span data-ttu-id="f32b2-106">gRPC 서비스를 호출하도록 gRPC 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="f32b2-107">단항, 서버 스트리밍, 클라이언트 스트리밍 및 양방향 스트리밍 메서드에 대한 gRPC 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="f32b2-108">gRPC 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="f32b2-108">Configure gRPC client</span></span>

<span data-ttu-id="f32b2-109">gRPC 클라이언트는 [ *\*.proto* 파일에서 생성](xref:grpc/basics#generated-c-assets)되는 구체적인 클라이언트 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="f32b2-110">구체적인 gRPC 클라이언트에는 *\*.proto* 파일에서 gRPC 서비스로 변환되는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="f32b2-111">예를 들어 `Greeter`라는 서비스는 서비스를 호출하는 메서드를 사용하여 `GreeterClient` 형식을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="f32b2-112">채널에서 gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="f32b2-113">먼저 `GrpcChannel.ForAddress`를 사용하여 채널을 만든 다음, 채널을 사용하여 gRPC 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="f32b2-114">채널은 gRPC 서비스에 대한 장기 연결을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="f32b2-115">채널이 생성되면 서비스 호출과 관련된 옵션을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="f32b2-116">예를 들어 호출, 최대 전송 및 수신 메시지 크기, 로깅을 수행하는 데 사용되는 `HttpClient`를 `GrpcChannelOptions`에서 지정하고 `GrpcChannel.ForAddress`와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="f32b2-117">전체 옵션 목록은 [클라이언트 구성 옵션](xref:grpc/configuration#configure-client-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f32b2-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="f32b2-118">TLS 구성</span><span class="sxs-lookup"><span data-stu-id="f32b2-118">Configure TLS</span></span>

<span data-ttu-id="f32b2-119">gRPC 클라이언트는 호출된 서비스와 동일한 연결 수준 보안을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="f32b2-120">gRPC 클라이언트 TLS(전송 계층 보안)는 gRPC 채널을 만들 때 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="f32b2-121">gRPC 클라이언트는 서비스를 호출할 때와 채널 및 서비스의 연결 수준 보안이 일치하지 않을 때 오류를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="f32b2-122">TLS를 사용하도록 gRPC 채널을 구성하려면 서버 주소가 `https`로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="f32b2-123">예를 들어 `GrpcChannel.ForAddress("https://localhost:5001")`는 HTTPS 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="f32b2-124">gRPC 채널은 TLS로 보호되는 연결을 자동으로 협상하고 보안 연결을 사용하여 gRPC 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="f32b2-125">gRPC는 TLS를 통한 클라이언트 인증서 인증을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="f32b2-126">gRPC 채널을 사용하여 클라이언트 인증서를 구성하는 방법에 대한 내용은 <xref:grpc/authn-and-authz#client-certificate-authentication>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f32b2-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="f32b2-127">보안이 설정되지 않은 gRPC 서비스를 호출하려면 서버 주소가 `http`로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="f32b2-128">예를 들어 `GrpcChannel.ForAddress("http://localhost:5000")`는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="f32b2-129">.NET Core 3.1 이상에서 [.NET 클라이언트를 사용하여 안전하지 않은 gRPC 서비스를 호출](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="f32b2-130">클라이언트 성능</span><span class="sxs-lookup"><span data-stu-id="f32b2-130">Client performance</span></span>

<span data-ttu-id="f32b2-131">채널 및 클라이언트 성능과 사용법:</span><span class="sxs-lookup"><span data-stu-id="f32b2-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="f32b2-132">채널을 만드는 작업은 비용이 많이 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="f32b2-133">gRPC 호출에 채널을 재사용하면 성능 혜택이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="f32b2-134">gRPC 클라이언트는 채널을 사용하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="f32b2-135">gRPC 클라이언트는 경량 개체이며 캐시하거나 재사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="f32b2-136">다양한 형식의 클라이언트를 포함하여 여러 gRPC 클라이언트를 한 채널에서 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="f32b2-137">채널 및 채널에서 만든 클라이언트를 여러 스레드에서 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="f32b2-138">채널에서 만든 클라이언트는 여러 개의 동시 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="f32b2-139">`GrpcChannel.ForAddress`가 gRPC 클라이언트를 만들기 위한 유일한 옵션은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="f32b2-140">ASP.NET Core 앱에서 gRPC 서비스를 호출하는 경우 [gRPC 클라이언트 팩터리 통합](xref:grpc/clientfactory)을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="f32b2-141">`HttpClientFactory`와 gRPC 통합은 중앙에서 gRPC 클라이언트를 만드는 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="f32b2-142">`Grpc.Net.Client`를 사용하여 HTTP/2를 통해 gRPC를 호출하는 기능은 현재 Xamarin에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="f32b2-143">향후 Xamarin 릴리스에서 HTTP/2 지원을 개선하기 위해 노력하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="f32b2-144">현재 실행 가능한 대체 방법은 [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 및 [gRPC-Web](xref:grpc/browser)입니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="f32b2-145">gRPC 호출 수행</span><span class="sxs-lookup"><span data-stu-id="f32b2-145">Make gRPC calls</span></span>

<span data-ttu-id="f32b2-146">클라이언트에서 메서드를 호출하여 gRPC 호출을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="f32b2-147">gRPC 클라이언트는 메시지 직렬화 및 gRPC 호출에 올바른 서비스 주소를 지정하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="f32b2-148">gRPC에는 다양한 형식의 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-148">gRPC has different types of methods.</span></span> <span data-ttu-id="f32b2-149">클라이언트를 사용하여 gRPC 호출을 수행하는 방법은 호출되는 메서드 형식에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="f32b2-150">gRPC 메서드 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-150">The gRPC method types are:</span></span>

* <span data-ttu-id="f32b2-151">단항</span><span class="sxs-lookup"><span data-stu-id="f32b2-151">Unary</span></span>
* <span data-ttu-id="f32b2-152">서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="f32b2-152">Server streaming</span></span>
* <span data-ttu-id="f32b2-153">클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="f32b2-153">Client streaming</span></span>
* <span data-ttu-id="f32b2-154">양방향 스트리밍</span><span class="sxs-lookup"><span data-stu-id="f32b2-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="f32b2-155">단항 호출</span><span class="sxs-lookup"><span data-stu-id="f32b2-155">Unary call</span></span>

<span data-ttu-id="f32b2-156">단항 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="f32b2-157">서비스가 완료되면 응답 메시지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="f32b2-158">\* *.proto* 파일에 있는 각 단항 서비스 메서드는 구체적인 gRPC 클라이언트 형식에 해당 메서드를 호출하기 위한 두 가지 .NET 메서드인 비동기 메서드와 차단 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="f32b2-159">예를 들어 `GreeterClient`에서 `SayHello`를 호출하는 방법에는 다음 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="f32b2-160">`GreeterClient.SayHelloAsync` - `Greeter.SayHello` 서비스를 비동기적으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="f32b2-161">대기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-161">Can be awaited.</span></span>
* <span data-ttu-id="f32b2-162">`GreeterClient.SayHello` - `Greeter.SayHello` 서비스를 호출하고 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="f32b2-163">비동기 코드에서는 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="f32b2-164">서버 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="f32b2-164">Server streaming call</span></span>

<span data-ttu-id="f32b2-165">서버 스트리밍 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="f32b2-166">`ResponseStream.MoveNext()`는 서비스에서 스트리밍된 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="f32b2-167">`ResponseStream.MoveNext()`에서 `false`를 반환하면 서버 스트리밍 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="f32b2-168">C# 8 이상을 사용하는 경우 `await foreach` 구문을 사용하여 메시지를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="f32b2-169">`IAsyncStreamReader<T>.ReadAllAsync()` 확장 메서드는 응답 스트림에서 모든 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="f32b2-170">클라이언트 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="f32b2-170">Client streaming call</span></span>

<span data-ttu-id="f32b2-171">클라이언트 스트리밍 호출은 클라이언트에서 메시지를 보내지 ‘않고’ 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="f32b2-172">클라이언트는 `RequestStream.WriteAsync`를 사용하여 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="f32b2-173">클라이언트가 메시지 전송을 완료하면 서비스에 알리기 위해 `RequestStream.CompleteAsync()`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="f32b2-174">서비스에서 응답 메시지를 반환하면 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-174">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="f32b2-175">양방향 스트리밍 호출</span><span class="sxs-lookup"><span data-stu-id="f32b2-175">Bi-directional streaming call</span></span>

<span data-ttu-id="f32b2-176">양방향 스트리밍 호출은 클라이언트에서 메시지를 보내지 ‘않고’ 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="f32b2-177">클라이언트는 `RequestStream.WriteAsync`를 사용하여 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="f32b2-178">서비스에서 스트리밍된 메시지는 `ResponseStream.MoveNext()` 또는 `ResponseStream.ReadAllAsync()`를 사용하여 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="f32b2-179">`ResponseStream`에 더는 메시지가 없으면 양방향 스트리밍 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="f32b2-180">최적의 성능을 얻고 클라이언트 및 서비스에서 불필요한 오류를 방지하려면 양방향 스트리밍 호출을 정상적으로 완료해 보세요.</span><span class="sxs-lookup"><span data-stu-id="f32b2-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="f32b2-181">서버에서 요청 스트림 읽기를 마치고 클라이언트에서 응답 스트림 읽기를 완료하면 양방향 호출이 정상적으로 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="f32b2-182">위의 샘플 호출은 정상적으로 종료되는 양방향 호출의 한 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="f32b2-183">호출에서 클라이언트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-183">In the call, the client:</span></span>

1. <span data-ttu-id="f32b2-184">`EchoClient.Echo`를 호출하여 새 양방향 스트리밍 호출을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="f32b2-185">`ResponseStream.ReadAllAsync()`를 사용하여 서비스에서 메시지를 읽는 백그라운드 작업을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="f32b2-186">`RequestStream.WriteAsync`를 사용하여 메시지를 서버에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="f32b2-187">`RequestStream.CompleteAsync()`를 사용하여 메시지를 보냈다고 서버에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="f32b2-188">백그라운드 작업에서 들어오는 모든 메시지를 읽을 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="f32b2-189">양방향 스트리밍 호출 중에 클라이언트와 서비스는 언제든지 서로에게 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="f32b2-190">양방향 호출과 상호 작용하는 데 가장 적합한 클라이언트 논리는 서비스 논리에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="f32b2-191">gRPC 트레일러 액세스</span><span class="sxs-lookup"><span data-stu-id="f32b2-191">Access gRPC trailers</span></span>

<span data-ttu-id="f32b2-192">gRPC 호출은 gRPC 트레일러를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="f32b2-193">gRPC 트레일러는 호출에 대한 이름/값 메타데이터를 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="f32b2-194">트레일러는 HTTP 헤더와 유사한 기능을 제공하지만 호출이 끝날 때 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="f32b2-195">gRPC 트레일러는 메타데이터의 컬렉션을 반환하는 `GetTrailers()`를 사용하여 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="f32b2-196">응답이 완료된 후 트레일러가 반환되므로 모든 응답 메시지를 대기한 이후에 트레일러에 액세스해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="f32b2-197">단항 및 클라이언트 스트리밍 호출은 `GetTrailers()`를 호출하기 전에 `ResponseAsync`를 대기해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="f32b2-198">서버 및 양방향 스트리밍 호출은 `GetTrailers()`를 호출하기 전에 응답 스트림 대기를 완료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="f32b2-199">`RpcException`에서도 gRPC 트레일러에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="f32b2-200">서비스에서 비정상 gRPC 상태와 함께 트레일러를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="f32b2-201">이 경우에는 gRPC 클라이언트에서 throw된 예외에서 트레일러가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="f32b2-202">최종 기한 구성</span><span class="sxs-lookup"><span data-stu-id="f32b2-202">Configure deadline</span></span>

<span data-ttu-id="f32b2-203">gRPC 호출 최종 기한 구성은 호출을 실행할 수 있는 기간에 대한 상한을 제공하기 때문에 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="f32b2-204">또한 오작동하는 서비스가 계속 실행되어 서버 리소스가 소진되는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="f32b2-205">최종 기한은 안정적인 앱을 빌드하는 데 유용한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="f32b2-206">`CallOptions.Deadline`을 구성하여 gRPC 호출의 최종 기한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f32b2-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="f32b2-207">자세한 내용은 <xref:grpc/deadlines-cancellation#deadlines>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f32b2-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f32b2-208">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f32b2-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
