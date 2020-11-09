---
title: C Core에서 ASP.NET Core로 gRPC 서비스 마이그레이션
author: juntaoluo
description: ASP.NET Core 스택을 토대로 실행되도록 기존 C Core 기반 gRPC 앱을 이동하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059834"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="81961-103">C Core에서 ASP.NET Core로 gRPC 서비스 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="81961-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="81961-104">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="81961-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="81961-105">기본 스택의 구현 때문에 일부 기능은 [C Core 기반 gRPC](https://grpc.io/blog/grpc-stacks) 앱과 ASP.NET Core 기반 앱에서 동일한 방식으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="81961-106">이 문서에서는 두 스택 간의 마이그레이션을 위해 주요 차이점을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="81961-107">gRPC 서비스 구현 수명</span><span class="sxs-lookup"><span data-stu-id="81961-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="81961-108">ASP.NET Core 스택에서 gRPC 서비스는 기본적으로 [범위가 지정된 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="81961-109">반면, gRPC C Core는 기본적으로 [singleton 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 사용하여 서비스에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="81961-110">범위가 지정된 수명을 사용하면 서비스 구현에서 범위가 지정된 수명을 사용하는 다른 서비스를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="81961-111">예를 들어 범위가 지정된 수명이 생성자 주입을 통해 DI 컨테이너에서 `DbContext`를 확인할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="81961-112">범위가 지정된 수명 사용과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="81961-113">각 요청에 대해 서비스 구현의 새 인스턴스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="81961-114">구현 형식의 인스턴스 멤버를 통해 요청 간에 상태를 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="81961-115">DI 컨테이너의 단일 서비스에 공유 상태를 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="81961-116">저장된 공유 상태는 gRPC 서비스 구현의 생성자에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="81961-117">서비스 수명에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#service-lifetimes>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81961-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="81961-118">singleton 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="81961-118">Add a singleton service</span></span>

<span data-ttu-id="81961-119">gRPC C Core 구현에서 ASP.NET Core로 전환하기 쉽도록 서비스 구현의 서비스 수명을 범위가 지정된 수명에서 singleton 수명으로 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="81961-120">여기에는 서비스 구현 인스턴스를 DI 컨테이너에 추가하는 작업이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="81961-121">그러나 singleton 수명을 사용하는 서비스 구현은 이제 생성자 주입을 통해 범위가 지정된 서비스를 확인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="81961-122">gRPC 서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="81961-122">Configure gRPC services options</span></span>

<span data-ttu-id="81961-123">C Core 기반 앱에서 `grpc.max_receive_message_length`, `grpc.max_send_message_length` 등의 설정은 [서버 인스턴스를 생성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)할 때 `ChannelOption`을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="81961-124">ASP.NET Core에서 gRPC는 `GrpcServiceOptions` 형식을 통해 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="81961-125">예를 들어 gRPC 서비스에서 들어오는 메시지의 최대 크기는 `AddGrpc`를 통해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="81961-126">다음 예제에서는 기본 `MaxReceiveMessageSize`인 4MB를 16MB로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="81961-127">구성에 대한 자세한 내용은 <xref:grpc/configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81961-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="81961-128">로깅</span><span class="sxs-lookup"><span data-stu-id="81961-128">Logging</span></span>

<span data-ttu-id="81961-129">C Core 기반 앱은 `GrpcEnvironment`를 사용하여 디버깅 용도의 [로거를 구성](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_)합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="81961-130">ASP.NET Core 스택은 [로깅 API](xref:fundamentals/logging/index)를 통해 이 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="81961-131">예를 들어 생성자 주입을 통해 gRPC 서비스에 로거를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="81961-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="81961-132">HTTPS</span></span>

<span data-ttu-id="81961-133">C Core 기반 앱은 [Server.Ports 속성](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports)을 통해 HTTPS를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="81961-134">ASP.NET Core에서 서버를 구성하는 경우에도 유사한 개념이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="81961-135">예를 들어 Kestrel은 이 기능을 위해 [엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="81961-136">gRPC 인터셉터 대 미들웨어</span><span class="sxs-lookup"><span data-stu-id="81961-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="81961-137">ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)는 C Core 기반 gRPC 앱의 인터셉터와 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="81961-138">ASP.NET Core 미들웨어와 인터셉터는 개념적으로 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="81961-139">둘 다:</span><span class="sxs-lookup"><span data-stu-id="81961-139">Both:</span></span>

* <span data-ttu-id="81961-140">gRPC 요청을 처리하는 파이프라인을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="81961-141">파이프라인의 다음 구성 요소 이전이나 이후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="81961-142">`HttpContext`에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="81961-143">미들웨어에서 `HttpContext`는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="81961-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="81961-144">인터셉터에서는 `ServerCallContext.GetHttpContext` 확장 메서드와 함께 `ServerCallContext` 매개 변수를 사용하여 `HttpContext`에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="81961-145">이 기능은 ASP.NET Core에서 실행되는 인터셉터와 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="81961-146">ASP.NET Core 미들웨어와 gRPC 인터셉터의 차이점:</span><span class="sxs-lookup"><span data-stu-id="81961-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="81961-147">인터셉터:</span><span class="sxs-lookup"><span data-stu-id="81961-147">Interceptors:</span></span>
  * <span data-ttu-id="81961-148">[ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)를 사용하여 추상화의 gRPC 계층에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="81961-149">다음에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-149">Provide access to:</span></span>
    * <span data-ttu-id="81961-150">호출에 전송되는 역직렬화된 메시지</span><span class="sxs-lookup"><span data-stu-id="81961-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="81961-151">직렬화되기 전에 호출에서 반환되는 메시지</span><span class="sxs-lookup"><span data-stu-id="81961-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="81961-152">gRPC 서비스에서 throw된 예외를 catch하고 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="81961-153">미들웨어:</span><span class="sxs-lookup"><span data-stu-id="81961-153">Middleware:</span></span>
  * <span data-ttu-id="81961-154">gRPC 인터셉터 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="81961-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="81961-155">기본 HTTP/2 메시지에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="81961-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="81961-156">요청 및 응답 스트림의 바이트에만 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81961-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81961-157">추가 자료</span><span class="sxs-lookup"><span data-stu-id="81961-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
