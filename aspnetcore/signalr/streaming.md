---
title: 'ASP.NET Core에서 스트리밍 사용 :::no-loc(SignalR):::'
author: bradygaster
description: 클라이언트와 서버 간에 데이터를 스트리밍하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
no-loc:
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
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690636"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="826e7-103">ASP.NET Core에서 스트리밍 사용 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="826e7-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="826e7-104">만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="826e7-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="826e7-105">ASP.NET Core는 :::no-loc(SignalR)::: 클라이언트에서 서버로 및 서버에서 클라이언트로의 스트리밍을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="826e7-106">이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="826e7-107">스트리밍할 때 모든 데이터를 사용할 수 있을 때까지 기다리지 않고 각 조각이 사용할 수 있게 되는 즉시 클라이언트 또는 서버로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="826e7-108">ASP.NET Core :::no-loc(SignalR)::: 는 서버 메서드의 스트리밍 반환 값을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="826e7-109">이는 시간이 지남에 따라 데이터 조각이 도착 하는 시나리오에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="826e7-110">반환 값이 클라이언트로 스트리밍되는 경우 모든 데이터를 사용할 수 있을 때까지 기다리지 않고 각 조각이 사용할 수 있게 되는 즉시 클라이언트에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="826e7-111">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="826e7-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="826e7-112">스트리밍을 위한 허브 설정</span><span class="sxs-lookup"><span data-stu-id="826e7-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="826e7-113">허브 메서드는,, 또는를 반환할 때 자동으로 스트리밍 허브 메서드가 됩니다 <xref:System.Collections.Generic.IAsyncEnumerable`1> <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="826e7-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="826e7-114">허브 메서드는 또는를 반환 하는 경우 자동으로 스트리밍 허브 메서드가 됩니다 <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="826e7-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="826e7-115">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="826e7-116">스트리밍 허브 메서드는 `IAsyncEnumerable<T>` 외에도를 반환할 수 있습니다 `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="826e7-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="826e7-117">을 반환 하는 가장 간단한 방법은 `IAsyncEnumerable<T>` 다음 샘플에서 보여 주는 것 처럼 허브 메서드를 비동기 반복기 메서드로 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="826e7-118">허브 비동기 반복기 메서드는 `CancellationToken` 클라이언트가 스트림에서 구독을 취소 하는 경우 트리거되는 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="826e7-119">비동기 반복기 메서드는 `ChannelReader` 를 완료 하지 않고 가장 일찍 반환 하거나 메서드를 종료 하는 등 채널에 공통적인 문제를 방지 <xref:System.Threading.Channels.ChannelWriter`1> 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="826e7-120">다음 샘플에서는 채널을 사용 하 여 클라이언트에 스트리밍 데이터의 기본 사항을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="826e7-121">개체를에 쓸 때마다 <xref:System.Threading.Channels.ChannelWriter%601> 개체가 클라이언트에 즉시 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="826e7-122">끝에 `ChannelWriter` 이 완료 되 면 클라이언트에 스트림이 닫히도록 지시 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="826e7-123">`ChannelWriter<T>`백그라운드 스레드에서에 쓰고 가능한 한 빨리를 반환 합니다 `ChannelReader` .</span><span class="sxs-lookup"><span data-stu-id="826e7-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="826e7-124">다른 허브 호출은가 반환 될 때까지 차단 됩니다 `ChannelReader` .</span><span class="sxs-lookup"><span data-stu-id="826e7-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="826e7-125">[ `try ... catch` 문에서](/dotnet/csharp/language-reference/keywords/try-catch)논리를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="826e7-126">`Channel` [ `finally` 블록](/dotnet/csharp/language-reference/keywords/try-catch-finally)에서를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="826e7-127">오류를 전달 하려면 `catch` 블록 내에서 캡처한 다음 블록에 씁니다 `finally` .</span><span class="sxs-lookup"><span data-stu-id="826e7-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

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

<span data-ttu-id="826e7-128">서버-클라이언트 스트리밍 허브 메서드는 `CancellationToken` 클라이언트가 스트림에서 구독을 취소 하는 경우 트리거되는 매개 변수를 받아들일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="826e7-129">이 토큰을 사용 하 여 서버 작업을 중지 하 고 클라이언트에서 스트림의 끝 이전에 연결을 해제 하는 경우 모든 리소스를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="826e7-130">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-130">Client-to-server streaming</span></span>

<span data-ttu-id="826e7-131">허브 메서드는 또는 형식의 개체를 하나 이상 허용 하는 경우 자동으로 클라이언트-서버 스트리밍 허브 메서드가 됩니다 <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="826e7-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="826e7-132">다음 샘플에서는 클라이언트에서 보낸 스트리밍 데이터를 읽는 방법의 기본 사항을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="826e7-133">클라이언트에서에 쓸 때마다 <xref:System.Threading.Channels.ChannelWriter%601> 데이터가 `ChannelReader` 허브 메서드가 읽고 있는 서버의에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="826e7-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>메서드 버전은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="826e7-135">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="826e7-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="826e7-136">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="826e7-137">`StreamAsync`의 및 `StreamAsChannelAsync` 메서드는 `HubConnection` 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="826e7-138">허브 메서드에 정의 된 허브 메서드 이름 및 인수를 또는에 전달 `StreamAsync` 합니다 `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="826e7-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="826e7-139">및의 제네릭 매개 변수는 `StreamAsync<T>` `StreamAsChannelAsync<T>` 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="826e7-140">또는 형식의 개체 `IAsyncEnumerable<T>` `ChannelReader<T>` 는 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="826e7-141">`StreamAsync`다음을 반환 하는 예제 `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="826e7-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="826e7-142">다음을 `StreamAsChannelAsync` 반환 하는 해당 예제 `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="826e7-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="826e7-143">`StreamAsChannelAsync`의 메서드는 `HubConnection` 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="826e7-144">허브 메서드에 정의 된 허브 메서드 이름 및 인수를에 전달 `StreamAsChannelAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="826e7-145">의 제네릭 매개 변수는 `StreamAsChannelAsync<T>` 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="826e7-146">는 `ChannelReader<T>` 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="826e7-147">`StreamAsChannelAsync`의 메서드는 `HubConnection` 서버-클라이언트 스트리밍 메서드를 호출 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="826e7-148">허브 메서드에 정의 된 허브 메서드 이름 및 인수를에 전달 `StreamAsChannelAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="826e7-149">의 제네릭 매개 변수는 `StreamAsChannelAsync<T>` 스트리밍 메서드에서 반환 하는 개체의 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="826e7-150">는 `ChannelReader<T>` 스트림 호출에서 반환 되 고 클라이언트의 스트림을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="826e7-151">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-151">Client-to-server streaming</span></span>

<span data-ttu-id="826e7-152">.NET 클라이언트에서 클라이언트-서버 스트리밍 허브 메서드를 호출 하는 방법에는 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="826e7-153">`IAsyncEnumerable<T>` `ChannelReader` `SendAsync` `InvokeAsync` `StreamAsChannelAsync` 호출 된 허브 메서드에 따라 또는를, 또는에 인수로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="826e7-154">또는 개체에 데이터를 쓸 `IAsyncEnumerable` 때마다 `ChannelWriter` 서버의 허브 메서드는 클라이언트의 데이터가 포함 된 새 항목을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="826e7-155">개체를 사용 하는 경우 스트림 `IAsyncEnumerable` 항목을 반환 하는 메서드는 종료 된 후에 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="826e7-156">또는를 사용 하는 경우 다음을 사용 하 여 `ChannelWriter` 채널을 완성 합니다 `channel.Writer.Complete()` .</span><span class="sxs-lookup"><span data-stu-id="826e7-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="826e7-157">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="826e7-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="826e7-158">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-158">Server-to-client streaming</span></span>

<span data-ttu-id="826e7-159">JavaScript 클라이언트는를 사용 하 여 허브에서 서버-클라이언트 스트리밍 메서드를 호출 `connection.stream` 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="826e7-160">`stream`메서드는 두 개의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="826e7-161">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-161">The name of the hub method.</span></span> <span data-ttu-id="826e7-162">다음 예제에서 허브 메서드 이름은 `Counter` 입니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="826e7-163">매개 변수가 허브 메서드에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="826e7-164">다음 예제에서 인수는 받을 스트림 항목 수와 스트림 항목 간의 지연 개수입니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="826e7-165">`connection.stream``IStreamResult`메서드를 포함 하는을 반환 합니다 `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="826e7-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="826e7-166">`IStreamSubscriber`를에 전달 하 `subscribe` 고 `next` , `error` 및 `complete` 콜백을 호출에서 알림을 받도록 설정 합니다 `stream` .</span><span class="sxs-lookup"><span data-stu-id="826e7-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="826e7-167">클라이언트에서 스트림을 종료 하려면 `dispose` 메서드에서 반환 된에 대해 메서드를 호출 합니다 `ISubscription` `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="826e7-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="826e7-168">이 메서드를 호출 하면 `CancellationToken` 허브 메서드의 매개 변수를 취소 하 게 됩니다 (제공 된 경우).</span><span class="sxs-lookup"><span data-stu-id="826e7-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="826e7-169">클라이언트에서 스트림을 종료 하려면 `dispose` 메서드에서 반환 된에 대해 메서드를 호출 합니다 `ISubscription` `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="826e7-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="826e7-170">클라이언트와 서버 간 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-170">Client-to-server streaming</span></span>

