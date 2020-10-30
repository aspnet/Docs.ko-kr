---
title: ASP.NET Core SignalR 소개
author: bradygaster
description: ASP.NET Core SignalR 라이브러리가 앱에 실시간 기능 추가를 간소화 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 1810fef903362addcef4a6c9ec53264604f58d2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051475"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a>ASP.NET Core SignalR 소개

## <a name="what-is-no-locsignalr"></a>SignalR란?

ASP.NET Core SignalR 는 응용 프로그램에 실시간 웹 기능 추가를 간소화 하는 오픈 소스 라이브러리입니다. 실시간 웹 기능을 사용 하면 서버측 코드에서 클라이언트에 콘텐츠를 즉시 푸시할 수 있습니다.

적합 한 후보 SignalR :

* 서버에서 자주 업데이트해야 하는 앱. 예를 들어 게임, 소셜 네트워크, 투표, 경매, 지도 및 GPS 앱이 있습니다.
* 대시보드 및 모니터링 앱. 예를 들어 회사 대시보드, 즉석 판매 업데이트 또는 여행 경고가 있습니다.
* 공동 작업 앱. 예를 들어 화이트보드 앱 및 팀 회의 소프트웨어가 있습니다.
* 알림이 필요한 앱. 소셜 네트워크, 이메일, 채팅, 게임, 여행 경고 및 다른 많은 앱에서 알림을 사용합니다.

SignalR 는 서버-클라이언트 [원격 프로시저 호출 (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)을 만들기 위한 API를 제공 합니다. Rpc는 서버 쪽 .NET Core 코드에서 클라이언트에 대 한 JavaScript 함수를 호출 합니다.

SignalRASP.NET Core에 대 한 몇 가지 기능은 다음과 같습니다.

* 연결 관리를 자동으로 처리 합니다.
* 모든 연결 된 클라이언트에 동시에 메시지를 보냅니다. 예를 들어 대화방입니다.
* 특정 클라이언트나 클라이언트 그룹으로 메시지를 보냅니다.
* 늘어난 트래픽을 처리 하도록 크기를 조정 합니다.

소스는 [ SignalR GitHub의 리포지토리에서](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)호스팅됩니다.

## <a name="transports"></a>전송

SignalR 에서는 실시간 통신을 처리 하는 다음과 같은 기술을 지원 합니다 (정상적인 대체 순서).

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Server-Sent 이벤트
* 긴 폴링

SignalR 는 서버 및 클라이언트의 기능 내에서 가장 적합 한 전송 방법을 자동으로 선택 합니다.

## <a name="hubs"></a>허브

SignalR*허브* 를 사용 하 여 클라이언트와 서버 간에 통신 합니다.

허브는 클라이언트와 서버가 서로 메서드를 호출할 수 있도록 하는 고급 파이프라인입니다. SignalR 클라이언트에서 서버에 대 한 메서드를 호출할 수 있도록 하 고 그 반대의 경우도 가능 하 게 하는 컴퓨터 경계 간 디스패치를 처리 합니다. 강력한 형식의 매개 변수를 메서드에 전달 하 여 모델 바인딩을 사용할 수 있습니다. SignalR 에서는 두 가지 기본 제공 허브 프로토콜인 JSON 기반의 텍스트 프로토콜과 [MessagePack](https://msgpack.org/)기반 이진 프로토콜을 제공 합니다.  MessagePack는 일반적으로 JSON과 비교 하 여 더 작은 메시지를 만듭니다. 이전 브라우저는 MessagePack 프로토콜 지원을 제공 하기 위해 [Xhr 수준 2](https://caniuse.com/#feat=xhr2) 를 지원 해야 합니다.

허브는 클라이언트 쪽 메서드의 이름 및 매개 변수를 포함 하는 메시지를 전송 하 여 클라이언트 쪽 코드를 호출 합니다. 메서드 매개 변수로 보낸 개체는 구성 된 프로토콜을 사용 하 여 deserialize 됩니다. 클라이언트는 클라이언트 쪽 코드의 메서드와 이름을 일치 시 키 려 고 시도 합니다. 클라이언트에서 일치 하는 항목을 찾으면 메서드를 호출 하 여 deserialize 된 매개 변수 데이터를 전달 합니다.

## <a name="additional-resources"></a>추가 리소스

* [SignalRASP.NET Core에 대 한 시작](xref:tutorials/signalr)
* [지원 되는 플랫폼](xref:signalr/supported-platforms)
* [허브](xref:signalr/hubs)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
