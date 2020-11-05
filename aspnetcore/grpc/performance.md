---
title: gRPC 관련 성능 모범 사례
author: jamesnk
description: 고성능 gRPC 서비스를 빌드하기 위한 모범 사례를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060406"
---
# <a name="performance-best-practices-with-grpc"></a>gRPC 관련 성능 모범 사례

작성자: [James Newton-King](https://twitter.com/jamesnk)

gRPC는 고성능 서비스를 위해 설계되었습니다. 이 문서에서는 gRPC에서 가능한 최상의 성능을 얻는 방법을 설명합니다.

## <a name="reuse-grpc-channels"></a>gRPC 채널 다시 사용

gRPC 호출 시 gRPC 채널을 재사용해야 합니다. 채널을 재사용하면 기존 HTTP/2 연결을 통해 호출이 멀티플렉싱될 수 있습니다.

gRPC 호출별로 새 채널을 생성하는 경우 완료하는 데 상당히 오랜 시간이 걸릴 수 있습니다. 호출할 때마다 새 HTTP/2 연결을 만들기 위해 클라이언트와 서버 간에 여러 번의 네트워크 왕복이 필요합니다.

1. 소켓 열기
2. TCP 연결 설정
3. TLS 협상
4. HTTP/2 연결 시작
5. gRPC 호출하기

gRPC 호출 간에 채널을 공유하고 재사용할 수 있습니다.

* gRPC 클라이언트는 채널을 사용하여 만듭니다. gRPC 클라이언트는 경량 개체이며 캐시하거나 재사용할 필요가 없습니다.
* 다양한 형식의 클라이언트를 포함하여 여러 gRPC 클라이언트를 한 채널에서 만들 수 있습니다.
* 채널 및 채널에서 만든 클라이언트를 여러 스레드에서 안전하게 사용할 수 있습니다.
* 채널에서 만든 클라이언트는 여러 개의 동시 호출을 수행할 수 있습니다.

gRPC 클라이언트 팩터리는 중앙 집중식으로 채널을 구성하는 방법을 제공합니다. 자동으로 기본 채널을 다시 사용합니다. 자세한 내용은 <xref:grpc/clientfactory>를 참조하세요.

## <a name="connection-concurrency"></a>연결 동시성

일반적으로 HTTP/2 연결에는 하나의 연결에서 한 번에 사용 가능한 [최대 동시 스트림(활성 HTTP 요청)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) 수에 한도가 있습니다. 기본적으로 대부분의 서버는 이 한도를 100개의 동시 스트림으로 설정합니다.

gRPC 채널은 단일 HTTP/2 연결을 사용하고, 해당 연결에서 동시 호출이 멀티플렉싱됩니다. 활성 호출 수가 연결 스트림 한도에 도달하면 추가 호출은 클라이언트에서 큐에 대기합니다. 큐에 대기 중인 호출은 활성 호출이 완료될 때까지 대기한 후 전송됩니다. 부하가 높은 애플리케이션이나 장기 스트리밍 gRPC 호출에서는 이 한도로 인해 큐에 대기 중인 호출이 야기하는 성능 문제가 발생할 수 있습니다.

::: moniker range=">= aspnetcore-5.0"

.NET 5에는 `SocketsHttpHandler.EnableMultipleHttp2Connections` 속성이 도입되었습니다. `true`로 설정하면 동시 스트림 한도에 도달하는 경우 채널에서 추가 HTTP/2 연결이 만들어집니다. `GrpcChannel`이 만들어지면 내부 `SocketsHttpHandler`가 추가 HTTP/2 연결을 만들도록 자동으로 구성됩니다. 앱이 고유한 처리기를 구성하는 경우에는 `EnableMultipleHttp2Connections`를 `true`로 설정하는 것이 좋습니다.

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

.NET Core 3.1 앱을 위한 몇 가지 해결 방법이 있습니다.

* 부하가 높은 앱 영역에 대해 별도의 gRPC 채널을 만듭니다. 예를 들어 `Logger` gRPC 서비스의 부하가 높을 수 있습니다. 별도의 채널을 사용하여 앱에서 `LoggerClient`를 만듭니다.
* gRPC 채널의 풀을 사용합니다(예: gRPC 채널 목록 만들기). `Random`은 gRPC 채널이 필요할 때마다 목록에서 채널을 선택하는 데 사용됩니다. `Random`을 사용하면 호출이 여러 연결에서 무작위로 분산됩니다.

> [!IMPORTANT]
> 서버에 대한 최대 동시 스트림 한도를 늘리는 것은 이 문제를 해결하는 또 다른 방법입니다. Kestrel에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>을 사용하여 구성합니다.
>
> 최대 동시 스트림 한도를 늘리지 않는 것이 좋습니다. 단일 HTTP/2 연결에서 스트림이 너무 많으면 새로운 성능 문제가 발생합니다.
>
> * 연결에 쓰려고 시도하는 스트림 간에 스레드 경합이 발생합니다.
> * 연결 패킷 손실로 인해 TCP 계층에서 모든 호출이 차단됩니다.

## <a name="load-balancing"></a>부하 분산

일부 부하 분산 장치는 gRPC에서 효과적으로 작동하지 않습니다. L4(전송) 부하 분산 장치는 엔드포인트 간에 TCP 연결을 분산하여 연결 수준에서 작동합니다. 이 접근 방식은 HTTP/1.1을 사용하여 수행된 API 호출의 부하를 분산하는 데 적합합니다. HTTP/1.1을 사용하여 수행된 동시 호출은 여러 연결에서 전송되므로 엔드포인트에서 호출의 부하를 분산할 수 있습니다.

L4 부하 분산 장치는 연결 수준에서 작동하므로 gRPC에서 제대로 작동하지 않습니다. gRPC는 단일 TCP 연결에서 여러 호출을 멀티플렉싱하는 HTTP/2를 사용합니다. 해당 연결을 통한 모든 gRPC 호출은 하나의 엔드포인트로 이동합니다.

gRPC의 부하를 효과적으로 분산하는 두 가지 옵션이 있습니다.

* 클라이언트 쪽 부하 분산
* L7(애플리케이션) 프록시 부하 분산

> [!NOTE]
> 엔드포인트 간에 gRPC 호출의 부하만 분산할 수 있습니다. 스트리밍 gRPC 호출이 설정되면 스트림을 통해 전송되는 모든 메시지가 하나의 엔드포인트로 이동합니다.

### <a name="client-side-load-balancing"></a>클라이언트 쪽 부하 분산

클라이언트 쪽 부하 분산을 사용하면 클라이언트가 엔드포인트를 인식합니다. 각 gRPC 호출을 위해 클라이언트는 호출을 전송할 다른 엔드포인트를 선택합니다. 클라이언트 쪽 부하 분산은 대기 시간이 중요한 경우에 적합합니다. 클라이언트와 서비스 간에 프록시가 없으므로 호출이 서비스로 직접 전송됩니다. 클라이언트 쪽 부하 분산의 단점은 각 클라이언트가 사용해야 하는 사용 가능한 엔드포인트를 추적해야 한다는 것입니다.

할당 준비 클라이언트 부하 분산은 부하 분산 상태가 중앙 위치에 저장되는 기술입니다. 클라이언트는 부하 분산을 결정할 때 사용할 정보를 중앙 위치에서 주기적으로 쿼리합니다.

`Grpc.Net.Client`는 현재 클라이언트 쪽 부하 분산을 지원하지 않습니다. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core)는 .NET에서 클라이언트 쪽 부하 분산이 필요한 경우에 적합합니다.

