---
title: WCF를 ASP.NET Core gRPC로 마이그레이션하는 이유
author: markrendle
description: 이 문서에서는 최신 아키텍처 및 플랫폼으로 마이그레이션하려는 WCF(Windows Communication Foundation) 개발자에게 ASP.NET Core gRPC가 가장 적합한 이유를 간략하게 설명합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058690"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="9d8dd-103">WCF(Windows Communication Foundation) 개발자용 gRPC</span><span class="sxs-lookup"><span data-stu-id="9d8dd-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="9d8dd-104">이 문서에서는 최신 아키텍처 및 플랫폼으로 마이그레이션하려는 WCF(Windows Communication Foundation) 개발자에게 ASP.NET Core gRPC가 가장 적합한 이유를 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="9d8dd-105">WCF와 비교</span><span class="sxs-lookup"><span data-stu-id="9d8dd-105">Comparison to WCF</span></span>

<span data-ttu-id="9d8dd-106">gRPC의 구현 및 접근 방식은 다르지만 gRPC를 사용하여 서비스를 개발하고 사용하는 환경은 WCF 개발자에게 직관적이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="9d8dd-107">WCF 및 gRPC는 목표가 같은 RPC(원격 프로시저 호출) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="9d8dd-108">클라이언트와 서버가 동일한 플랫폼에 있는 것처럼 코딩할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="9d8dd-109">간소화된 이동식 네트워킹 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="9d8dd-110">인터페이스를 선언하는 프로세스는 다르지만 두 플랫폼 모두 인터페이스를 선언하고 구현하는 요구 사항을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="9d8dd-111">gRPC에서 지원하는 많은 형식의 RPC 호출은 WCF 서비스에 사용할 수 있는 바인딩에도 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="9d8dd-112">자세한 내용 및 예제는 [GRPC로 WCF 솔루션 마이그레이션](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="9d8dd-113">gRPC의 이점</span><span class="sxs-lookup"><span data-stu-id="9d8dd-113">Benefits of gRPC</span></span>

<span data-ttu-id="9d8dd-114">gRPC는 다음과 같은 이유로 다른 접근 방식보다 더 나은 프레임워크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="9d8dd-115">성능</span><span class="sxs-lookup"><span data-stu-id="9d8dd-115">Performance</span></span>

<span data-ttu-id="9d8dd-116">gRPC는 HTTP/2를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="9d8dd-117">HTTP/1.1과 달리 HTTP/2는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="9d8dd-118">더 작고 더 빠른 이진 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="9d8dd-119">컴퓨터에서 구문 분석하는 데 더욱 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="9d8dd-120">단일 연결에 대해 멀티플렉싱 요청을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="9d8dd-121">멀티플렉싱을 사용하면 서로를 차단하는 요청 없이 하나의 연결을 통해 여러 요청을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="9d8dd-122">HTTP/1.1에서는 차단을 “HOL(Head-Of-Line) 차단”이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="9d8dd-123">gRPC는 효율적인 이진 형식인 Protobuf를 사용하여 메시지를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="9d8dd-124">Protobuf 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-124">Protobuf messages are:</span></span>
* <span data-ttu-id="9d8dd-125">직렬화 및 역직렬화가 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="9d8dd-126">텍스트 기반 형식보다 더 적은 대역폭을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="9d8dd-127">gRPC는 대역폭 제한이 있는 모바일 디바이스 및 네트워크에 적합한 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="9d8dd-128">상호 운용성</span><span class="sxs-lookup"><span data-stu-id="9d8dd-128">Interoperability</span></span>

<span data-ttu-id="9d8dd-129">.NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby 및 PHP를 비롯한 모든 주요 프로그래밍 언어와 플랫폼에 대한 gRPC 도구 및 라이브러리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="9d8dd-130">각 플랫폼의 Protobuf 이진 통신 형식 및 효율적인 코드 생성 덕분에 개발자는 플랫폼 간 고성능 앱을 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="9d8dd-131">유용성 및 생산성</span><span class="sxs-lookup"><span data-stu-id="9d8dd-131">Usability and productivity</span></span>

<span data-ttu-id="9d8dd-132">gRPC는 포괄적인 RPC 솔루션입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="9d8dd-133">여러 언어 및 플랫폼에서 일관되게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="9d8dd-134">또한 대부분의 상용구 코드가 자동으로 생성되는 뛰어난 도구를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="9d8dd-135">WCF와 마찬가지로 gRPC는 메시지와 강력한 형식의 클라이언트를 자동으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="9d8dd-136">개발자 시간이 확보되어 비즈니스 논리에 집중할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="9d8dd-137">스트리밍</span><span class="sxs-lookup"><span data-stu-id="9d8dd-137">Streaming</span></span>

<span data-ttu-id="9d8dd-138">gRPC에는 WCF의 전이중 서비스와 유사한 기능을 제공하는 전체 양방향 스트리밍 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="9d8dd-139">gRPC 스트리밍은 일반적인 인터넷 연결, 부하 분산 장치 및 서비스 메시를 통해 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="9d8dd-140">최종 기한, 시간 초과 및 취소</span><span class="sxs-lookup"><span data-stu-id="9d8dd-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="9d8dd-141">gRPC를 통해 클라이언트는 RPC가 완료될 때까지 최대 시간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="9d8dd-142">지정된 최종 기한을 초과하면 서버에서 클라이언트와 별개로 작업을 취소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="9d8dd-143">후속 gRPC 호출을 통해 최종 기한 및 취소를 전파하면 리소스 사용량 제한을 강제로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="9d8dd-144">클라이언트는 최종 기한을 초과한 경우나 필요에 따라 그 이전에 작업을 중지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="9d8dd-145">예를 들어 클라이언트는 사용자 상호 작용으로 인해 작업을 중지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="9d8dd-146">보안</span><span class="sxs-lookup"><span data-stu-id="9d8dd-146">Security</span></span>

<span data-ttu-id="9d8dd-147">gRPC는 TLS 및 HTTP/2를 사용하여 클라이언트와 서버 간에 엔드투엔드 암호화된 연결을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="9d8dd-148">클라이언트 인증서 인증을 지원하여 클라이언트와 서버 간의 보안 및 신뢰가 더욱 강화됩니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="9d8dd-149">WCF를 .NET Core 및 .NET 5로 마이그레이션하는 경로로서 gRPC</span><span class="sxs-lookup"><span data-stu-id="9d8dd-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="9d8dd-150">.NET Core 및 .NET 5는 다양한 플랫폼에서 서비스를 제공하려는 개발자에게 Microsoft가 원격 통신 솔루션을 제공하는 방식의 변화를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="9d8dd-151">.NET Core 및 .NET 5는 [WCF 서비스 호출](/dotnet/core/additional-tools/wcf-web-service-reference-guide)을 지원하지만 WCF 호스트에 대한 서버 쪽 지원은 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="9d8dd-152">다음은 WCF 앱을 현대화하는 경우 권장되는 두 가지 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="9d8dd-153">gRPC는 최신 기술을 기반으로 하며 RPC 앱 개발자 커뮤니티에서 가장 인기 있는 선택 사항으로 등장했습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="9d8dd-154">.NET Core 3.0부터 최신 .NET 플랫폼은 gRPC를 매우 탁월하게 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="9d8dd-155">gRPC를 사용하도록 WCF 서비스를 마이그레이션하면 최신 앱에 필요한 RPC 기능, 성능, 상호 운용성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="9d8dd-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF)는 .NET Core 및 .NET 5로의 WCF 서비스 호스트를 지원하기 위한 커뮤니티의 노력입니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="9d8dd-157">미리 보기 릴리스를 사용할 수 있으며 프로젝트는 프로덕션 준비를 위해 최선을 다하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="9d8dd-158">CoreWCF는 WCF 기능의 하위 세트만 지원하며, 이 기능을 사용하기 위해 마이그레이션하는 .NET Framework 앱에서는 코드 변경 및 테스트를 성공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="9d8dd-159">CoreWCF는 앱이 WCF 서비스를 호출하는 기존 클라이언트와의 호환성을 유지해야 하는 경우에 매우 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="9d8dd-160">시작</span><span class="sxs-lookup"><span data-stu-id="9d8dd-160">Get started</span></span>

<span data-ttu-id="9d8dd-161">WCF 개발자를 위해 ASP.NET Core에서 gRPC 서비스를 구축하는 방법에 대한 자세한 지침은 [WCF 개발자를 위한 ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9d8dd-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
