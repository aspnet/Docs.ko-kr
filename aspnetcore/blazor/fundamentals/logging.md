---
title: ASP.NET Core Blazor 로깅
author: guardrex
description: 로그 수준 구성, Razor 구성 요소로부터 로그 메시지를 작성하는 방법을 포함하여 Blazor 앱의 로깅에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055986"
---
# <a name="aspnet-core-no-locblazor-logging"></a>ASP.NET Core Blazor 로깅

## Blazor WebAssembly

`Program.Main`의 `WebAssemblyHostBuilder.Logging` 속성을 사용하여 Blazor WebAssembly 앱에서 로깅을 구성합니다.

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging` 속성은 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>형식이므로 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>에서 사용할 수 있는 모든 확장 메서드를 `Logging`에서도 사용할 수 있습니다.

로깅 구성은 앱 설정 파일에서 로드할 수 있습니다. 자세한 내용은 <xref:blazor/fundamentals/configuration#logging-configuration>를 참조하세요.

## Blazor Server

일반적인 ASP.NET Core 로깅 지침은 <xref:fundamentals/logging/index>을 참조하세요.

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a>Blazor WebAssembly SignalR .NET 클라이언트 로깅

<xref:Microsoft.Extensions.Logging.ILoggerProvider>를 삽입하여 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>로 전달된 로깅 공급자에 `WebAssemblyConsoleLogger`를 추가합니다. 기존의 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>와 달리 `WebAssemblyConsoleLogger`는 브라우저별 로깅 API 주위의 래퍼입니다(예: `console.log`). `WebAssemblyConsoleLogger`를 사용하면 브라우저 컨텍스트의 Mono 내에서 로깅을 수행할 수 있습니다.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a>Razor 구성 요소의 로그

로거는 앱 시작 구성을 사용합니다.

<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>와 같은 API의 Intellisense 작성을 지원하려면 <xref:Microsoft.Extensions.Logging>의 `using` 지시문이 필요합니다.

다음 예제에서는 Razor 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILogger>를 사용하여 로깅하는 방법을 보여 줍니다.

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

다음 예제에서는 Razor 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 로깅하는 방법을 보여 줍니다.

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

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/logging/index>