### <a name="proxy-load-balancing"></a>프록시 부하 분산

L7(애플리케이션) 프록시는 L4(전송) 프록시보다 더 높은 수준에서 작동합니다. L7 프록시는 HTTP/2를 해석하며 하나의 HTTP/2 연결을 통해 프록시로 멀티플렉싱된 gRPC 호출을 여러 엔드포인트에서 분산할 수 있습니다. 프록시 사용은 클라이언트 쪽 부하 분산보다 더 간단하지만 gRPC 호출에 대기 시간을 더 추가할 수 있습니다.

많은 L7 프록시를 사용할 수 있습니다. 몇 가지 옵션은 다음과 같습니다.

* [Envoy](https://www.envoyproxy.io/) - 인기 있는 오픈 소스 프록시입니다.
* [Linkerd](https://linkerd.io/) - Kubernetes용 서비스 메시입니다.
* [YARP: 역방향 프록시](https://microsoft.github.io/reverse-proxy/) - .NET으로 작성된 미리 보기 오픈 소스 프록시입니다.

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>프로세스 간 통신

클라이언트와 서비스 간의 gRPC 호출은 일반적으로 TCP 소켓을 통해 전송됩니다. TCP는 네트워크를 통해 통신하는 데 유용하지만, 클라이언트와 서비스가 동일한 머신에 있는 경우에는 [IPC(프로세스 간 통신)](https://wikipedia.org/wiki/Inter-process_communication)가 더 효율적입니다.

동일한 머신에 있는 프로세스 간 gRPC 호출에는 Unix 도메인 소켓이나 명명된 파이프와 같은 전송을 사용하는 것이 좋습니다. 자세한 내용은 <xref:grpc/interprocess>를 참조하세요.

## <a name="keep-alive-pings"></a>연결 유지 ping

연결 유지 ping을 사용하여 비활성 기간 동안 HTTP/2 연결을 유지할 수 있습니다. 앱이 작업을 계속할 때 기존 HTTP/2 연결을 곧바로 사용할 수 있으면 연결을 다시 설정하느라 지연이 발생하지 않고 초기 gRPC 호출을 신속하게 수행할 수 있습니다.

연결 유지 ping은 <xref:System.Net.Http.SocketsHttpHandler>에서 구성됩니다.

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

앞의 코드는 비활성 기간 동안 60초마다 연결 유지 ping을 서버에 보내는 채널을 구성합니다. ping을 사용하면 서버와 사용 중인 모든 프록시가 비활성 상태로 인해 연결을 닫지 않습니다.

::: moniker-end

## <a name="streaming"></a>스트리밍

gRPC 양방향 스트리밍을 사용하면 고성능 시나리오에서 단항 gRPC 호출을 바꿀 수 있습니다. 양방향 스트림이 시작된 후에는 메시지를 스트리밍하는 것이 여러 개의 단항 gRPC 호출을 통해 메시지를 보내는 것보다 빠릅니다. 스트리밍된 메시지는 기존 HTTP/2 요청에서 데이터로 전송되며, 단항 호출별로 새 HTTP/2 요청을 만드는 오버헤드가 제거됩니다.

예제 서비스:

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

클라이언트 예:

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

성능상의 이유로 단항 호출을 양방향 스트리밍으로 대체하는 것은 고급 기법이며 많은 상황에서 적합하지 않습니다.

스트리밍 호출을 사용하는 것은 다음과 같은 경우에 적합합니다.

1. 높은 처리량이나 짧은 대기 시간이 필요한 경우
2. gRPC 및 HTTP/2가 성능 병목 상태로 식별된 경우
3. 클라이언트의 작업자가 gRPC 서비스를 사용하여 일반 메시지를 보내거나 받는 경우

단항 대신 스트리밍 호출을 사용할 때 추가 복잡성과 제한 사항에 유의하세요.

1. 서비스 또는 연결 오류로 인해 스트림이 중단될 수 있습니다. 오류가 발생하는 경우 스트림을 다시 시작하기 위한 논리가 필요합니다.
2. `RequestStream.WriteAsync`는 다중 스레딩에 안전하게 사용할 수 없습니다. 한 번에 하나의 메시지만 스트림에 쓸 수 있습니다. 여러 스레드의 메시지를 단일 스트림을 통해 보내려면 <xref:System.Threading.Channels.Channel%601> 같은 생산자/소비자 큐가 메시지를 마샬링해야 합니다.
3. gRPC 스트리밍 방법은 한 유형의 메시지를 수신하고 한 유형의 메시지 유형을 보내는 것으로 제한됩니다. 예를 들어 `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)`는 `RequestMessage`를 수신하고 `ResponseMessage`를 보냅니다. Protobuf가 `Any` 및 `oneof`를 사용하여 알 수 없는 메시지나 조건부 메시지를 지원하면 이러한 제한을 해결할 수 있습니다.
