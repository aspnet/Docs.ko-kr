---
title: ASP.NET Core SignalR 클라이언트
author: bradygaster
description: 다양 한 ASP.NET Core 클라이언트에서 지 원하는 기능에 대해 알아봅니다 SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/client-features
ms.openlocfilehash: 65ad619bb943a0e5ffbaec7ec80243972dafc49f
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024667"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>ASP.NET Core SignalR 클라이언트

## <a name="versioning-support-and-compatibility"></a>버전 관리, 지원 및 호환성

SignalR클라이언트는 서버 구성 요소와 함께 제공 되며 일치 하도록 버전이 지정 됩니다. 지원 되는 모든 클라이언트는 지원 되는 모든 서버에 안전 하 게 연결할 수 있으며, 모든 호환성 문제는 버그를 수정 하는 것으로 간주 됩니다. SignalR 클라이언트는 .NET Core의 나머지 부분과 동일한 지원 수명 주기에서 지원 됩니다. 자세한 내용은 [.Net Core 지원 정책을](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 참조 하세요.

많은 기능에는 호환 되는 클라이언트 **와** 서버가 필요 합니다. 다양 한 기능에 대 한 최소 버전을 보여 주는 표는 아래를 참조 하세요.

1.x 버전의는 SignalR 2.1 및 2.2 .Net Core 릴리스에 매핑되고 수명이 동일 합니다. 버전 3.x 이상에서 SignalR 버전은 .net의 나머지 부분과 정확 하 게 일치 하며 동일한 지원 수명 주기를 가집니다.

| SignalR 버전 | .NET Core 버전 | 지원 수준 | 지원 종료 |
| - | - | - | - |
| 1.0. x | 2.1.x | 장기적 지원 | 2021 년 8 월 21 일 |
| 1.1. x | 2.2. x | 수명 종료 | 2019 년 12 월 23 일 |
| 3(sp3) 이상 | *버전과 동일 SignalR* | [.Net Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 을 참조 하세요. |

**참고:** ASP.NET Core 3.0에서 JavaScript 클라이언트는  `@microsoft/signalr` npm 패키지로 이동 했습니다.

## <a name="feature-distribution"></a>기능 배포

다음 표에서는 실시간 지원을 제공 하는 클라이언트에 대 한 기능 및 지원을 보여 줍니다. 각 기능에 대해이 기능을 지 원하는 *최소* 버전이 나열 됩니다. 버전이 나열 되지 않은 경우에는 기능이 지원 되지 않습니다.

| 기능 | 서버 | .NET 클라이언트 | JavaScript 클라이언트 | Java 클라이언트 |
| ---- | :-: | :-: | :-: | :-: |
| Azure SignalR 서비스 지원 |2.1.0|1.0.0|1.0.0|1.0.0|
| [서버-클라이언트 스트리밍](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [클라이언트와 서버 간 스트리밍](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| 자동 다시 연결 ([.net](./dotnet-client.md?tabs=visual-studio&view=aspnetcore-3.0#handle-lost-connection), [JavaScript](./javascript-client.md?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Websocket 전송 |2.1.0|1.0.0|1.0.0|1.0.0|
| Server-Sent 이벤트 전송 |2.1.0|1.0.0|1.0.0|❌|
| 긴 폴링 전송 |2.1.0|1.0.0|1.0.0|3.0.0|
| JSON 허브 프로토콜 |2.1.0|1.0.0|1.0.0|1.0.0|
| MessagePack 허브 프로토콜 |2.1.0|1.0.0|1.0.0|5.0.0|

추가 클라이언트 기능 사용에 대 한 지원은 [문제 추적기](https://github.com/dotnet/AspNetCore/issues)에서 추적 됩니다.

## <a name="additional-resources"></a>추가 리소스

* [SignalRASP.NET Core에 대 한 시작](xref:tutorials/signalr)
* [지원되는 플랫폼](xref:signalr/supported-platforms)
* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)