---
title: ASP.NET Core에서 스트리밍 사용 SignalR
author: bradygaster
description: 클라이언트와 서버 간에 데이터를 스트리밍하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
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
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690636"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a>ASP.NET Core에서 스트리밍 사용 SignalR

만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core는 SignalR 클라이언트에서 서버로 및 서버에서 클라이언트로의 스트리밍을 지원 합니다. 이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다. 스트리밍할 때 모든 데이터를 사용할 수 있을 때까지 기다리지 않고 각 조각이 사용할 수 있게 되는 즉시 클라이언트 또는 서버로 전송 됩니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR 는 서버 메서드의 스트리밍 반환 값을 지원 합니다. 이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다. 반환 값이 클라이언트로 스트리밍되는 경우 모든 데이터를 사용할 수 있을 때까지 기다리지 않고 각 조각이 사용할 수 있게 되는 즉시 클라이언트에 전송 됩니다.

::: moniker-end

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>스트리밍을 위한 허브 설정

::: moniker range=">= aspnetcore-3.0"

허브 메서드는,, 또는를 반환할 때 자동으로 스트리밍 허브 메서드가 됩니다 <xref:System.Collections.Generic.IAsyncEnumerable`1> <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

허브 메서드는 또는를 반환 하는 경우 자동으로 스트리밍 허브 메서드가 됩니다 <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍

::: moniker range=">= aspnetcore-3.0"

스트리밍 허브 메서드는 `IAsyncEnumerable<T>` 외에도를 반환할 수 있습니다 `ChannelReader<T>` . 을 반환 하는 가장 간단한 방법은 `IAsyncEnumerable<T>` 다음 샘플에서 보여 주는 것 처럼 허브 메서드를 비동기 반복기 메서드로 만드는 것입니다. 허브 비동기 반복기 메서드는 `CancellationToken` 클라이언트가 스트림에서 구독을 취소 하는 경우 트리거되는 매개 변수를 사용할 수 있습니다. 비동기 반복기 메서드는 `ChannelReader` 를 완료 하지 않고 가장 일찍 반환 하거나 메서드를 종료 하는 등 채널에 공통적인 문제를 방지 <xref:System.Threading.Channels.ChannelWriter`1> 합니다.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

다음 샘플에서는 채널을 사용 하 여 클라이언트에 스트리밍 데이터의 기본 사항을 보여 줍니다. 개체를에 쓸 때마다 <xref:System.Threading.Channels.ChannelWriter%601> 개체가 클라이언트에 즉시 전송 됩니다. 끝에 `ChannelWriter` 이 완료 되 면 클라이언트에 스트림이 닫히도록 지시 합니다.

> [!NOTE]
> `ChannelWriter<T>`백그라운드 스레드에서에 쓰고 가능한 한 빨리를 반환 합니다 `ChannelReader` . 다른 허브 호출은가 반환 될 때까지 차단 됩니다 `ChannelReader` .
>
> [ `try ... catch` 문에서](/dotnet/csharp/language-reference/keywords/try-catch)논리를 래핑합니다. `Channel` [ `finally` 블록](/dotnet/csharp/language-reference/keywords/try-catch-finally)에서를 완료 합니다. 오류를 전달 하려면 `catch` 블록 내에서 캡처한 다음 블록에 씁니다 `finally` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

서버-클라이언트 스트리밍 허브 메서드는 `CancellationToken` 클라이언트가 스트림에서 구독을 취소 하는 경우 트리거되는 매개 변수를 받아들일 수 있습니다. 이 토큰을 사용 하 여 서버 작업을 중지 하 고 클라이언트에서 스트림의 끝 이전에 연결을 해제 하는 경우 모든 리소스를 해제 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>클라이언트와 서버 간 스트리밍

허브 메서드는 또는 형식의 개체를 하나 이상 허용 하는 경우 자동으로 클라이언트-서버 스트리밍 허브 메서드가 됩니다 <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> . 다음 샘플에서는 클라이언트에서 보낸 스트리밍 데이터를 읽는 방법의 기본 사항을 보여 줍니다. 클라이언트에서에 쓸 때마다 <xref:System.Threading.Channels.ChannelWriter%601> 데이터가 `ChannelReader` 허브 메서드가 읽고 있는 서버의에 기록 됩니다.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<xref:System.Collections.Generic.IAsyncEnumerable%601>메서드 버전은 다음과 같습니다.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>.NET 클라이언트

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍


::: moniker range=">= aspnetcore-3.0"

`StreamAsync`의 및 `StreamAsChannelAsync` 메서드는 `HubConnection` 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. 허브 메서드에 정의 된 허브 메서드 이름 및 인수를 또는에 전달 `StreamAsync` 합니다 `StreamAsChannelAsync` . 및의 제네릭 매개 변수는 `StreamAsync<T>` `StreamAsChannelAsync<T>` 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다. 또는 형식의 개체 `IAsyncEnumerable<T>` `ChannelReader<T>` 는 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.

