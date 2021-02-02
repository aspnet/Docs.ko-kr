---
title: gRPC 서비스와 HTTP API 비교
author: jamesnk
description: gRPC와 HTTP API를 비교한 방법과 권장 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
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
uid: grpc/comparison
ms.openlocfilehash: 1ec553d54a9cad170cb322bc186bb67ac8bbded4
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658731"
---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="43ae1-103">gRPC 서비스와 HTTP API 비교</span><span class="sxs-lookup"><span data-stu-id="43ae1-103">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="43ae1-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="43ae1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="43ae1-105">이 문서에서는 [gRPC 서비스](https://grpc.io/docs/guides/)와 JSON을 사용한 HTTP API(ASP.NET Core [Web API](xref:web-api/index) 포함)를 비교하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-105">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="43ae1-106">앱에 대한 API를 제공하는 데 사용되는 기술은 중요한 선택 이며 gRPC는 HTTP API와 비교해서 고유한 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-106">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="43ae1-107">이 문서에서는 gRPC의 장점과 단점을 설명하 고 다른 기술에서 gRPC를 사용하는 시나리오를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-107">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="43ae1-108">개괄적인 비교</span><span class="sxs-lookup"><span data-stu-id="43ae1-108">High-level comparison</span></span>

<span data-ttu-id="43ae1-109">다음 표에서는 gRPC와 JSON을 사용하는 HTTP API 간의 고급 기능 비교를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-109">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="43ae1-110">기능</span><span class="sxs-lookup"><span data-stu-id="43ae1-110">Feature</span></span>          | <span data-ttu-id="43ae1-111">gRPC</span><span class="sxs-lookup"><span data-stu-id="43ae1-111">gRPC</span></span>                                               | <span data-ttu-id="43ae1-112">JSON을 사용하는 HTTP API</span><span class="sxs-lookup"><span data-stu-id="43ae1-112">HTTP APIs with JSON</span></span>           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| <span data-ttu-id="43ae1-113">계약</span><span class="sxs-lookup"><span data-stu-id="43ae1-113">Contract</span></span>         | <span data-ttu-id="43ae1-114">필수( *.proto*)</span><span class="sxs-lookup"><span data-stu-id="43ae1-114">Required (*.proto*)</span></span>                                | <span data-ttu-id="43ae1-115">선택 사항(OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="43ae1-115">Optional (OpenAPI)</span></span>            |
| <span data-ttu-id="43ae1-116">프로토콜</span><span class="sxs-lookup"><span data-stu-id="43ae1-116">Protocol</span></span>         | <span data-ttu-id="43ae1-117">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="43ae1-117">HTTP/2</span></span>                                             | <span data-ttu-id="43ae1-118">HTTP</span><span class="sxs-lookup"><span data-stu-id="43ae1-118">HTTP</span></span>                          |
| <span data-ttu-id="43ae1-119">Payload</span><span class="sxs-lookup"><span data-stu-id="43ae1-119">Payload</span></span>          | <span data-ttu-id="43ae1-120">[Protobuf](#performance)(소형, 이진)</span><span class="sxs-lookup"><span data-stu-id="43ae1-120">[Protobuf (small, binary)](#performance)</span></span>           | <span data-ttu-id="43ae1-121">JSON(대형, 사람이 읽을 수 있음)</span><span class="sxs-lookup"><span data-stu-id="43ae1-121">JSON (large, human readable)</span></span>  |
| <span data-ttu-id="43ae1-122">규범</span><span class="sxs-lookup"><span data-stu-id="43ae1-122">Prescriptiveness</span></span> | [<span data-ttu-id="43ae1-123">엄격한 사양</span><span class="sxs-lookup"><span data-stu-id="43ae1-123">Strict specification</span></span>](#strict-specification)      | <span data-ttu-id="43ae1-124">느슨함.</span><span class="sxs-lookup"><span data-stu-id="43ae1-124">Loose.</span></span> <span data-ttu-id="43ae1-125">모든 HTTP가 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-125">Any HTTP is valid.</span></span>     |
| <span data-ttu-id="43ae1-126">스트리밍</span><span class="sxs-lookup"><span data-stu-id="43ae1-126">Streaming</span></span>        | [<span data-ttu-id="43ae1-127">클라이언트, 서버, 양방향</span><span class="sxs-lookup"><span data-stu-id="43ae1-127">Client, server, bi-directional</span></span>](#streaming)       | <span data-ttu-id="43ae1-128">클라이언트, 서버</span><span class="sxs-lookup"><span data-stu-id="43ae1-128">Client, server</span></span>                |
| <span data-ttu-id="43ae1-129">브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="43ae1-129">Browser support</span></span>  | [<span data-ttu-id="43ae1-130">아니요(gRPC-웹 필요)</span><span class="sxs-lookup"><span data-stu-id="43ae1-130">No (requires grpc-web)</span></span>](#limited-browser-support) | <span data-ttu-id="43ae1-131">예</span><span class="sxs-lookup"><span data-stu-id="43ae1-131">Yes</span></span>                           |
| <span data-ttu-id="43ae1-132">보안</span><span class="sxs-lookup"><span data-stu-id="43ae1-132">Security</span></span>         | <span data-ttu-id="43ae1-133">전송(TLS)</span><span class="sxs-lookup"><span data-stu-id="43ae1-133">Transport (TLS)</span></span>                                    | <span data-ttu-id="43ae1-134">전송(TLS)</span><span class="sxs-lookup"><span data-stu-id="43ae1-134">Transport (TLS)</span></span>               |
| <span data-ttu-id="43ae1-135">클라이언트 코드 생성</span><span class="sxs-lookup"><span data-stu-id="43ae1-135">Client code-generation</span></span> | [<span data-ttu-id="43ae1-136">예</span><span class="sxs-lookup"><span data-stu-id="43ae1-136">Yes</span></span>](#code-generation)                      | <span data-ttu-id="43ae1-137">OpenAPI + 타사 도구</span><span class="sxs-lookup"><span data-stu-id="43ae1-137">OpenAPI + third-party tooling</span></span> |

## <a name="grpc-strengths"></a><span data-ttu-id="43ae1-138">gRPC 장점</span><span class="sxs-lookup"><span data-stu-id="43ae1-138">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="43ae1-139">성능</span><span class="sxs-lookup"><span data-stu-id="43ae1-139">Performance</span></span>

<span data-ttu-id="43ae1-140">gRPC 메시지는 효율적인 이진 메시지 형식인 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)를 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-140">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="43ae1-141">Protobuf는 서버와 클라이언트에서 매우 빠르게 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-141">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="43ae1-142">Protobuf serialization은 작은 메시지 페이로드를 발생시키며 이는 모바일 앱과 같은 제한된 대역폭 시나리오에서 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-142">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="43ae1-143">gRPC는 HTTP 1.x에 비해 상당한 성능 이점을 제공하는, HTTP의 주요 개정판인 HTTP/2용으로 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-143">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="43ae1-144">이진 프레이밍 및 압축.</span><span class="sxs-lookup"><span data-stu-id="43ae1-144">Binary framing and compression.</span></span> <span data-ttu-id="43ae1-145">HTTP/2 프로토콜은 간단하며, 보내고 받을 때 모두 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-145">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="43ae1-146">단일 TCP 연결보다 여러 HTTP/2 호출의 멀티플렉싱.</span><span class="sxs-lookup"><span data-stu-id="43ae1-146">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="43ae1-147">멀티플렉싱은 [HOL(Head of Line) 차단](https://en.wikipedia.org/wiki/Head-of-line_blocking)을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-147">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="43ae1-148">HTTP/2는 gRPC에만 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-148">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="43ae1-149">JSON을 사용한 HTTP API를 포함하여 다양한 요청 형식에서 HTTP/2를 사용하고 성능 개선을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-149">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="43ae1-150">코드 생성</span><span class="sxs-lookup"><span data-stu-id="43ae1-150">Code generation</span></span>

<span data-ttu-id="43ae1-151">모든 gRPC 프레임워크는 코드 생성에 대한 최고 수준의 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-151">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="43ae1-152">gRPC 개발의 핵심 파일은 gRPC 서비스 및 메시지의 계약을 정의하는 [`.proto` 파일](https://developers.google.com/protocol-buffers/docs/proto3)입니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-152">A core file to gRPC development is the [`.proto` file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="43ae1-153">gRPC 프레임워크는 이 파일에서 서비스 기본 클래스, 메시지, 전체 클라이언트를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-153">From this file, gRPC frameworks generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="43ae1-154">서버와 클라이언트 간에 *proto* 파일을 공유하여 메시지와 클라이언트 코드를 종단 간에 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-154">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="43ae1-155">클라이언트의 코드 생성은 클라이언트와 서버에서 메시지의 중복을 제거하고 강력한 형식의 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-155">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="43ae1-156">클라이언트를 작성하지 않아도 되므로 많은 서비스를 갖춘 응용 프로그램의 개발 시간이 상당히 절감됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-156">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="43ae1-157">엄격한 사양</span><span class="sxs-lookup"><span data-stu-id="43ae1-157">Strict specification</span></span>

<span data-ttu-id="43ae1-158">JSON을 사용하는 HTTP API에 대한 공식 사양은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-158">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="43ae1-159">개발자는 URL, HTTP 동사 및 응답 코드의 가장 좋은 형식을 논의합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-159">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="43ae1-160">[gRPC 사양](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)은 gRPC 서비스가 따라야 하는 형식에 대한 지침입니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-160">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="43ae1-161">gRPC는 플랫폼 및 구현에 상관없이 일치하므로 논쟁이 불필요하며 개발자 시간을 절약합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-161">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="43ae1-162">스트리밍</span><span class="sxs-lookup"><span data-stu-id="43ae1-162">Streaming</span></span>

<span data-ttu-id="43ae1-163">HTTP/2는 수명이 긴 실시간 통신 스트림에 대한 기초를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-163">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="43ae1-164">gRPC는 HTTP/2를 통한 스트리밍을 위한 최고 수준의 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-164">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="43ae1-165">gRPC 서비스는 모든 스트리밍 조합을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-165">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="43ae1-166">단항(스트리밍 없음)</span><span class="sxs-lookup"><span data-stu-id="43ae1-166">Unary (no streaming)</span></span>
* <span data-ttu-id="43ae1-167">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="43ae1-167">Server to client streaming</span></span>
* <span data-ttu-id="43ae1-168">클라이언트-서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="43ae1-168">Client to server streaming</span></span>
* <span data-ttu-id="43ae1-169">양방향 스트리밍</span><span class="sxs-lookup"><span data-stu-id="43ae1-169">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="43ae1-170">최종 기한/시간 초과 및 취소</span><span class="sxs-lookup"><span data-stu-id="43ae1-170">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="43ae1-171">gRPC는 클라이언트가 RPC가 완료될 때까지 대기하는 기간을 지정하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-171">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="43ae1-172">[최종 기한](https://grpc.io/blog/deadlines)이 서버에 전송되고 서버에서 최종 기한을 초과하는 경우 수행할 작업을 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-172">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="43ae1-173">예를 들어 서버에서 제한 시간에 진행 중인 gRPC/HTTP/데이터베이스 요청을 취소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-173">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="43ae1-174">자식 gRPC 호출을 통해 최종 기한 및 취소를 전파하면 리소스 사용 제한을 강제로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-174">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="43ae1-175">gRPC 권장 시나리오</span><span class="sxs-lookup"><span data-stu-id="43ae1-175">gRPC recommended scenarios</span></span>

<span data-ttu-id="43ae1-176">gRPC는 다음과 같은 시나리오에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-176">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="43ae1-177">**마이크로 서비스**: gRPC는 대기 시간이 짧고 처리량이 높은 통신을 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-177">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="43ae1-178">gRPC는 효율성이 중요한 경량 마이크로 서비스에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-178">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="43ae1-179">**지점 간 실시간 통신**: 양방향 스트리밍을 위한 뛰어난 지원 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-179">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="43ae1-180">gRPC 서비스는 폴링을 사용하지 않고 실시간으로 메시지를 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-180">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="43ae1-181">**Polyglot 환경**: gRPC 도구는 널리 사용되는 모든 개발 언어를 지원하며, 따라서 gRPC는 다중 언어 환경에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-181">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="43ae1-182">**네트워크 제한 환경**: gRPC 메시지는 경량 메시지 형식인 Protobuf를 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-182">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="43ae1-183">gRPC 메시지는 항상 해당하는 JSON 메시지보다 작습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-183">A gRPC message is always smaller than an equivalent JSON message.</span></span>
* <span data-ttu-id="43ae1-184">**IPC(프로세스 간 통신)** : Unix 도메인 소켓 및 명명된 파이프와 같은 IPC 전송은 gRPC에서 동일한 머신에 있는 앱 간에 통신하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-184">**Inter-process communication (IPC)**: IPC transports such as Unix domain sockets and named pipes can be used with gRPC to communicate between apps on the same machine.</span></span> <span data-ttu-id="43ae1-185">자세한 내용은 <xref:grpc/interprocess>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43ae1-185">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="43ae1-186">gRPC 약점</span><span class="sxs-lookup"><span data-stu-id="43ae1-186">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="43ae1-187">제한된 브라우저 지원</span><span class="sxs-lookup"><span data-stu-id="43ae1-187">Limited browser support</span></span>

<span data-ttu-id="43ae1-188">현재 브라우저에서 gRPC 서비스를 직접 호출하는 것은 불가능합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-188">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="43ae1-189">gRPC는 HTTP/2 기능을 많이 사용하며, 브라우저에서 gRPC 클라이언트를 지원하기 위해 웹 요청에 필요한 제어 수준을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-189">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="43ae1-190">예를 들어, 브라우저는 호출자가 HTTP/2를 사용하도록 요구하거나 기본 HTTP/2 프레임에 대한 액세스를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-190">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="43ae1-191">다음과 같은 두 가지 일반적인 방법으로 gRPC를 브라우저 앱으로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-191">There are two common approaches to bring gRPC to browser apps:</span></span>

* <span data-ttu-id="43ae1-192">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html)은 브라우저에서 gRPC 지원을 제공하는 gRPC 팀의 추가 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-192">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides gRPC support in the browser.</span></span> <span data-ttu-id="43ae1-193">gRPC-Web을 사용하면 브라우저 앱에서 gRPC의 고성능과 낮은 네트워크 사용량을 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-193">gRPC-Web allows browser apps to benefit from the high-performance and low network usage of gRPC.</span></span> <span data-ttu-id="43ae1-194">gRPC-웹에서 모든 gRPC 기능을 지원하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-194">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="43ae1-195">클라이언트 및 양방향 스트리밍이 지원되지 않으며 서버 스트리밍이 제한적으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-195">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

  <span data-ttu-id="43ae1-196">.NET Core는 gRPC-Web을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-196">.NET Core has support for gRPC-Web.</span></span> <span data-ttu-id="43ae1-197">자세한 내용은 <xref:grpc/browser>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43ae1-197">For more information, see <xref:grpc/browser>.</span></span>

* <span data-ttu-id="43ae1-198">RESTful JSON 웹 API는 [HTTP 메타데이터](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)로 *.proto* 파일에 주석을 달아 gRPC 서비스에서 자동으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-198">RESTful JSON Web APIs can be automatically created from gRPC services by annotating the *.proto* file with [HTTP metadata](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).</span></span> <span data-ttu-id="43ae1-199">따라서 앱은 gRPC와 JSON 웹 API 둘 다에 대해 별도의 서비스를 구축하는 노력을 들이지 않고도 둘 다를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-199">This allows an app to support both gRPC and JSON web APIs, without duplicating effort of building separate services for both.</span></span>

  <span data-ttu-id="43ae1-200">.NET Core는 gRPC 서비스에서 JSON 웹 API 만들기에 대한 실험적인 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-200">.NET Core has experimental support for creating JSON web APIs from gRPC services.</span></span> <span data-ttu-id="43ae1-201">자세한 내용은 <xref:grpc/httpapi>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="43ae1-201">For more information, see <xref:grpc/httpapi>.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="43ae1-202">사람이 읽을 수 없음</span><span class="sxs-lookup"><span data-stu-id="43ae1-202">Not human readable</span></span>

<span data-ttu-id="43ae1-203">HTTP API 요청은 텍스트로 전송되며, 사람이 읽고 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-203">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="43ae1-204">gRPC 메시지는 기본적으로 Protobuf로 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-204">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="43ae1-205">Protobuf는 송신 및 수신에 효율적이지만, 이진 형식은 사람이 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-205">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="43ae1-206">Protobuf를 사용하려면 올바른 역직렬화를 위해 *.proto* 파일에 지정된 메시지의 인터페이스 설명이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-206">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="43ae1-207">네트워크에서 Protobuf 페이로드를 분석하고 요청을 직접 작성하려면 추가 도구가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-207">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="43ae1-208">[서버 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) 및 [gRPC 명령줄 도구](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)와 같은 기능은 이진 Protobuf 메시지를 지원하기 위해 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-208">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="43ae1-209">또한 Protobuf 메시지는 [JSON 변환](https://developers.google.com/protocol-buffers/docs/proto3#json)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-209">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="43ae1-210">기본 제공 JSON 변환은 디버그할 때 Protobuf 메시지를 사람이 읽을 수 있는 형식으로 변환하는 효율적인 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-210">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="43ae1-211">대체 프레임워크 시나리오</span><span class="sxs-lookup"><span data-stu-id="43ae1-211">Alternative framework scenarios</span></span>

<span data-ttu-id="43ae1-212">다음과 같은 시나리오에서는 gRPC보다 다른 프레임워크가 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-212">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="43ae1-213">**브라우저에서 액세스 가능한 API**: gRPC는 브라우저에서 완전히 지원되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-213">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="43ae1-214">gRPC-웹은 브라우저 지원을 제공할 수 있지만 제한 사항이 있으며 서버 프록시를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-214">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="43ae1-215">**브로드캐스트 실시간 통신**: gRPC는 스트리밍을 통해 실시간 통신을 지원하지만 등록된 연결에 메시지를 브로드캐스트하는 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-215">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="43ae1-216">예를 들어 대화방의 모든 클라이언트에 새 채팅 메시지를 보내야 하는 대화방 시나리오에서 각 gRPC 호출은 클라이언트에 새 채팅 메시지를 개별적으로 스트리밍하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-216">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="43ae1-217">[SignalR](xref:signalr/introduction)은 이 시나리오에 유용한 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-217">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> <span data-ttu-id="43ae1-218">SignalR에는 영구 연결 개념과 메시지 브로드캐스트에 대한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43ae1-218">SignalR has the concept of persistent connections and built-in support for broadcasting messages.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43ae1-219">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="43ae1-219">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
