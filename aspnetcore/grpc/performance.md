---
title: gRPC 관련 성능 모범 사례
author: jamesnk
description: 고성능 gRPC 서비스를 빌드하기 위한 모범 사례를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060406"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="ec90d-103">gRPC 관련 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="ec90d-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="ec90d-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ec90d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="ec90d-105">gRPC는 고성능 서비스를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="ec90d-106">이 문서에서는 gRPC에서 가능한 최상의 성능을 얻는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="ec90d-107">gRPC 채널 다시 사용</span><span class="sxs-lookup"><span data-stu-id="ec90d-107">Reuse gRPC channels</span></span>

<span data-ttu-id="ec90d-108">gRPC 호출 시 gRPC 채널을 재사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="ec90d-109">채널을 재사용하면 기존 HTTP/2 연결을 통해 호출이 멀티플렉싱될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="ec90d-110">gRPC 호출별로 새 채널을 생성하는 경우 완료하는 데 상당히 오랜 시간이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="ec90d-111">호출할 때마다 새 HTTP/2 연결을 만들기 위해 클라이언트와 서버 간에 여러 번의 네트워크 왕복이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="ec90d-112">소켓 열기</span><span class="sxs-lookup"><span data-stu-id="ec90d-112">Opening a socket</span></span>
2. <span data-ttu-id="ec90d-113">TCP 연결 설정</span><span class="sxs-lookup"><span data-stu-id="ec90d-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="ec90d-114">TLS 협상</span><span class="sxs-lookup"><span data-stu-id="ec90d-114">Negotiating TLS</span></span>
4. <span data-ttu-id="ec90d-115">HTTP/2 연결 시작</span><span class="sxs-lookup"><span data-stu-id="ec90d-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="ec90d-116">gRPC 호출하기</span><span class="sxs-lookup"><span data-stu-id="ec90d-116">Making the gRPC call</span></span>

<span data-ttu-id="ec90d-117">gRPC 호출 간에 채널을 공유하고 재사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="ec90d-118">gRPC 클라이언트는 채널을 사용하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="ec90d-119">gRPC 클라이언트는 경량 개체이며 캐시하거나 재사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="ec90d-120">다양한 형식의 클라이언트를 포함하여 여러 gRPC 클라이언트를 한 채널에서 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="ec90d-121">채널 및 채널에서 만든 클라이언트를 여러 스레드에서 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="ec90d-122">채널에서 만든 클라이언트는 여러 개의 동시 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="ec90d-123">gRPC 클라이언트 팩터리는 중앙 집중식으로 채널을 구성하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="ec90d-124">자동으로 기본 채널을 다시 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="ec90d-125">자세한 내용은 <xref:grpc/clientfactory>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ec90d-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="ec90d-126">연결 동시성</span><span class="sxs-lookup"><span data-stu-id="ec90d-126">Connection concurrency</span></span>

<span data-ttu-id="ec90d-127">일반적으로 HTTP/2 연결에는 하나의 연결에서 한 번에 사용 가능한 [최대 동시 스트림(활성 HTTP 요청)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) 수에 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="ec90d-128">기본적으로 대부분의 서버는 이 한도를 100개의 동시 스트림으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="ec90d-129">gRPC 채널은 단일 HTTP/2 연결을 사용하고, 해당 연결에서 동시 호출이 멀티플렉싱됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="ec90d-130">활성 호출 수가 연결 스트림 한도에 도달하면 추가 호출은 클라이언트에서 큐에 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="ec90d-131">큐에 대기 중인 호출은 활성 호출이 완료될 때까지 대기한 후 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="ec90d-132">부하가 높은 애플리케이션이나 장기 스트리밍 gRPC 호출에서는 이 한도로 인해 큐에 대기 중인 호출이 야기하는 성능 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ec90d-133">.NET 5에는 `SocketsHttpHandler.EnableMultipleHttp2Connections` 속성이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="ec90d-134">`true`로 설정하면 동시 스트림 한도에 도달하는 경우 채널에서 추가 HTTP/2 연결이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="ec90d-135">`GrpcChannel`이 만들어지면 내부 `SocketsHttpHandler`가 추가 HTTP/2 연결을 만들도록 자동으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="ec90d-136">앱이 고유한 처리기를 구성하는 경우에는 `EnableMultipleHttp2Connections`를 `true`로 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="ec90d-137">.NET Core 3.1 앱을 위한 몇 가지 해결 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="ec90d-138">부하가 높은 앱 영역에 대해 별도의 gRPC 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="ec90d-139">예를 들어 `Logger` gRPC 서비스의 부하가 높을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="ec90d-140">별도의 채널을 사용하여 앱에서 `LoggerClient`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="ec90d-141">gRPC 채널의 풀을 사용합니다(예: gRPC 채널 목록 만들기).</span><span class="sxs-lookup"><span data-stu-id="ec90d-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="ec90d-142">`Random`은 gRPC 채널이 필요할 때마다 목록에서 채널을 선택하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="ec90d-143">`Random`을 사용하면 호출이 여러 연결에서 무작위로 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ec90d-144">서버에 대한 최대 동시 스트림 한도를 늘리는 것은 이 문제를 해결하는 또 다른 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="ec90d-145">Kestrel에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="ec90d-146">최대 동시 스트림 한도를 늘리지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="ec90d-147">단일 HTTP/2 연결에서 스트림이 너무 많으면 새로운 성능 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="ec90d-148">연결에 쓰려고 시도하는 스트림 간에 스레드 경합이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="ec90d-149">연결 패킷 손실로 인해 TCP 계층에서 모든 호출이 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="ec90d-150">부하 분산</span><span class="sxs-lookup"><span data-stu-id="ec90d-150">Load balancing</span></span>

