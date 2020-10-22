---
title: gRPC 서비스 및 메서드 만들기
author: jamesnk
description: gRPC 서비스 및 메서드를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
no-loc:
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
uid: grpc/services
ms.openlocfilehash: c4d37556a3345d275f45c537a40908c5966fe015
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113623"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="1aa2e-103">gRPC 서비스 및 메서드 만들기</span><span class="sxs-lookup"><span data-stu-id="1aa2e-103">Create gRPC services and methods</span></span>

<span data-ttu-id="1aa2e-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1aa2e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1aa2e-105">이 문서에서는 C#에서 gRPC 서비스 및 메서드를 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="1aa2e-106">다룰 주제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-106">Topics include:</span></span>

* <span data-ttu-id="1aa2e-107">*.proto* 파일에서 서비스 및 메서드를 정의하는 방법</span><span class="sxs-lookup"><span data-stu-id="1aa2e-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="1aa2e-108">gRPC C# 도구를 사용하여 생성된 코드</span><span class="sxs-lookup"><span data-stu-id="1aa2e-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="1aa2e-109">gRPC 서비스 및 메서드 구현</span><span class="sxs-lookup"><span data-stu-id="1aa2e-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="1aa2e-110">새 gRPC 서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="1aa2e-110">Create new gRPC services</span></span>