<span data-ttu-id="826e7-171">JavaScript 클라이언트는 `Subject` `send` `invoke` `stream` 호출 된 허브 메서드에 따라를, 또는에 인수로 전달 하 여 허브에서 클라이언트-서버 스트리밍 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="826e7-172">는 `Subject` 처럼 보이는 클래스입니다 `Subject` .</span><span class="sxs-lookup"><span data-stu-id="826e7-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="826e7-173">예를 들어 RxJS에서 해당 라이브러리의 [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="826e7-174">`subject.next(item)`항목을 사용 하 여를 호출 하면 항목이 스트림에 기록 되 고 허브 메서드는 서버에서 항목을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="826e7-175">스트림을 종료 하려면를 호출 `subject.complete()` 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="826e7-176">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="826e7-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="826e7-177">서버-클라이언트 스트리밍</span><span class="sxs-lookup"><span data-stu-id="826e7-177">Server-to-client streaming</span></span>

<span data-ttu-id="826e7-178">:::no-loc(SignalR):::Java 클라이언트는 메서드를 사용 하 여 `stream` 스트리밍 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="826e7-179">`stream` 세 개 이상의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="826e7-180">예상 되는 스트림 항목 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="826e7-181">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-181">The name of the hub method.</span></span>
* <span data-ttu-id="826e7-182">매개 변수가 허브 메서드에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="826e7-183">`stream`의 메서드는 `HubConnection` 스트림 항목 형식의 관찰 가능 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="826e7-184">관찰 가능한 형식의 `subscribe` 메서드는 `onNext` , `onError` 및 처리기가 `onCompleted` 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="826e7-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="826e7-185">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="826e7-185">Additional resources</span></span>

* [<span data-ttu-id="826e7-186">허브</span><span class="sxs-lookup"><span data-stu-id="826e7-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="826e7-187">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="826e7-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="826e7-188">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="826e7-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="826e7-189">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="826e7-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