<span data-ttu-id="ec90d-151">일부 부하 분산 장치는 gRPC에서 효과적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="ec90d-152">L4(전송) 부하 분산 장치는 엔드포인트 간에 TCP 연결을 분산하여 연결 수준에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="ec90d-153">이 접근 방식은 HTTP/1.1을 사용하여 수행된 API 호출의 부하를 분산하는 데 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="ec90d-154">HTTP/1.1을 사용하여 수행된 동시 호출은 여러 연결에서 전송되므로 엔드포인트에서 호출의 부하를 분산할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="ec90d-155">L4 부하 분산 장치는 연결 수준에서 작동하므로 gRPC에서 제대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="ec90d-156">gRPC는 단일 TCP 연결에서 여러 호출을 멀티플렉싱하는 HTTP/2를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="ec90d-157">해당 연결을 통한 모든 gRPC 호출은 하나의 엔드포인트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="ec90d-158">gRPC의 부하를 효과적으로 분산하는 두 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="ec90d-159">클라이언트 쪽 부하 분산</span><span class="sxs-lookup"><span data-stu-id="ec90d-159">Client-side load balancing</span></span>
* <span data-ttu-id="ec90d-160">L7(애플리케이션) 프록시 부하 분산</span><span class="sxs-lookup"><span data-stu-id="ec90d-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="ec90d-161">엔드포인트 간에 gRPC 호출의 부하만 분산할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="ec90d-162">스트리밍 gRPC 호출이 설정되면 스트림을 통해 전송되는 모든 메시지가 하나의 엔드포인트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="ec90d-163">클라이언트 쪽 부하 분산</span><span class="sxs-lookup"><span data-stu-id="ec90d-163">Client-side load balancing</span></span>

<span data-ttu-id="ec90d-164">클라이언트 쪽 부하 분산을 사용하면 클라이언트가 엔드포인트를 인식합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="ec90d-165">각 gRPC 호출을 위해 클라이언트는 호출을 전송할 다른 엔드포인트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="ec90d-166">클라이언트 쪽 부하 분산은 대기 시간이 중요한 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="ec90d-167">클라이언트와 서비스 간에 프록시가 없으므로 호출이 서비스로 직접 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="ec90d-168">클라이언트 쪽 부하 분산의 단점은 각 클라이언트가 사용해야 하는 사용 가능한 엔드포인트를 추적해야 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="ec90d-169">할당 준비 클라이언트 부하 분산은 부하 분산 상태가 중앙 위치에 저장되는 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="ec90d-170">클라이언트는 부하 분산을 결정할 때 사용할 정보를 중앙 위치에서 주기적으로 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="ec90d-171">`Grpc.Net.Client`는 현재 클라이언트 쪽 부하 분산을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="ec90d-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core)는 .NET에서 클라이언트 쪽 부하 분산이 필요한 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="ec90d-173">프록시 부하 분산</span><span class="sxs-lookup"><span data-stu-id="ec90d-173">Proxy load balancing</span></span>

<span data-ttu-id="ec90d-174">L7(애플리케이션) 프록시는 L4(전송) 프록시보다 더 높은 수준에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="ec90d-175">L7 프록시는 HTTP/2를 해석하며 하나의 HTTP/2 연결을 통해 프록시로 멀티플렉싱된 gRPC 호출을 여러 엔드포인트에서 분산할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="ec90d-176">프록시 사용은 클라이언트 쪽 부하 분산보다 더 간단하지만 gRPC 호출에 대기 시간을 더 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="ec90d-177">많은 L7 프록시를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-177">There are many L7 proxies available.</span></span> <span data-ttu-id="ec90d-178">몇 가지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-178">Some options are:</span></span>

