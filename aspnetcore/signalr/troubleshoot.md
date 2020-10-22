---
title: ASP.NET Core SignalR 연결 문제 해결
author: bradygaster
description: SignalR연결 문제 해결을 ASP.NET Core 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: 2ffe8bcf4bef5dcb9fc74513c7507e5cb3a6b7cb
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379566"
---
# <a name="troubleshoot-connection-errors"></a>연결 오류 문제 해결

이 섹션에서는 ASP.NET Core 허브에 대 한 연결을 설정 하려고 할 때 발생할 수 있는 오류에 대 한 도움말을 제공 합니다 SignalR .

### <a name="response-code-404"></a>응답 코드 404

Websocket을 사용 하는 경우 `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* 고정 세션이 없는 여러 서버를 사용 하는 경우 한 서버에서 연결을 시작한 후 다른 서버로 전환할 수 있습니다. 다른 서버는 이전 연결을 인식 하지 못합니다.
* 클라이언트가 올바른 끝점에 연결 하 고 있는지 확인 합니다. 예를 들어 서버가에서 호스트 되 `http://127.0.0.1:5000/hub/myHub` 고 클라이언트가에 연결 하려고 `http://127.0.0.1:5000/myHub` 합니다.
* 연결에서 ID를 사용 하 고 협상 후 서버에 요청을 보내는 데 시간이 너무 오래 걸리는 경우 서버는 다음과 같습니다.

  * ID를 삭제 합니다.
  * 404을 반환 합니다.

### <a name="response-code-400-or-503"></a>응답 코드 400 또는 503

다음 오류의 경우:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

이 오류는 대개 Websocket 전송만 사용 하는 클라이언트에 의해 발생 하지만 서버에서 Websocket 프로토콜이 사용 하도록 설정 되어 있지 않습니다.

### <a name="response-code-307"></a>응답 코드 307

Websocket을 사용 하는 경우 `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

이 오류는 negotiate 요청 중에도 발생할 수 있습니다.

일반적인 원인:
* 앱은에서를 호출 하 여 HTTPS를 적용 `UseHttpsRedirection` `Startup` 하거나 URL 재작성 규칙을 통해 https를 적용 하도록 구성 됩니다.

가능한 해결 방법:
* 클라이언트 쪽의 URL을 "http"에서 "https"로 변경 합니다. `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>응답 코드 405

Http 상태 코드 405-메서드를 사용할 수 없습니다.

* 앱에서 [CORS](xref:signalr/security#cross-origin-resource-sharing) 를 사용 하도록 설정 하지 않았습니다.

### <a name="response-code-0"></a>응답 코드 0

Http 상태 코드 0-일반적으로 [CORS](xref:signalr/security#cross-origin-resource-sharing) 문제 이며 상태 코드는 제공 되지 않습니다.

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* 예상 원본을에 추가 합니다. `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* `.AllowCredentials()`CORS 정책에를 추가 합니다. `.AllowAnyOrigin()` `.WithOrigins("*")` 이 옵션과 함께 또는를 사용할 수 없습니다.

### <a name="response-code-413"></a>응답 코드 413

Http 상태 코드 413-페이로드가 너무 큼

이는 종종 4k를 초과 하는 액세스 토큰이 있는 경우에 발생 합니다.

* Azure 서비스를 사용 하는 경우 다음을 사용 하 여 SignalR 서비스를 통해 전송 되는 클레임을 사용자 지정 하 여 토큰 크기를 줄입니다.
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>일시적인 네트워크 오류

일시적인 네트워크 오류로 인해 연결이 닫힐 수 있습니다 SignalR . 서버는 닫힌 연결을 정상적인 클라이언트 연결 끊기로 해석할 수 있습니다. 이러한 경우 클라이언트에서 연결을 끊은 이유에 대 한 자세한 정보를 보려면 [클라이언트 및 서버에서 로그를 수집](xref:signalr/diagnostics)합니다.