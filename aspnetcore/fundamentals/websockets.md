---
title: ASP.NET Core에서 WebSocket 지원
author: rick-anderson
description: ASP.NET Core에서 Websocket을 시작하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 1ed586745ba4d678272547785c6ffa77aa841392
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588999"
---
# <a name="websockets-support-in-aspnet-core"></a>ASP.NET Core에서 WebSocket 지원

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Andrew Stanton-Nurse](https://github.com/anurse)

본문에서는 ASP.NET Core에서 Websocket을 사용하는 방법을 알아봅니다. [WebSocket](https://wikipedia.org/wiki/WebSocket)([RFC 6455](https://tools.ietf.org/html/rfc6455))은 TCP 연결을 통해 지속적인 양방향 통신 채널을 사용할 수 있도록 해주는 프로토콜입니다. 채팅, 대시보드 및 게임 앱 등 신속한 실시간 통신을 활용하는 앱에서 사용됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/websockets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다. [실행 방법](#sample-app).

## SignalR

[ASP.NET Core SignalR](xref:signalr/introduction)은 앱에 실시간 웹 기능을 추가하는 것을 간소화하는 라이브러리입니다. 가능하면 Websocket을 사용합니다.

대부분의 애플리케이션의 경우 원시 WebSockets보다 SignalR을 권장합니다. SignalR은 WebSockets를 사용할 수 없는 환경에 대한 전송 대체(fallback)를 제공합니다. 기본 원격 프로시저 호출 앱 모델도 제공합니다. 그리고 대부분의 시나리오에서 SignalR은 원시 WebSockets 사용과 비교할 때 큰 성능상의 단점이 없습니다.

일부 앱에 대해 [.NET의 gRPC](xref:grpc/index)는 Websocket을 대신하는 방법을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* ASP.NET Core를 지원하는 모든 OS:  
  * Windows 7/Windows Server 2008 이상
  * Linux
  * macOS  
* IIS가 있는 Windows에서 앱을 실행하는 경우:
  * Windows 8 / Windows Server 2012 이상
  * IIS 8 / IIS 8 Express
  * Websocket을 사용하도록 설정해야 합니다. [IIS/IIS Express 지원](#iisiis-express-support) 섹션을 참조하세요.  
* 앱이 [HTTP.sys](xref:fundamentals/servers/httpsys)에서 실행되는 경우:
  * Windows 8 / Windows Server 2012 이상
* 지원되는 브라우저는 https://caniuse.com/#feat=websockets 를 참조하세요.

## <a name="configure-the-middleware"></a>미들웨어 구성하기

`Startup` 클래스의 `Configure` 메서드에 WebSockets 미들웨어를 추가합니다.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

> [!NOTE]
> 컨트롤러에서 WebSocket 요청을 허용하려면 `app.UseWebSockets`에 대한 호출이 `app.UseEndpoints` 이전에 발생해야 합니다.

::: moniker range="< aspnetcore-2.2"

이때 다음과 같은 설정을 구성할 수 있습니다.

* `KeepAliveInterval` - 프록시가 연결을 유지할 수 있도록 클라이언트로 "핑(ping)" 프레임을 전송하는 빈도입니다. 기본값은 2분입니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

이때 다음과 같은 설정을 구성할 수 있습니다.

* `KeepAliveInterval` - 프록시가 연결을 유지할 수 있도록 클라이언트로 "핑(ping)" 프레임을 전송하는 빈도입니다. 기본값은 2분입니다.
* `AllowedOrigins` - WebSocket 요청에 허용된 원본 헤더 값의 목록입니다. 기본적으로 모든 원본이 허용됩니다. 자세한 내용은 아래의 "WebSocket 원본 제한"을 참조하세요.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>WebSocket 요청 수락하기

요청 수명 주기의 뒷부분에서(예를 들어, `Configure` 메서드나 작업 메서드 뒷부분에서) WebSocket 요청 여부를 확인하고 WebSocket 요청을 수락합니다.

다음 예제는 `Configure` 메서드의 뒷부분에서 나옵니다.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

WebSocket 요청은 모든 URL을 통해서 전달될 수 있지만, 이 예제 코드에서는 `/ws` 에 대한 요청만 수락합니다.

WebSocket을 사용하는 경우 연결 기간 동안 미들웨어 파이프라인이 계속 실행되도록 **해야 합니다**. 미들웨어 파이프라인 종료 후 WebSocket 메시지를 보내거나 받으려고 하면 다음과 같은 예외가 발생할 수 있습니다.

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

백그라운드 서비스를 사용하여 WebSocket에 데이터를 쓰는 경우 미들웨어 파이프라인이 계속 실행되도록 해야 합니다. <xref:System.Threading.Tasks.TaskCompletionSource%601>을 사용하여 이 작업을 수행합니다. `TaskCompletionSource`를 백그라운드 서비스에 전달하고 WebSocket 사용을 완료하면 <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A>를 호출하도록 합니다. 그런 다음, `await`는 요청 중에 다음 예제와 같이 <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> 속성을 만듭니다.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

작업 메서드에서 너무 빨리 반환하면 WebSocket 닫힌 예외가 발생할 수도 있습니다. 작업 메서드에서 소켓을 수락하는 경우 작업 메서드에서 반환하기 전에 소켓을 사용하는 코드가 완료될 때까지 기다립니다.

소켓이 완료될 때까지 대기하려면 `Task.Wait`, `Task.Result` 또는 유사한 차단 호출을 사용하지 마세요. 심각한 스레드 문제가 발생할 수 있습니다. 항상 `await`를 사용합니다.

## <a name="send-and-receive-messages"></a>메시지 보내기 및 받기

`AcceptWebSocketAsync` 메서드는 TCP 연결을 WebSocket 연결로 업그레이드하고 [WebSocket](/dotnet/core/api/system.net.websockets.websocket) 개체를 제공합니다. `WebSocket` 개체를 사용하여 메시지를 보내고 받습니다.

앞에서 살펴본 WebSocket 요청을 수락하는 코드는 `WebSocket` 개체를 `Echo` 메서드로 전달합니다. 코드는 메시지를 수신하고 동일한 메시지를 즉시 보냅니다. 메시지는 클라이언트가 연결을 닫을 때까지 루프에서 보내고 받습니다.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

루프가 시작되기 전에 WebSocket 연결을 수락하면 미들웨어 파이프라인이 종료됩니다. 그리고 소켓을 닫으면 파이프라인이 풀립니다. 즉, 요청은 WebSocket이 수락될 때 파이프라인에서 앞으로 이동하지 않습니다. 루프가 완료되고 소켓이 닫히면 요청이 파이프라인 백업을 진행합니다.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>클라이언트 연결 끊김 처리

연결이 끊어져서 클라이언트 연결이 해제된 경우 서버에 자동으로 알림이 전송되지 않습니다. 클라이언트에서 연결 끊김 메시지를 보내는 경우에만 서버에서 연결 끊김 메시지를 받습니다. 인터넷 연결이 끊긴 경우 메시지가 전송되지 않습니다. 그런 경우 조치를 취하려면 특정 시간 내에서 클라이언트에서 아무런 메시지도 받지 않은 후 시간 제한을 설정합니다.

클라이언트가 항상 메시지를 보내지 않고 연결이 유휴 상태가 되기 때문에 시간 제한을 원하지 않는 경우 클라이언트에서 타이머를 사용하여 X초마다 ping 메시지를 보내도록 합니다. 서버에서 메시지가 이전 메시지 후 2\*X초 이내에 도착하지 않는 경우 연결을 종료하고 클라이언트 연결이 끊겼음을 보고합니다. ping 메시지를 지연시킬 수 있는 네트워크 지연에 대해 추가 시간을 두려면 예상 시간 간격의 2배 동안 기다립니다.

## <a name="websocket-origin-restriction"></a>WebSocket 원본 제한

CORS에서 제공하는 보호 기능은 WebSocket에 적용되지 않습니다. 브라우저는 다음을 수행하지 **않습니다**.

* CORS pre-flight 요청을 수행합니다.
* WebSocket 요청을 생성할 때 `Access-Control` 헤더에 지정된 제한 사항을 준수합니다.

그러나 브라우저는 WebSocket 요청을 발급할 때 `Origin` 헤더를 보냅니다. 애플리케이션은 예상된 원본에서 제공하는 WebSocket만 허용되도록 이러한 헤더의 유효성을 검사하도록 구성되어야 합니다.

"https://server.com "에서 서버를 호스팅하고 "https://client.com"에서 클라이언트를 호스팅하는 경우 `AllowedOrigins` 목록에 "https://client.com"을 추가하여 WebSocket을 확인합니다.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> `Origin` 헤더는 클라이언트에 의해 제어되며 `Referer` 헤더와 마찬가지로 위조될 수 있습니다. 이러한 헤더를 인증 메커니즘으로 사용하지 **마세요**.

::: moniker-end

## <a name="iisiis-express-support"></a>IIS/IIS Express 지원

IIS/IIS Express 8 이상이 있는 Windows Server 2012 이상 및 Windows 8 이상에서는 WebSocket 프로토콜을 지원합니다.

> [!NOTE]
> IIS Express를 사용할 때 Websocket이 항상 활성화됩니다.

### <a name="enabling-websockets-on-iis"></a>IIS에서 Websocket 사용

Windows Server 2012 이상에서 WebSocket 프로토콜을 지원하려면:

> [!NOTE]
> IIS Express를 사용할 때 이러한 단계가 필요하지 않습니다.

1. **관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.
1. **역할 기반 또는 기능 기반 설치** 를 선택합니다. **새로 만들기** 를 선택합니다.
1. 적절한 서버를 선택합니다(로컬 서버가 기본적으로 선택됨). **새로 만들기** 를 선택합니다.
1. **역할** 트리에서 **Web Server(IIS)** 를 확장하고 **Web Server** 를 확장한 다음, **애플리케이션 개발** 을 확장합니다.
1. **WebSocket 프로토콜** 을 선택합니다. **새로 만들기** 를 선택합니다.
1. 추가 기능이 필요 없는 경우 **다음** 을 선택합니다.
1. **설치** 를 선택합니다.
1. 설치가 완료되면 **닫기** 를 선택하여 마법사를 종료합니다.

Windows 8 이상에서 WebSocket 프로토콜을 지원하려면:

> [!NOTE]
> IIS Express를 사용할 때 이러한 단계가 필요하지 않습니다.

1. **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 이동합니다.
1. 다음 노드를 엽니다. **인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**.
1. **WebSocket 프로토콜** 기능을 선택합니다. **확인** 을 선택합니다.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Node.js에서 socket.io를 사용할 때 WebSocket 비활성화

[Node.js](https://nodejs.org/)의 [socket.io](https://socket.io/)에서 WebSocket 지원을 사용하는 경우 *web.config* 또는 *applicationHost.config* 의 `webSocket` 요소를 사용하여 기본 IIS WebSocket 모듈을 비활성화합니다. 이 단계를 수행하지 않으면 IIS WebSocket 모듈이 Node.js 및 앱이 아닌 WebSocket 통신을 처리하려고 시도합니다.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>샘플 앱

이 아티클과 함께 제공되는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/websockets/samples)은 에코 앱입니다. WebSocket 연결을 생성하는 웹 페이지가 제공되며 서버는 수신한 메시지를 클라이언트로 재전송합니다. 샘플 앱은 IIS Express를 사용하여 Visual Studio에서 실행되도록 구성되지 않으므로, [`dotnet run`](/dotnet/core/tools/dotnet-run)을 사용하여 명령 셸에서 앱을 실행하고 브라우저에서 `http://localhost:5000`으로 이동합니다. 웹 페이지에 연결 상태가 표시됩니다.

![WebSocket 연결 전 웹 페이지 초기 상태](websockets/_static/start.png)

**Connect** 를 선택해서 지정한 URL로 WebSocket 요청을 전송합니다. 그리고 테스트 메시지를 입력한 다음 **Send** 를 선택합니다. 테스트를 모두 마쳤으면 **Close Socket** 을 선택하십시오. **Communication Log** 섹션에는 각각의 열기, 전송 및 닫기 동작이 발생한 순서대로 나타납니다.

![WebSocket 연결 및 테스트 메시지를 보내고 받은 후 웹 페이지 최종 상태](websockets/_static/end.png)