`StreamAsync`다음을 반환 하는 예제 `IAsyncEnumerable<int>` :

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

다음을 `StreamAsChannelAsync` 반환 하는 해당 예제 `ChannelReader<int>` :

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

`StreamAsChannelAsync`의 메서드는 `HubConnection` 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. 허브 메서드에 정의 된 허브 메서드 이름 및 인수를에 전달 `StreamAsChannelAsync` 합니다. 의 제네릭 매개 변수는 `StreamAsChannelAsync<T>` 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다. 는 `ChannelReader<T>` 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`StreamAsChannelAsync`의 메서드는 `HubConnection` 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다. 허브 메서드에 정의 된 허브 메서드 이름 및 인수를에 전달 `StreamAsChannelAsync` 합니다. 의 제네릭 매개 변수는 `StreamAsChannelAsync<T>` 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다. 는 `ChannelReader<T>` 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>클라이언트와 서버 간 스트리밍

.NET 클라이언트에서 클라이언트-서버 스트리밍 허브 메서드를 호출 하는 방법에는 두 가지가 있습니다. `IAsyncEnumerable<T>` `ChannelReader` `SendAsync` `InvokeAsync` `StreamAsChannelAsync` 호출 된 허브 메서드에 따라 또는를, 또는에 인수로 전달할 수 있습니다.

또는 개체에 데이터를 쓸 `IAsyncEnumerable` 때마다 `ChannelWriter` 서버의 허브 메서드는 클라이언트의 데이터가 포함 된 새 항목을 받습니다.

개체를 사용 하는 경우 스트림 `IAsyncEnumerable` 항목을 반환 하는 메서드는 종료 된 후에 종료 됩니다.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

또는를 사용 하는 경우 다음을 사용 하 여 `ChannelWriter` 채널을 완성 합니다 `channel.Writer.Complete()` .

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript 클라이언트

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍

JavaScript 클라이언트는를 사용 하 여 허브에서 서버-클라이언트 스트리밍 메서드를 호출 `connection.stream` 합니다. `stream`메서드는 두 개의 인수를 허용 합니다.

* 허브 메서드의 이름입니다. 다음 예제에서 허브 메서드 이름은 `Counter` 입니다.
* 매개 변수가 허브 메서드에 정의 되어 있습니다. 다음 예제에서 인수는 받을 스트림 항목 수와 스트림 항목 간의 지연 개수입니다.

`connection.stream``IStreamResult`메서드를 포함 하는을 반환 합니다 `subscribe` . `IStreamSubscriber`를에 전달 하 `subscribe` 고 `next` , `error` 및 `complete` 콜백을 호출에서 알림을 받도록 설정 합니다 `stream` .

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

클라이언트에서 스트림을 종료 하려면 `dispose` 메서드에서 반환 된에 대해 메서드를 호출 합니다 `ISubscription` `subscribe` . 이 메서드를 호출 하면 `CancellationToken` 허브 메서드의 매개 변수를 취소 하 게 됩니다 (제공 된 경우).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

클라이언트에서 스트림을 종료 하려면 `dispose` 메서드에서 반환 된에 대해 메서드를 호출 합니다 `ISubscription` `subscribe` .

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>클라이언트와 서버 간 스트리밍

JavaScript 클라이언트는 `Subject` `send` `invoke` `stream` 호출 된 허브 메서드에 따라를, 또는에 인수로 전달 하 여 허브에서 클라이언트-서버 스트리밍 메서드를 호출 합니다. 는 `Subject` 처럼 보이는 클래스입니다 `Subject` . 예를 들어 RxJS에서 해당 라이브러리의 [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) 클래스를 사용할 수 있습니다.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

`subject.next(item)`항목을 사용 하 여를 호출 하면 항목이 스트림에 기록 되 고 허브 메서드는 서버에서 항목을 받습니다.

스트림을 종료 하려면를 호출 `subject.complete()` 합니다.

## <a name="java-client"></a>Java 클라이언트

### <a name="server-to-client-streaming"></a>서버-클라이언트 스트리밍

SignalRJava 클라이언트는 메서드를 사용 하 여 `stream` 스트리밍 메서드를 호출 합니다. `stream` 세 개 이상의 인수를 허용 합니다.

* 예상 되는 스트림 항목 유형입니다.
* 허브 메서드의 이름입니다.
* 매개 변수가 허브 메서드에 정의 되어 있습니다.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream`의 메서드는 `HubConnection` 스트림 항목 형식의 관찰 가능 개체를 반환 합니다. 관찰 가능한 형식의 `subscribe` 메서드는 `onNext` , `onError` 및 처리기가 `onCompleted` 정의 됩니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