* <span data-ttu-id="ec90d-179">[Envoy](https://www.envoyproxy.io/) - 인기 있는 오픈 소스 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="ec90d-180">[Linkerd](https://linkerd.io/) - Kubernetes용 서비스 메시입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="ec90d-181">[YARP: 역방향 프록시](https://microsoft.github.io/reverse-proxy/) - .NET으로 작성된 미리 보기 오픈 소스 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="ec90d-182">프로세스 간 통신</span><span class="sxs-lookup"><span data-stu-id="ec90d-182">Inter-process communication</span></span>

<span data-ttu-id="ec90d-183">클라이언트와 서비스 간의 gRPC 호출은 일반적으로 TCP 소켓을 통해 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="ec90d-184">TCP는 네트워크를 통해 통신하는 데 유용하지만, 클라이언트와 서비스가 동일한 머신에 있는 경우에는 [IPC(프로세스 간 통신)](https://wikipedia.org/wiki/Inter-process_communication)가 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="ec90d-185">동일한 머신에 있는 프로세스 간 gRPC 호출에는 Unix 도메인 소켓이나 명명된 파이프와 같은 전송을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="ec90d-186">자세한 내용은 <xref:grpc/interprocess>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ec90d-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="ec90d-187">연결 유지 ping</span><span class="sxs-lookup"><span data-stu-id="ec90d-187">Keep alive pings</span></span>

<span data-ttu-id="ec90d-188">연결 유지 ping을 사용하여 비활성 기간 동안 HTTP/2 연결을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="ec90d-189">앱이 작업을 계속할 때 기존 HTTP/2 연결을 곧바로 사용할 수 있으면 연결을 다시 설정하느라 지연이 발생하지 않고 초기 gRPC 호출을 신속하게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="ec90d-190">연결 유지 ping은 <xref:System.Net.Http.SocketsHttpHandler>에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="ec90d-191">앞의 코드는 비활성 기간 동안 60초마다 연결 유지 ping을 서버에 보내는 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="ec90d-192">ping을 사용하면 서버와 사용 중인 모든 프록시가 비활성 상태로 인해 연결을 닫지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="ec90d-193">스트리밍</span><span class="sxs-lookup"><span data-stu-id="ec90d-193">Streaming</span></span>

<span data-ttu-id="ec90d-194">gRPC 양방향 스트리밍을 사용하면 고성능 시나리오에서 단항 gRPC 호출을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="ec90d-195">양방향 스트림이 시작된 후에는 메시지를 스트리밍하는 것이 여러 개의 단항 gRPC 호출을 통해 메시지를 보내는 것보다 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="ec90d-196">스트리밍된 메시지는 기존 HTTP/2 요청에서 데이터로 전송되며, 단항 호출별로 새 HTTP/2 요청을 만드는 오버헤드가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="ec90d-197">예제 서비스:</span><span class="sxs-lookup"><span data-stu-id="ec90d-197">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="ec90d-198">클라이언트 예:</span><span class="sxs-lookup"><span data-stu-id="ec90d-198">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="ec90d-199">성능상의 이유로 단항 호출을 양방향 스트리밍으로 대체하는 것은 고급 기법이며 많은 상황에서 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="ec90d-200">스트리밍 호출을 사용하는 것은 다음과 같은 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="ec90d-201">높은 처리량이나 짧은 대기 시간이 필요한 경우</span><span class="sxs-lookup"><span data-stu-id="ec90d-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="ec90d-202">gRPC 및 HTTP/2가 성능 병목 상태로 식별된 경우</span><span class="sxs-lookup"><span data-stu-id="ec90d-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="ec90d-203">클라이언트의 작업자가 gRPC 서비스를 사용하여 일반 메시지를 보내거나 받는 경우</span><span class="sxs-lookup"><span data-stu-id="ec90d-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="ec90d-204">단항 대신 스트리밍 호출을 사용할 때 추가 복잡성과 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="ec90d-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="ec90d-205">서비스 또는 연결 오류로 인해 스트림이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="ec90d-206">오류가 발생하는 경우 스트림을 다시 시작하기 위한 논리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="ec90d-207">`RequestStream.WriteAsync`는 다중 스레딩에 안전하게 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="ec90d-208">한 번에 하나의 메시지만 스트림에 쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="ec90d-209">여러 스레드의 메시지를 단일 스트림을 통해 보내려면 <xref:System.Threading.Channels.Channel%601> 같은 생산자/소비자 큐가 메시지를 마샬링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="ec90d-210">gRPC 스트리밍 방법은 한 유형의 메시지를 수신하고 한 유형의 메시지 유형을 보내는 것으로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="ec90d-211">예를 들어 `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)`는 `RequestMessage`를 수신하고 `ResponseMessage`를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="ec90d-212">Protobuf가 `Any` 및 `oneof`를 사용하여 알 수 없는 메시지나 조건부 메시지를 지원하면 이러한 제한을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ec90d-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
