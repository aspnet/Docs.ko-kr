---
title: SignalRJavaScript 클라이언트 ASP.NET Core
author: bradygaster
description: SignalRJavaScript 클라이언트 ASP.NET Core 개요
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 1b5c89c1beaf36912766b3e08b9f16c4d33945f8
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588063"
---
# <a name="aspnet-core-signalr-javascript-client"></a>SignalRJavaScript 클라이언트 ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

작성자: [Rachel Appel](https://twitter.com/rachelappel)

개발자는 ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/javascript-client/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>클라이언트 패키지를 설치 합니다. SignalR

SignalRJavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다. 다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.

### <a name="install-with-npm"></a>Npm를 사용 하 여 설치

Visual Studio의 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다. Visual Studio Code의 경우 **통합 터미널** 에서 다음 명령을 실행 합니다.

```bash
npm init -y
npm install @microsoft/signalr
```

npm 패키지 콘텐츠를 *node_modules \\ @microsoft\signalr\dist\browser* 폴더에 설치 합니다. *Wwwroot \\ lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다. *signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.

SignalR요소에서 JavaScript 클라이언트를 참조 `<script>` 합니다. 예를 들면 다음과 같습니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>CDN (Content Delivery Network) 사용

Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다. 예를 들면 다음과 같습니다.

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a>을 사용 하 여 설치

라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index) 예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다. 이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.

## <a name="connect-to-a-hub"></a>허브에 연결

다음 코드에서는 연결을 만들고 시작 합니다. 허브 이름은 대/소문자를 구분 하지 않습니다.

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a>크로스-원본 연결

일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다. 그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.

> [!IMPORTANT]
> 클라이언트 코드는 상대 URL이 아닌 절대 URL을 사용 해야 합니다. `.withUrl("/chathub")`을 `.withUrl("https://myappurl/chathub")`으로 변경합니다.

악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다. 원본 간 요청을 허용 하려면 클래스에서 사용 하도록 설정 합니다 `Startup` .

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a>클라이언트에서 허브 메서드 호출

JavaScript 클라이언트는 [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)의 [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) 메서드를 통해 허브에서 공용 메서드를 호출 합니다. 이 `invoke` 메서드는 다음을 허용 합니다.

* 허브 메서드의 이름입니다.
* 허브 메서드에 정의 된 모든 인수입니다.

다음 예제에서 허브의 메서드 이름은 `SendMessage` 입니다. 허브 메서드의 및 인수에 매핑하기 위해 전달 되는 두 번째 및 세 번째 인수는 `invoke` `user` `message` 다음과 같습니다.

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> 클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다. 자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.

`invoke`메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다. 는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다. 서버에서 메서드가 오류를 throw 하는 경우 `Promise` 오류 메시지와 함께이 거부 됩니다. `async`및 `await` 또는 `Promise` 의 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우를 처리 합니다.

JavaScript 클라이언트는의 [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 메서드를 통해 허브에서 공용 메서드를 호출할 수도 있습니다 `HubConnection` . 메서드와 달리 `invoke` `send` 메서드는 서버의 응답을 기다리지 않습니다. `send`메서드는 JavaScript를 반환 합니다 `Promise` . `Promise`메시지가 서버에 전송 되 면이 확인 됩니다. 메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다. `async`및 `await` 또는 `Promise` 의 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우를 처리 합니다.

> [!NOTE]
> 를 사용 하면 `send` 서버에서 메시지를 받을 때까지 기다리지 않습니다. 따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.

## <a name="call-client-methods-from-the-hub"></a>허브에서 클라이언트 메서드 호출

허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 메서드를 사용 하 여 메서드를 정의 합니다 `HubConnection` .

* JavaScript 클라이언트 메서드의 이름입니다.
* 허브가 메서드에 전달 하는 인수입니다.

다음 예제에서 메서드 이름은 `ReceiveMessage` 입니다. 인수 이름은 및입니다 `user` `message` .

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

에서 위의 코드는 `connection.on` 메서드를 사용 하 여 서버 쪽 코드가 호출 하는 경우 실행 됩니다 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

SignalR 및에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 `SendAsync` 합니다 `connection.on` .

> [!NOTE]
> 모범 사례에 따라 이후에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다 `HubConnection` `on` . 이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

및 `try` `catch` `async` `await` 또는의 메서드를 사용 `Promise` `catch` 하 여 클라이언트 쪽 오류를 처리 합니다. `console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다. 메시지는 지정 된 로그 수준 이상으로 기록 됩니다. 사용 가능한 로그 수준은 다음과 같습니다.

* `signalR.LogLevel.Error`: 오류 메시지입니다. `Error`메시지만 로깅합니다.
* `signalR.LogLevel.Warning`: 잠재적 오류에 대 한 경고 메시지입니다. 로그 `Warning` 및 `Error` 메시지.
* `signalR.LogLevel.Information`: 오류가 없는 상태 메시지입니다. `Information`, `Warning` 및 메시지를 기록 `Error` 합니다.
* `signalR.LogLevel.Trace`: 추적 메시지입니다. 허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.

[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다. 메시지는 브라우저 콘솔에 기록 됩니다.

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a>클라이언트 다시 연결

### <a name="automatically-reconnect"></a>자동으로 다시 연결

SignalR `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)에서 메서드를 사용 하 여 자동으로 다시 연결 되도록에 대 한 JavaScript 클라이언트를 구성할 수 있습니다. 기본적으로 자동으로 다시 연결 되지 않습니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

