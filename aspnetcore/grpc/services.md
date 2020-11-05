---
title: gRPC 서비스 및 메서드 만들기
author: jamesnk
description: gRPC 서비스 및 메서드를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058742"
---
# <a name="create-grpc-services-and-methods"></a>gRPC 서비스 및 메서드 만들기

작성자: [James Newton-King](https://twitter.com/jamesnk)

이 문서에서는 C#에서 gRPC 서비스 및 메서드를 만드는 방법을 설명합니다. 다룰 주제는 다음과 같습니다.

* *.proto* 파일에서 서비스 및 메서드를 정의하는 방법
* gRPC C# 도구를 사용하여 생성된 코드
* gRPC 서비스 및 메서드 구현

## <a name="create-new-grpc-services"></a>새 gRPC 서비스 만들기

[C#을 사용하는 gRPC 서비스](xref:grpc/basics)에는 API 개발에 대해 gRPC의 계약 중심 접근 방식이 도입되었습니다. 서비스 및 메시지는 *.proto* 파일에서 정의됩니다. 그런 다음 C# 도구가 *.proto* 파일에서 코드를 생성합니다. 서버 쪽 자산의 경우 임의의 메시지의 클래스와 함께 각 서비스에 대해 추상 기본 형식이 생성됩니다.

아래의 *.proto* 파일은 다음과 같습니다.

* `Greeter` 서비스를 정의합니다.
* `Greeter` 서비스가 `SayHello` 호출을 정의합니다.
* `SayHello`가 `HelloRequest` 메시지를 보내고 `HelloReply` 메시지를 수신합니다.

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

C# 도구가 C# `GreeterBase` 기본 형식을 생성합니다.

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

기본적으로, 생성된 `GreeterBase`는 아무것도 하지 않습니다. 가상 `SayHello` 메서드는 호출한 클라이언트에 `UNIMPLEMENTED` 오류를 반환합니다. 서비스가 유용하려면 앱이 `GreeterBase`의 구체적인 구현을 만들어야 합니다.

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

서비스 구현이 앱에 등록됩니다. ASP.NET Core gRPC가 서비스를 호스트하는 경우 `MapGrpcService` 메서드를 사용하여 서비스를 라우팅 파이프라인에 추가해야 합니다.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

자세한 내용은 <xref:grpc/aspnetcore> 를 참조하세요.

## <a name="implement-grpc-methods"></a>gRPC 메서드 구현

gRPC 서비스에는 다양한 형식의 메서드가 있을 수 있습니다. 서비스에서 메시지를 보내고 받는 방법은 정의된 메서드 형식에 따라 다릅니다. gRPC 메서드 형식은 다음과 같습니다.

* 단항
* 서버 스트리밍
* 클라이언트 스트리밍
* 양방향 스트리밍

스트리밍 호출은 *.proto* 파일에서 `stream` 키워드를 사용하여 지정됩니다. `stream`은 호출의 요청 메시지나 응답 메시지 또는 두 메시지 모두에 배치할 수 있습니다.

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

각 호출 유형에는 다른 메서드 시그니처가 있습니다. 구체적인 구현에서 추상 기본 서비스 유형에서 생성된 메서드를 재정의하면 올바른 인수와 반환 형식이 사용됩니다.

### <a name="unary-method"></a>단항 메서드

단항 메서드는 매개 변수로 요청 메시지를 가져오고 응답을 반환합니다. 응답이 반환되면 단항 호출이 완료됩니다.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

단항 호출은 [웹 API 컨트롤러의 작업](xref:web-api/index)과 가장 비슷합니다. gRPC 메서드가 작업과 다른 한 가지 중요한 차이점은 gRPC 메서드가 요청의 일부를 다른 메서드 인수에 바인딩할 수 없다는 것입니다. gRPC 메서드에는 들어오는 요청 데이터에 대해 항상 하나의 메시지 인수가 있습니다. 요청 메시지의 필드로 만들어 여러 값을 gRPC 서비스로 보낼 수 있습니다.

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>서버 스트리밍 메서드

서버 스트리밍 메서드는 요청 메시지를 매개 변수로 가져옵니다. 여러 메시지를 다시 호출자에 스트리밍할 수 있으므로 응답 메시지를 보내는 데 `responseStream.WriteAsync`를 사용합니다. 메서드가 반환되면 서버 스트리밍 호출이 완료됩니다.

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

클라이언트에는 서버 스트리밍 메서드가 시작된 후 추가 메시지나 데이터를 보낼 방법이 없습니다. 일부 스트리밍 메서드는 계속 실행되도록 설계되었습니다. 연속 스트리밍 메서드의 경우 클라이언트는 더 이상 필요하지 않을 때 호출을 취소할 수 있습니다. 취소가 발생하면 클라이언트가 서버에 신호를 보내고 [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken)이 발생합니다. 비동기 메서드를 사용하는 서버에서 `CancellationToken` 토큰을 사용하여 다음과 같이 수행해야 합니다.

* 모든 비동기 작업은 스트리밍 호출과 함께 취소됩니다.
* 메서드가 신속하게 종료됩니다.

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

### <a name="client-streaming-method"></a>클라이언트 스트리밍 메서드

클라이언트 스트리밍 메서드는 메시지를 수신하지 ‘않고도’ 시작됩니다. `requestStream` 매개 변수는 클라이언트에서 메시지를 읽는 데 사용됩니다. 응답 메시지가 반환되면 클라이언트 스트리밍 호출이 완료됩니다.

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

C# 8 이상을 사용하는 경우 `await foreach` 구문을 사용하여 메시지를 읽을 수 있습니다. `IAsyncStreamReader<T>.ReadAllAsync()` 확장 메서드는 요청 스트림에서 모든 메시지를 읽습니다.

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

### <a name="bi-directional-streaming-method"></a>양방향 스트리밍 메서드

양방향 스트리밍 메서드는 메시지를 수신하지 ‘않고도’ 시작됩니다. `requestStream` 매개 변수는 클라이언트에서 메시지를 읽는 데 사용됩니다. 메서드는 `responseStream.WriteAsync`를 사용하여 메시지를 보내도록 선택할 수 있습니다. 메서드가 반환되면 양방향 스트리밍 호출이 완료됩니다.

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

위의 코드는

* 각 요청에 대한 응답을 보냅니다.
* 양방향 스트리밍의 기본적인 사용 방법입니다.

동시에 요청을 읽고 응답을 보내는 것과 같이 더욱 복잡한 시나리오를 지원할 수 있습니다.

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

양방향 스트리밍 메서드에서 클라이언트와 서비스는 언제든지 서로에게 메시지를 보낼 수 있습니다. 양방향 메서드를 구현하는 최상의 방법은 요구 사항에 따라 다릅니다.

## <a name="access-grpc-request-headers"></a>gRPC 요청 헤더 액세스

요청 메시지는 클라이언트에서 gRPC 서비스로 데이터를 보내는 유일한 방법은 아닙니다. `ServerCallContext.RequestHeaders`를 사용하면 서비스에서 헤더 값을 사용할 수 있습니다.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>추가 리소스

* <xref:grpc/basics>
* <xref:grpc/client>
