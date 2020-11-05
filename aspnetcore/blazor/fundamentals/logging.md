---
title: 'ASP.NET Core :::no-loc(Blazor)::: 로깅'
author: guardrex
description: '로그 수준 구성, :::no-loc(Razor)::: 구성 요소로부터 로그 메시지를 작성하는 방법을 포함하여 :::no-loc(Blazor)::: 앱의 로깅에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055986"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="2964a-103">ASP.NET Core :::no-loc(Blazor)::: 로깅</span><span class="sxs-lookup"><span data-stu-id="2964a-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="2964a-104">`Program.Main`의 `WebAssemblyHostBuilder.Logging` 속성을 사용하여 :::no-loc(Blazor WebAssembly)::: 앱에서 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="2964a-105">`Logging` 속성은 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>형식이므로 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>에서 사용할 수 있는 모든 확장 메서드를 `Logging`에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="2964a-106">로깅 구성은 앱 설정 파일에서 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="2964a-107">자세한 내용은 <xref:blazor/fundamentals/configuration#logging-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2964a-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="2964a-108">일반적인 ASP.NET Core 로깅 지침은 <xref:fundamentals/logging/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2964a-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="2964a-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="2964a-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="2964a-110"><xref:Microsoft.Extensions.Logging.ILoggerProvider>를 삽입하여 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>로 전달된 로깅 공급자에 `WebAssemblyConsoleLogger`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="2964a-111">기존의 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>와 달리 `WebAssemblyConsoleLogger`는 브라우저별 로깅 API 주위의 래퍼입니다(예: `console.log`).</span><span class="sxs-lookup"><span data-stu-id="2964a-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="2964a-112">`WebAssemblyConsoleLogger`를 사용하면 브라우저 컨텍스트의 Mono 내에서 로깅을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="2964a-113">:::no-loc(Razor)::: 구성 요소의 로그</span><span class="sxs-lookup"><span data-stu-id="2964a-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="2964a-114">로거는 앱 시작 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="2964a-115"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>와 같은 API의 Intellisense 작성을 지원하려면 <xref:Microsoft.Extensions.Logging>의 `using` 지시문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="2964a-116">다음 예제에서는 :::no-loc(Razor)::: 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILogger>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="2964a-117">다음 예제에서는 :::no-loc(Razor)::: 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2964a-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="2964a-118">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2964a-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
