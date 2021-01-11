---
title: .NET을 사용하는 Code First gRPC 서비스 및 클라이언트
author: jamesnk
description: .NET으로 Code First gRPC 서비스를 작성하는 경우의 기본 개념을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880622"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="18801-103">.NET을 사용하는 Code First gRPC 서비스 및 클라이언트</span><span class="sxs-lookup"><span data-stu-id="18801-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="18801-104">작성자: [James Newton-King](https://twitter.com/jamesnk) 및 [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="18801-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="18801-105">Code First gRPC는 .NET 형식을 사용하여 서비스 및 메시지 계약을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="18801-106">Code First는 전체 시스템에서 .NET을 사용하는 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="18801-107">.NET 서비스 및 데이터 계약 형식은 .NET 서버와 클라이언트 간에 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18801-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="18801-108">`.proto` 파일 및 코드 생성에서 계약을 정의하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="18801-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="18801-109">여러 언어가 있는 폴리글랏(polyglot) 시스템에서는 Code First를 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="18801-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="18801-110">.NET 서비스 및 데이터 계약 형식은 .NET 플랫폼이 아닌 플랫폼에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="18801-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="18801-111">다른 플랫폼은 Code First를 사용하여 작성된 gRPC 서비스를 호출하려면 서비스와 일치하는 `.proto` 계약을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="18801-112">protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="18801-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="18801-113">protobuf-net.Grpc에 대한 도움말이 필요하면 [protobuf-net.Grpc 웹 사이트](https://protobuf-net.github.io/protobuf-net.Grpc/)를 방문하거나 [protobuf-net.Grpc GitHub 리포지토리](https://github.com/protobuf-net/protobuf-net.Grpc)에서 문제를 제출하세요.</span><span class="sxs-lookup"><span data-stu-id="18801-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="18801-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/)는 커뮤니티 프로젝트이며 Microsoft에서 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18801-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="18801-115">protobuf-net.Grpc는 `Grpc.AspNetCore` 및 `Grpc.Net.Client`에 Code First 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="18801-116">또한 특성으로 주석이 지정된 .NET 형식을 사용하여 앱의 gRPC 서비스 및 메시지를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="18801-117">Code First gRPC 서비스를 만드는 첫 번째 단계는 코드 계약을 정의하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="18801-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="18801-118">서버와 클라이언트가 공유할 새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="18801-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="18801-119">[protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="18801-120">서비스 및 데이터 계약 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="18801-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="18801-121">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="18801-121">The preceding code:</span></span>

* <span data-ttu-id="18801-122">`HelloRequest` 및 `HelloReply` 메시지를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="18801-123">단항 `SayHelloAsync` gRPC 메서드로 `IGreeterService` 계약 인터페이스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="18801-124">서비스 계약은 서버에서 구현되고 클라이언트에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="18801-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="18801-125">서비스 인터페이스에 정의된 메서드는 단항인지, 서버 스트리밍인지, 클라이언트 스트리밍인지, 양방향 스트리밍인지에 따라 특정 서명과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="18801-126">서비스 계약을 정의하는 방법에 대한 자세한 내용은 [protobuf-net.Grpc 시작 설명서](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="18801-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="18801-127">Code First gRPC 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="18801-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="18801-128">ASP.NET Core 앱에 gRPC Code First 서비스를 추가하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="18801-129">[protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="18801-130">공유 코드 계약 프로젝트에 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="18801-131">새 `GreeterService.cs` 파일을 만들고 `IGreeterService` 서비스 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="18801-132">`Startup.cs` 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="18801-133">앞의 코드에서 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18801-133">In the preceding code:</span></span>

* <span data-ttu-id="18801-134">`AddCodeFirstGrpc`는 Code First를 사용하도록 설정하는 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="18801-135">`MapGrpcService<GreeterService>`는 Code First 서비스 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="18801-136">Code First로 구현된 gRPC 서비스와 `.proto` 파일로 구현된 gRPC 서비스는 동일한 앱에 공존할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18801-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="18801-137">모든 gRPC 서비스는 [gRPC 서비스 구성](xref:grpc/configuration#configure-services-options)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="18801-138">Code First gRPC 클라이언트 만들기</span><span class="sxs-lookup"><span data-stu-id="18801-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="18801-139">Code First gRPC 클라이언트는 서비스 계약을 사용하여 gRPC 서비스를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="18801-140">Code First 클라이언트를 사용하여 gRPC 서비스를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="18801-141">[protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="18801-142">공유 코드 계약 프로젝트에 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="18801-143">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="18801-143">The preceding code:</span></span>

* <span data-ttu-id="18801-144">gRPC 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="18801-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="18801-145">`CreateGrpcService<IGreeterService>` 확장 메서드를 사용하여 채널에서 Code First 클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="18801-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="18801-146">`SayHelloAsync`를 사용하여 gRPC 서비스를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="18801-147">Code First gRPC 클라이언트가 채널에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="18801-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="18801-148">일반 클라이언트와 마찬가지로 Code First 클라이언트는 [채널 구성](xref:grpc/configuration#configure-client-options)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18801-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18801-149">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="18801-149">Additional resources</span></span>

* [<span data-ttu-id="18801-150">protobuf-net.Grpc 웹 사이트</span><span class="sxs-lookup"><span data-stu-id="18801-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="18801-151">protobuf-net.Grpc GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="18801-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