매개 변수를 사용 하지 않으면는 `withAutomaticReconnect()` 각 다시 연결 시도를 시도 하기 전에 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고, 실패 한 네 번 시도 하면 중지 합니다.

다시 연결 시도를 시작 하기 전에는 상태로 `HubConnection` 전환 되 고 `HubConnectionState.Reconnecting` `onreconnecting` `Disconnected` `onclose` `HubConnection` 자동 다시 연결이 구성 되지 않은 등의 콜백 트리거를 상태로 전환 하는 대신 해당 콜백을 발생 시킵니다. 이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

클라이언트에서 처음 네 번의 시도 내에 다시 연결 하는 경우 `HubConnection` 이 상태로 다시 전환 되 `Connected` 고 `onreconnected` 콜백이 발생 합니다. 이렇게 하면 연결이 다시 설정 되었음을 사용자에 게 알릴 수 있습니다.

연결은 서버에서 완전히 새로운 `connectionId` 것 이므로 콜백에 새가 제공 됩니다 `onreconnected` .

> [!WARNING]
> 가 `onreconnected` `connectionId` `HubConnection` [협상을 건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 콜백의 매개 변수는 정의 되지 않습니다.

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()``HubConnection`초기 시작 오류를 다시 시도 하도록를 구성 하지 않으므로 시작 실패를 수동으로 처리 해야 합니다.

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

클라이언트가 처음 네 번의 시도 내에서 성공적으로 다시 연결 되지 않으면 `HubConnection` 이 상태로 전환 되 `Disconnected` 고 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백이 발생 합니다. 이를 통해 사용자에 게 연결이 영구적으로 손실 되었음을 알리고 페이지 새로 고침을 권장 하는 기회를 제공 합니다.

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자가 다시 연결 시도 횟수를 구성 하기 위해는 `withAutomaticReconnect` 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

앞의 예제에서는 `HubConnection` 연결이 끊긴 후 즉시 다시 연결 시도를 시작 하도록를 구성 합니다. 기본 구성의 경우에도 마찬가지입니다.

첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.

두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.

그런 다음 기본 구성에서와 같이 30 초 후에 다시 연결을 다시 시도 하는 대신 세 번째 다시 연결 시도 실패 후 중지 하 여 기본 동작에서 사용자 지정 동작이 다시 달라 지므로.

자동 다시 연결 시도의 타이밍과 수를 더 많이 제어 하려는 경우는 `withAutomaticReconnect` 라는 단일 메서드가 있는 인터페이스를 구현 하는 개체를 허용 `IRetryPolicy` `nextRetryDelayInMilliseconds` 합니다.

`nextRetryDelayInMilliseconds` 는 형식의 단일 인수를 사용 `RetryContext` 합니다. 에는 `RetryContext` `previousRetryCount` , `elapsedMilliseconds` 및 `retryReason` `number` `number` `Error` 각각, 및 인 세 가지 속성이 있습니다. 첫 번째 다시 연결을 시도 하기 전에와는 모두 0이 되며 `previousRetryCount` `elapsedMilliseconds` 은 `retryReason` 연결이 끊어지는 원인이 되는 오류입니다. 실패 한 각 재시도 후에는 `previousRetryCount` 1 씩 증가 하 `elapsedMilliseconds` 고, 지금까지 밀리초 단위로 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 되며,는 `retryReason` 마지막 다시 연결 시도가 실패 한 원인이 되는 오류입니다.

`nextRetryDelayInMilliseconds` 는 다음 다시 연결 시도 전에 대기 하는 시간 (밀리초)을 나타내는 숫자를 반환 해야 합니다. 그렇지 않으면에서 다시 연결을 중지 해야 합니다 `null` `HubConnection` .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.

### <a name="manually-reconnect"></a>수동으로 다시 연결

다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.

1. 함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.
1. `start`연결의 이벤트 처리기에서 함수를 호출 합니다 `onclose` .

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.

## <a name="additional-resources"></a>추가 리소스

* [JavaScript API 참조](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [JavaScript 자습서](xref:tutorials/signalr)
* [WebPack 및 TypeScript 자습서](xref:tutorials/signalr-typescript-webpack)
* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [원본 간 요청 (CORS)](xref:security/cors)
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [연결 오류 문제 해결](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rachel Appel](https://twitter.com/rachelappel)

개발자는 ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/javascript-client/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>클라이언트 패키지를 설치 합니다. SignalR

SignalRJavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다. 다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.

### <a name="install-with-npm"></a>Npm를 사용 하 여 설치

Visual Studio를 사용 하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다. Visual Studio Code의 경우 **통합 터미널** 에서 다음 명령을 실행 합니다.

```bash
npm init -y
npm install @aspnet/signalr
```

npm 패키지 콘텐츠를 *node_modules \\ @aspnet\signalr\dist\browser* 폴더에 설치 합니다. *Wwwroot \\ lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다. *signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.

SignalR요소에서 JavaScript 클라이언트를 참조 `<script>` 합니다. 예를 들면 다음과 같습니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>CDN (Content Delivery Network) 사용

Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다. 예를 들면 다음과 같습니다.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a>을 사용 하 여 설치

라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index) 예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다. 이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.

## <a name="connect-to-a-hub"></a>허브에 연결

다음 코드에서는 연결을 만들고 시작 합니다. 허브 이름은 대/소문자를 구분 하지 않습니다.

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a>크로스-원본 연결

일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다. 그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.

악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다. 원본 간 요청을 허용 하려면 클래스에서 해당 요청을 사용 하도록 설정 `Startup` 합니다.

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출

JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출 합니다. `invoke`메서드는 두 개의 인수를 허용 합니다.

* 허브 메서드의 이름입니다. 다음 예제에서 허브의 메서드 이름은 `SendMessage` 입니다.
* 허브 메서드에 정의 된 모든 인수입니다. 다음 예에서는 인수 이름이 `message` 입니다. 예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원 되는 화살표 함수 구문을 사용 합니다.

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> 클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다. 자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.

`invoke`메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다. 는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다. 서버에서 메서드가 오류를 throw 하는 경우 `Promise` 오류 메시지와 함께이 거부 됩니다. `then` `catch` `Promise` 이러한 경우 또는 구문을 처리 하려면 자체에서 및 메서드를 사용 `await` 합니다.

`send`메서드는 JavaScript를 반환 합니다 `Promise` . `Promise`메시지가 서버에 전송 되 면이 확인 됩니다. 메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다. `then` `catch` `Promise` 이러한 경우 또는 구문을 처리 하려면 자체에서 및 메서드를 사용 `await` 합니다.

> [!NOTE]
> 를 사용 하면 `send` 서버에서 메시지를 받을 때까지 기다리지 않습니다. 따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출

허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용 하 여 메서드를 정의 합니다 `HubConnection` .

* JavaScript 클라이언트 메서드의 이름입니다. 다음 예제에서 메서드 이름은 `ReceiveMessage` 입니다.
* 허브가 메서드에 전달 하는 인수입니다. 다음 예제에서 인수 값은 `message` 입니다.

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

에서 위의 코드는 `connection.on` 서버 쪽 코드가 메서드를 사용 하 여 호출 하는 경우 실행 됩니다 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

SignalR 및에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 `SendAsync` 합니다 `connection.on` .

> [!NOTE]
> 모범 사례에 따라 이후에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다 `HubConnection` `on` . 이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅

메서드를 `catch` 메서드의 끝에 연결 하 여 `start` 클라이언트 쪽 오류를 처리 합니다. `console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다. 메시지는 지정 된 로그 수준 이상으로 기록 됩니다. 사용 가능한 로그 수준은 다음과 같습니다.

* `signalR.LogLevel.Error`: 오류 메시지입니다. `Error`메시지만 로깅합니다.
* `signalR.LogLevel.Warning`: 잠재적 오류에 대 한 경고 메시지입니다. 로그 `Warning` 및 `Error` 메시지.
* `signalR.LogLevel.Information`: 오류가 없는 상태 메시지입니다. `Information`, `Warning` 및 메시지를 기록 `Error` 합니다.
* `signalR.LogLevel.Trace`: 추적 메시지입니다. 허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.

[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다. 메시지는 브라우저 콘솔에 기록 됩니다.

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>클라이언트 다시 연결

### <a name="manually-reconnect"></a>수동으로 다시 연결

> [!WARNING]
> 3.0 이전 버전의에 대 한 JavaScript 클라이언트는 SignalR 자동으로 다시 연결 되지 않습니다. 클라이언트를 수동으로 다시 연결 하는 코드를 작성 해야 합니다.

다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.

1. 함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.
1. `start`연결의 이벤트 처리기에서 함수를 호출 합니다 `onclose` .

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.

## <a name="additional-resources"></a>추가 리소스

* [JavaScript API 참조](/javascript/api/?view=signalr-js-latest)
* [JavaScript 자습서](xref:tutorials/signalr)
* [WebPack 및 TypeScript 자습서](xref:tutorials/signalr-typescript-webpack)
* [허브](xref:signalr/hubs)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [원본 간 요청 (CORS)](xref:security/cors)
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
