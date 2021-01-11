---
title: ASP.NET Core Blazor 로깅
author: guardrex
description: 로그 수준 구성, Razor 구성 요소로부터 로그 메시지를 작성하는 방법을 포함하여 Blazor 앱의 로깅에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97666835"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="9e24c-103">ASP.NET Core Blazor 로깅</span><span class="sxs-lookup"><span data-stu-id="9e24c-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="9e24c-104"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> 속성을 사용하여 Blazor WebAssembly 앱에서 사용자 지정 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="9e24c-105"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="9e24c-106">`Program.cs`의 `Program.Main`에서 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType>로 최소 로깅 수준을 설정하고 사용자 지정 로깅 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="9e24c-107">`Logging` 속성은 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>형식이므로 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>에서 사용할 수 있는 모든 확장 메서드를 `Logging`에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="9e24c-108">로깅 구성은 앱 설정 파일에서 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="9e24c-109">자세한 내용은 <xref:blazor/fundamentals/configuration#logging-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e24c-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="9e24c-110">SignalR .NET 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="9e24c-110">SignalR .NET client logging</span></span>

<span data-ttu-id="9e24c-111"><xref:Microsoft.Extensions.Logging.ILoggerProvider>를 삽입하여 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>로 전달된 로깅 공급자에 `WebAssemblyConsoleLogger`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="9e24c-112">기존의 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>와 달리 `WebAssemblyConsoleLogger`는 브라우저별 로깅 API 주위의 래퍼입니다(예: `console.log`).</span><span class="sxs-lookup"><span data-stu-id="9e24c-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="9e24c-113">`WebAssemblyConsoleLogger`를 사용하면 브라우저 컨텍스트의 Mono 내에서 로깅을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="9e24c-114">`WebAssemblyConsoleLogger`는 [internal](/dotnet/csharp/language-reference/keywords/internal)이며, 개발자 코드에서 직접 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="9e24c-115"><xref:Microsoft.Extensions.Logging?displayProperty=fullName>의 네임스페이스를 추가하고 <xref:Microsoft.Extensions.Logging.ILoggerProvider>를 구성 요소에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="9e24c-116">구성 요소의 [`OnInitializedAsync` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)에서 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="9e24c-117">Blazor Server와 관련된 일반적인 ASP.NET Core 로깅 지침은 <xref:fundamentals/logging/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e24c-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="9e24c-118">Razor 구성 요소의 로그</span><span class="sxs-lookup"><span data-stu-id="9e24c-118">Log in Razor components</span></span>

<span data-ttu-id="9e24c-119">로거는 앱 시작 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="9e24c-120"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>와 같은 API의 [IntelliSense](/visualstudio/ide/using-intellisense) 작성을 지원하려면 <xref:Microsoft.Extensions.Logging>의 `using` 지시문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="9e24c-121">다음 예제에서는 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILogger>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="9e24c-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e24c-122">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="9e24c-123">다음 예제에서는 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9e24c-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="9e24c-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e24c-124">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="9e24c-125">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9e24c-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
