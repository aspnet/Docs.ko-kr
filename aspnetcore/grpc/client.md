---
title: .NET 클라이언트를 사용하여 gRPC 서비스 호출
author: jamesnk
description: .NET gRPC 클라이언트를 사용하여 gRPC 서비스를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 9322020083ce25b00b2979633ae8a692cfd4da4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060965"
---
# <a name="call-grpc-services-with-the-net-client"></a>.NET 클라이언트를 사용하여 gRPC 서비스 호출

.NET gRPC 클라이언트 라이브러리는 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 패키지에서 사용할 수 있습니다. 이 문서에서는 다음을 수행하는 방법을 설명합니다.

* gRPC 서비스를 호출하도록 gRPC 클라이언트를 구성합니다.
* 단항, 서버 스트리밍, 클라이언트 스트리밍 및 양방향 스트리밍 메서드에 대한 gRPC 호출을 수행합니다.

## <a name="configure-grpc-client"></a>gRPC 클라이언트 구성

gRPC 클라이언트는 [ *\*.proto* 파일에서 생성](xref:grpc/basics#generated-c-assets)되는 구체적인 클라이언트 형식입니다. 구체적인 gRPC 클라이언트에는 *\*.proto* 파일에서 gRPC 서비스로 변환되는 메서드가 있습니다. 예를 들어 `Greeter`라는 서비스는 서비스를 호출하는 메서드를 사용하여 `GreeterClient` 형식을 생성합니다.

채널에서 gRPC 클라이언트를 만듭니다. 먼저 `GrpcChannel.ForAddress`를 사용하여 채널을 만든 다음, 채널을 사용하여 gRPC 클라이언트를 만듭니다.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

채널은 gRPC 서비스에 대한 장기 연결을 나타냅니다. 채널이 생성되면 서비스 호출과 관련된 옵션을 사용하여 구성합니다. 예를 들어 호출, 최대 전송 및 수신 메시지 크기, 로깅을 수행하는 데 사용되는 `HttpClient`를 `GrpcChannelOptions`에서 지정하고 `GrpcChannel.ForAddress`와 함께 사용할 수 있습니다. 전체 옵션 목록은 [클라이언트 구성 옵션](xref:grpc/configuration#configure-client-options)을 참조하세요.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>TLS 구성

gRPC 클라이언트는 호출된 서비스와 동일한 연결 수준 보안을 사용해야 합니다. gRPC 클라이언트 TLS(전송 계층 보안)는 gRPC 채널을 만들 때 구성됩니다. gRPC 클라이언트는 서비스를 호출할 때와 채널 및 서비스의 연결 수준 보안이 일치하지 않을 때 오류를 throw합니다.

TLS를 사용하도록 gRPC 채널을 구성하려면 서버 주소가 `https`로 시작하는지 확인합니다. 예를 들어 `GrpcChannel.ForAddress("https://localhost:5001")`는 HTTPS 프로토콜을 사용합니다. gRPC 채널은 TLS로 보호되는 연결을 자동으로 협상하고 보안 연결을 사용하여 gRPC 호출을 수행합니다.

> [!TIP]
> gRPC는 TLS를 통한 클라이언트 인증서 인증을 지원합니다. gRPC 채널을 사용하여 클라이언트 인증서를 구성하는 방법에 대한 내용은 <xref:grpc/authn-and-authz#client-certificate-authentication>를 참조하세요.

보안이 설정되지 않은 gRPC 서비스를 호출하려면 서버 주소가 `http`로 시작하는지 확인합니다. 예를 들어 `GrpcChannel.ForAddress("http://localhost:5000")`는 HTTP 프로토콜을 사용합니다. .NET Core 3.1 이상에서 [.NET 클라이언트를 사용하여 안전하지 않은 gRPC 서비스를 호출](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)하려면 추가 구성이 필요합니다.

### <a name="client-performance"></a>클라이언트 성능

채널 및 클라이언트 성능과 사용법:

* 채널을 만드는 작업은 비용이 많이 들 수 있습니다. gRPC 호출에 채널을 재사용하면 성능 혜택이 있습니다.
* gRPC 클라이언트는 채널을 사용하여 만듭니다. gRPC 클라이언트는 경량 개체이며 캐시하거나 재사용할 필요가 없습니다.
* 다양한 형식의 클라이언트를 포함하여 여러 gRPC 클라이언트를 한 채널에서 만들 수 있습니다.
* 채널 및 채널에서 만든 클라이언트를 여러 스레드에서 안전하게 사용할 수 있습니다.
* 채널에서 만든 클라이언트는 여러 개의 동시 호출을 수행할 수 있습니다.

`GrpcChannel.ForAddress`가 gRPC 클라이언트를 만들기 위한 유일한 옵션은 아닙니다. ASP.NET Core 앱에서 gRPC 서비스를 호출하는 경우 [gRPC 클라이언트 팩터리 통합](xref:grpc/clientfactory)을 확인합니다. `HttpClientFactory`와 gRPC 통합은 중앙에서 gRPC 클라이언트를 만드는 대체 방법을 제공합니다.

> [!NOTE]
> `Grpc.Net.Client`를 사용하여 HTTP/2를 통해 gRPC를 호출하는 기능은 현재 Xamarin에서 지원되지 않습니다. 향후 Xamarin 릴리스에서 HTTP/2 지원을 개선하기 위해 노력하고 있습니다. 현재 실행 가능한 대체 방법은 [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 및 [gRPC-Web](xref:grpc/browser)입니다.

## <a name="make-grpc-calls"></a>gRPC 호출 수행

클라이언트에서 메서드를 호출하여 gRPC 호출을 시작합니다. gRPC 클라이언트는 메시지 직렬화 및 gRPC 호출에 올바른 서비스 주소를 지정하는 작업을 처리합니다.

gRPC에는 다양한 형식의 메서드가 있습니다. 클라이언트를 사용하여 gRPC 호출을 수행하는 방법은 호출되는 메서드 형식에 따라 다릅니다. gRPC 메서드 형식은 다음과 같습니다.

* 단항
* 서버 스트리밍
* 클라이언트 스트리밍
* 양방향 스트리밍

### <a name="unary-call"></a>단항 호출

단항 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작합니다. 서비스가 완료되면 응답 메시지가 반환됩니다.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

\* *.proto* 파일에 있는 각 단항 서비스 메서드는 구체적인 gRPC 클라이언트 형식에 해당 메서드를 호출하기 위한 두 가지 .NET 메서드인 비동기 메서드와 차단 메서드를 생성합니다. 예를 들어 `GreeterClient`에서 `SayHello`를 호출하는 방법에는 다음 두 가지가 있습니다.

* `GreeterClient.SayHelloAsync` - `Greeter.SayHello` 서비스를 비동기적으로 호출합니다. 대기할 수 있습니다.
* `GreeterClient.SayHello` - `Greeter.SayHello` 서비스를 호출하고 완료될 때까지 차단합니다. 비동기 코드에서는 사용하면 안 됩니다.

### <a name="server-streaming-call"></a>서버 스트리밍 호출

서버 스트리밍 호출은 클라이언트에서 요청 메시지를 보내는 것으로 시작됩니다. `ResponseStream.MoveNext()`는 서비스에서 스트리밍된 메시지를 읽습니다. `ResponseStream.MoveNext()`에서 `false`를 반환하면 서버 스트리밍 호출이 완료됩니다.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

C# 8 이상을 사용하는 경우 `await foreach` 구문을 사용하여 메시지를 읽을 수 있습니다. `IAsyncStreamReader<T>.ReadAllAsync()` 확장 메서드는 응답 스트림에서 모든 메시지를 읽습니다.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>클라이언트 스트리밍 호출

클라이언트 스트리밍 호출은 클라이언트에서 메시지를 보내지 ‘않고’ 시작됩니다. 클라이언트는 `RequestStream.WriteAsync`를 사용하여 메시지를 보내도록 선택할 수 있습니다. 클라이언트가 메시지 전송을 완료하면 서비스에 알리기 위해 `RequestStream.CompleteAsync()`를 호출해야 합니다. 서비스에서 응답 메시지를 반환하면 호출이 완료됩니다.

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

### <a name="bi-directional-streaming-call"></a>양방향 스트리밍 호출

양방향 스트리밍 호출은 클라이언트에서 메시지를 보내지 ‘않고’ 시작됩니다. 클라이언트는 `RequestStream.WriteAsync`를 사용하여 메시지를 보내도록 선택할 수 있습니다. 서비스에서 스트리밍된 메시지는 `ResponseStream.MoveNext()` 또는 `ResponseStream.ReadAllAsync()`를 사용하여 액세스할 수 있습니다. `ResponseStream`에 더는 메시지가 없으면 양방향 스트리밍 호출이 완료됩니다.

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

최적의 성능을 얻고 클라이언트 및 서비스에서 불필요한 오류를 방지하려면 양방향 스트리밍 호출을 정상적으로 완료해 보세요. 서버에서 요청 스트림 읽기를 마치고 클라이언트에서 응답 스트림 읽기를 완료하면 양방향 호출이 정상적으로 완료됩니다. 위의 샘플 호출은 정상적으로 종료되는 양방향 호출의 한 예입니다. 호출에서 클라이언트는 다음을 수행합니다.

1. `EchoClient.Echo`를 호출하여 새 양방향 스트리밍 호출을 시작합니다.
2. `ResponseStream.ReadAllAsync()`를 사용하여 서비스에서 메시지를 읽는 백그라운드 작업을 만듭니다.
3. `RequestStream.WriteAsync`를 사용하여 메시지를 서버에 보냅니다.
4. `RequestStream.CompleteAsync()`를 사용하여 메시지를 보냈다고 서버에 알립니다.
5. 백그라운드 작업에서 들어오는 모든 메시지를 읽을 때까지 기다립니다.

양방향 스트리밍 호출 중에 클라이언트와 서비스는 언제든지 서로에게 메시지를 보낼 수 있습니다. 양방향 호출과 상호 작용하는 데 가장 적합한 클라이언트 논리는 서비스 논리에 따라 다릅니다.

## <a name="access-grpc-trailers"></a>gRPC 트레일러 액세스

gRPC 호출은 gRPC 트레일러를 반환할 수 있습니다. gRPC 트레일러는 호출에 대한 이름/값 메타데이터를 제공하는 데 사용됩니다. 트레일러는 HTTP 헤더와 유사한 기능을 제공하지만 호출이 끝날 때 수신됩니다.

gRPC 트레일러는 메타데이터의 컬렉션을 반환하는 `GetTrailers()`를 사용하여 액세스할 수 있습니다. 응답이 완료된 후 트레일러가 반환되므로 모든 응답 메시지를 대기한 이후에 트레일러에 액세스해야 합니다.

단항 및 클라이언트 스트리밍 호출은 `GetTrailers()`를 호출하기 전에 `ResponseAsync`를 대기해야 합니다.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

서버 및 양방향 스트리밍 호출은 `GetTrailers()`를 호출하기 전에 응답 스트림 대기를 완료해야 합니다.

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

`RpcException`에서도 gRPC 트레일러에 액세스할 수 있습니다. 서비스에서 비정상 gRPC 상태와 함께 트레일러를 반환할 수 있습니다. 이 경우에는 gRPC 클라이언트에서 throw된 예외에서 트레일러가 검색됩니다.

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

## <a name="configure-deadline"></a>최종 기한 구성

gRPC 호출 최종 기한 구성은 호출을 실행할 수 있는 기간에 대한 상한을 제공하기 때문에 권장됩니다. 또한 오작동하는 서비스가 계속 실행되어 서버 리소스가 소진되는 것을 방지합니다. 최종 기한은 안정적인 앱을 빌드하는 데 유용한 도구입니다.

`CallOptions.Deadline`을 구성하여 gRPC 호출의 최종 기한을 설정합니다.

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

자세한 내용은 <xref:grpc/deadlines-cancellation#deadlines>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
