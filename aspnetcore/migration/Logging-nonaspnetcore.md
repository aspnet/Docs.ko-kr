---
title: Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션
author: pakrym
description: 2.1에서 2.2 또는 3.0로 non-ASP.NET를 사용 하는 Core 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060380"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션

이 문서에서는 `Microsoft.Extensions.Logging` 2.1에서 2.2 또는 3.0로를 사용 하는 Non-ASP.NET Core 응용 프로그램을 마이그레이션하는 일반적인 단계를 간략하게 설명 합니다.

## <a name="21-to-22"></a>2.1-2.2

수동으로 `ServiceCollection` 를 만들고 호출 `AddLogging` 합니다.

2.1 예제:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2.2 예제:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1 ~ 3.0

3.0에서는을 사용 `LoggingFactory.Create` 합니다.

2.1 예제:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0 예제:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>추가 리소스

* [Microsoft. extension. Console NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