<span data-ttu-id="1aa2e-111">[C#을 사용하는 gRPC 서비스](xref:grpc/basics)에는 API 개발에 대해 gRPC의 계약 중심 접근 방식이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="1aa2e-112">서비스 및 메시지는 *.proto* 파일에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="1aa2e-113">그런 다음 C# 도구가 *.proto* 파일에서 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="1aa2e-114">서버 쪽 자산의 경우 임의의 메시지의 클래스와 함께 각 서비스에 대해 추상 기본 형식이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="1aa2e-115">아래의 *.proto* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-115">The following *.proto* file:</span></span>

* <span data-ttu-id="1aa2e-116">`Greeter` 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="1aa2e-117">`Greeter` 서비스가 `SayHello` 호출을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="1aa2e-118">`SayHello`가 `HelloRequest` 메시지를 보내고 `HelloReply` 메시지를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="1aa2e-119">C# 도구가 C# `GreeterBase` 기본 형식을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="1aa2e-120">기본적으로, 생성된 `GreeterBase`는 아무것도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="1aa2e-121">가상 `SayHello` 메서드는 호출한 클라이언트에 `UNIMPLEMENTED` 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="1aa2e-122">서비스가 유용하려면 앱이 `GreeterBase`의 구체적인 구현을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="1aa2e-123">서비스 구현이 앱에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="1aa2e-124">ASP.NET Core gRPC가 서비스를 호스트하는 경우 `MapGrpcService` 메서드를 사용하여 서비스를 라우팅 파이프라인에 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="1aa2e-125">자세한 내용은 <xref:grpc/aspnetcore> 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="1aa2e-126">gRPC 메서드 구현</span><span class="sxs-lookup"><span data-stu-id="1aa2e-126">Implement gRPC methods</span></span>

<span data-ttu-id="1aa2e-127">gRPC 서비스에는 다양한 형식의 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="1aa2e-128">서비스에서 메시지를 보내고 받는 방법은 정의된 메서드 형식에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="1aa2e-129">gRPC 메서드 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-129">The gRPC method types are:</span></span>

* <span data-ttu-id="1aa2e-130">단항</span><span class="sxs-lookup"><span data-stu-id="1aa2e-130">Unary</span></span>
* <span data-ttu-id="1aa2e-131">서버 스트리밍</span><span class="sxs-lookup"><span data-stu-id="1aa2e-131">Server streaming</span></span>
* <span data-ttu-id="1aa2e-132">클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="1aa2e-132">Client streaming</span></span>
* <span data-ttu-id="1aa2e-133">양방향 스트리밍</span><span class="sxs-lookup"><span data-stu-id="1aa2e-133">Bi-directional streaming</span></span>

<span data-ttu-id="1aa2e-134">스트리밍 호출은 *.proto* 파일에서 `stream` 키워드를 사용하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="1aa2e-135">`stream`은 호출의 요청 메시지나 응답 메시지 또는 두 메시지 모두에 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="1aa2e-136">각 호출 유형에는 다른 메서드 시그니처가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-136">Each call type has a different method signature.</span></span> <span data-ttu-id="1aa2e-137">구체적인 구현에서 추상 기본 서비스 유형에서 생성된 메서드를 재정의하면 올바른 인수와 반환 형식이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="1aa2e-138">단항 메서드</span><span class="sxs-lookup"><span data-stu-id="1aa2e-138">Unary method</span></span>

<span data-ttu-id="1aa2e-139">단항 메서드는 매개 변수로 요청 메시지를 가져오고 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="1aa2e-140">응답이 반환되면 단항 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="1aa2e-141">단항 호출은 [웹 API 컨트롤러의 작업](xref:web-api/index)과 가장 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="1aa2e-142">gRPC 메서드가 작업과 다른 한 가지 중요한 차이점은 gRPC 메서드가 요청의 일부를 다른 메서드 인수에 바인딩할 수 없다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="1aa2e-143">gRPC 메서드에는 들어오는 요청 데이터에 대해 항상 하나의 메시지 인수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="1aa2e-144">요청 메시지의 필드로 만들어 여러 값을 gRPC 서비스로 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="1aa2e-145">서버 스트리밍 메서드</span><span class="sxs-lookup"><span data-stu-id="1aa2e-145">Server streaming method</span></span>

<span data-ttu-id="1aa2e-146">서버 스트리밍 메서드는 요청 메시지를 매개 변수로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="1aa2e-147">여러 메시지를 다시 호출자에 스트리밍할 수 있으므로 응답 메시지를 보내는 데 `responseStream.WriteAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="1aa2e-148">메서드가 반환되면 서버 스트리밍 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-148">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="1aa2e-149">클라이언트에는 서버 스트리밍 메서드가 시작된 후 추가 메시지나 데이터를 보낼 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="1aa2e-150">일부 스트리밍 메서드는 계속 실행되도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="1aa2e-151">연속 스트리밍 메서드의 경우 클라이언트는 더 이상 필요하지 않을 때 호출을 취소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="1aa2e-152">취소가 발생하면 클라이언트가 서버에 신호를 보내고 [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken)이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="1aa2e-153">비동기 메서드를 사용하는 서버에서 `CancellationToken` 토큰을 사용하여 다음과 같이 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="1aa2e-154">모든 비동기 작업은 스트리밍 호출과 함께 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="1aa2e-155">메서드가 신속하게 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-155">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="1aa2e-156">클라이언트 스트리밍 메서드</span><span class="sxs-lookup"><span data-stu-id="1aa2e-156">Client streaming method</span></span>

<span data-ttu-id="1aa2e-157">클라이언트 스트리밍 메서드는 메시지를 수신하지 ‘않고도’ 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="1aa2e-158">`requestStream` 매개 변수는 클라이언트에서 메시지를 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="1aa2e-159">응답 메시지가 반환되면 클라이언트 스트리밍 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-159">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="1aa2e-160">C# 8 이상을 사용하는 경우 `await foreach` 구문을 사용하여 메시지를 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="1aa2e-161">`IAsyncStreamReader<T>.ReadAllAsync()` 확장 메서드는 요청 스트림에서 모든 메시지를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="1aa2e-162">양방향 스트리밍 메서드</span><span class="sxs-lookup"><span data-stu-id="1aa2e-162">Bi-directional streaming method</span></span>

<span data-ttu-id="1aa2e-163">양방향 스트리밍 메서드는 메시지를 수신하지 ‘않고도’ 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="1aa2e-164">`requestStream` 매개 변수는 클라이언트에서 메시지를 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="1aa2e-165">메서드는 `responseStream.WriteAsync`를 사용하여 메시지를 보내도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="1aa2e-166">메서드가 반환되면 양방향 스트리밍 호출이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-166">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="1aa2e-167">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="1aa2e-167">The preceding code:</span></span>

* <span data-ttu-id="1aa2e-168">각 요청에 대한 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-168">Sends a response for each request.</span></span>
* <span data-ttu-id="1aa2e-169">양방향 스트리밍의 기본적인 사용 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="1aa2e-170">동시에 요청을 읽고 응답을 보내는 것과 같이 더욱 복잡한 시나리오를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="1aa2e-171">양방향 스트리밍 메서드에서 클라이언트와 서비스는 언제든지 서로에게 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="1aa2e-172">양방향 메서드를 구현하는 최상의 방법은 요구 사항에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="1aa2e-173">gRPC 요청 헤더 액세스</span><span class="sxs-lookup"><span data-stu-id="1aa2e-173">Access gRPC request headers</span></span>

<span data-ttu-id="1aa2e-174">요청 메시지는 클라이언트에서 gRPC 서비스로 데이터를 보내는 유일한 방법은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="1aa2e-175">`ServerCallContext.RequestHeaders`를 사용하면 서비스에서 헤더 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1aa2e-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="1aa2e-176">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="1aa2e-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
