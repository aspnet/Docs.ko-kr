---
title: ASP.NET Core에서 허브 사용 SignalR
author: bradygaster
description: ASP.NET Core에서 허브를 사용 하는 방법을 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 872b88cc3c87137365de8c50a37bf5dd5fd9fe10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587868"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>SignalRASP.NET Core 용 허브 사용

만든 사람 [Rachel Appel](https://twitter.com/rachelappel) 및 [Kevin Griffin](https://twitter.com/1kevgriff)

[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ )[(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>SignalR허브 정의

SignalR허브 API를 사용 하면 서버에서 연결 된 클라이언트의 메서드를 호출할 수 있습니다. 서버 코드에서 클라이언트에 의해 호출 되는 메서드를 정의 합니다. 클라이언트 코드에서는 서버에서 호출 되는 메서드를 정의 합니다. SignalR 에서는 실시간 클라이언트와 서버 간 통신을 가능 하 게 하는 백그라운드의 모든 작업을 처리 합니다.

## <a name="configure-signalr-hubs"></a>SignalR허브 구성

미들웨어에는 SignalR 를 호출 하 여 구성 되는 일부 서비스가 필요 합니다 `services.AddSignalR` .

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

SignalRASP.NET Core 앱에 기능을 추가 하는 경우 SignalR 메서드 콜백에서를 호출 하 여 경로를 설정 `endpoint.MapHub` `Startup.Configure` `app.UseEndpoints` 합니다.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

SignalRASP.NET Core 앱에 기능을 추가 하는 경우 SignalR 메서드를 호출 하 여 경로를 설정 `app.UseSignalR` `Startup.Configure` 합니다.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>허브 만들기 및 사용

에서 상속 되는 클래스를 선언 하 `Hub` 고 해당 클래스에 공용 메서드를 추가 하 여 허브를 만듭니다. 클라이언트는로 정의 된 메서드를 호출할 수 있습니다 `public` .

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

모든 c # 메서드에서와 같이 복합 형식 및 배열을 포함 하 여 반환 형식 및 매개 변수를 지정할 수 있습니다. SignalR 매개 변수와 반환 값의 복합 개체와 배열의 serialization 및 deserialization을 처리 합니다.

> [!NOTE]
> 허브는 일시적입니다.
>
> * 허브 클래스의 속성에 상태를 저장 하지 마세요. 모든 허브 메서드 호출은 새 허브 인스턴스에서 실행 됩니다.
> * `await`허브에 종속 된 비동기 메서드를 호출할 때 활성 상태로 유지 하는 경우에 사용 합니다. 예를 들어,를 사용 `Clients.All.SendAsync(...)` 하지 않고를 호출 하 고,가 `await` 완료 되기 전에 허브 메서드를 완료 하는 경우와 같은 메서드가 실패할 수 있습니다 `SendAsync` .

## <a name="the-context-object"></a>Context 개체입니다.

클래스에는 `Hub` `Context` 연결에 대 한 정보를 포함 하는 다음과 같은 속성이 포함 된 속성이 있습니다.

| 속성 | 설명 |
| ------ | ----------- |
| `ConnectionId` | 에서 할당 한 연결의 고유 ID를 가져옵니다 SignalR . 각 연결에 대해 하나의 연결 ID가 있습니다.|
| `UserIdentifier` | [사용자 식별자](xref:signalr/groups)를 가져옵니다. 기본적으로는 SignalR 연결과 관련 된의을 `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 사용자 식별자로 사용 합니다. |
| `User` | `ClaimsPrincipal`현재 사용자와 연결 된를 가져옵니다. |
| `Items` | 이 연결 범위 내에서 데이터를 공유 하는 데 사용할 수 있는 키/값 컬렉션을 가져옵니다. 데이터는이 컬렉션에 저장 될 수 있으며 다른 허브 메서드 호출을 통해 연결에 대해 유지 됩니다. |
| `Features` | 연결에서 사용할 수 있는 기능 컬렉션을 가져옵니다. 지금은이 컬렉션이 대부분의 시나리오에서 필요 하지 않으므로 자세히 설명 하지 않습니다. |
| `ConnectionAborted` | 연결이 중단 되는 경우에 알리는를 가져옵니다 `CancellationToken` . |

`Hub.Context` 또한에는 다음 메서드도 포함 되어 있습니다.

| 메서드 | 설명 |
| ------ | ----------- |
| `GetHttpContext` | 연결 `HttpContext` 에 대 한를 반환 하거나, `null` 연결이 HTTP 요청과 연결 되지 않은 경우을 반환 합니다. HTTP 연결의 경우이 메서드를 사용 하 여 HTTP 헤더 및 쿼리 문자열과 같은 정보를 가져올 수 있습니다. |
| `Abort` | 연결을 중단합니다. |

## <a name="the-clients-object"></a>Clients 개체

클래스에는 `Hub` `Clients` 서버와 클라이언트 간의 통신에 대 한 다음 속성이 포함 된 속성이 있습니다.

| 속성 | 설명 |
| ------ | ----------- |
| `All` | 연결 된 모든 클라이언트에서 메서드를 호출 합니다. |
| `Caller` | 허브 메서드를 호출한 클라이언트에서 메서드를 호출 합니다. |
| `Others` | 메서드를 호출한 클라이언트를 제외한 모든 연결 된 클라이언트에서 메서드를 호출 합니다. |

`Hub.Clients` 또한에는 다음 메서드도 포함 되어 있습니다.

| 메서드 | 설명 |
| ------ | ----------- |
| `AllExcept` | 지정 된 연결을 제외한 모든 연결 된 클라이언트에서 메서드를 호출 합니다. |
| `Client` | 연결 된 특정 클라이언트에서 메서드를 호출 합니다. |
| `Clients` | 연결 된 특정 클라이언트에서 메서드를 호출 합니다. |
| `Group` | 지정 된 그룹의 모든 연결에 대해 메서드를 호출 합니다.  |
| `GroupExcept` | 지정 된 연결을 제외 하 고 지정 된 그룹의 모든 연결에 대해 메서드를 호출 합니다. |
| `Groups` | 여러 연결 그룹에 대해 메서드를 호출 합니다.  |
| `OthersInGroup` | 허브 메서드를 호출한 클라이언트를 제외 하 고 연결 그룹에서 메서드를 호출 합니다.  |
| `User` | 특정 사용자와 연결 된 모든 연결에 대해 메서드를 호출 합니다. |
| `Users` | 지정 된 사용자와 연결 된 모든 연결에 대해 메서드를 호출 합니다. |

위의 표에 있는 각 속성 또는 메서드는 메서드를 사용 하 여 개체를 반환 합니다 `SendAsync` . 메서드를 사용 하 여 `SendAsync` 호출할 클라이언트 메서드의 이름 및 매개 변수를 제공할 수 있습니다.

## <a name="send-messages-to-clients"></a>클라이언트에 메시지 보내기

특정 클라이언트에 대 한 호출을 수행 하려면 개체의 속성을 사용 `Clients` 합니다. 다음 예제에는 세 가지 허브 메서드가 있습니다.

* `SendMessage` 을 사용 하 여 연결 된 모든 클라이언트에 메시지를 보냅니다 `Clients.All` .
* `SendMessageToCaller` 를 사용 하 여 메시지를 다시 호출자에 게 보냅니다 `Clients.Caller` .
* `SendMessageToGroups` 그룹의 모든 클라이언트에 메시지를 보냅니다 `SignalR Users` .

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>강력한 형식의 허브

을 사용 하는 경우의 단점은 `SendAsync` 호출 될 클라이언트 메서드를 지정 하기 위해 매직 문자열을 사용 한다는 것입니다. 그러면 메서드 이름의 철자가 틀렸거나 클라이언트에서 누락 된 경우 코드를 런타임 오류에 열어 둡니다.

를 사용 하는 대신를 사용 하는 `SendAsync` 것이 좋습니다 `Hub` <xref:Microsoft.AspNetCore.SignalR.Hub%601> . 다음 예제에서는 `ChatHub` 클라이언트 메서드가 라는 인터페이스로 추출 되었습니다 `IChatClient` .

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

이 인터페이스를 사용 하 여 앞의 예제를 리팩터링할 수 있습니다 `ChatHub` .

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

를 사용 `Hub<IChatClient>` 하면 클라이언트 메서드를 컴파일할 때 검사할 수 있습니다. 이렇게 하면 매직 문자열을 사용 하 여 발생 하는 문제를 방지할 수 있기 때문에는 `Hub<T>` 인터페이스에 정의 된 메서드에만 액세스할 수 있습니다.

강력한 형식의를 사용 하면을 `Hub<T>` 사용할 수 없습니다 `SendAsync` . 인터페이스에 정의 된 메서드는 여전히 비동기로 정의 될 수 있습니다. 실제로 이러한 각 메서드는를 반환 해야 `Task` 합니다. 인터페이스 이므로 키워드를 사용 하지 마세요 `async` . 예를 들면 다음과 같습니다.

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async`접미사는 메서드 이름에서 제거 되지 않습니다. 클라이언트 메서드를로 정의 하지 않은 경우에 `.on('MyMethodAsync')` 는를 `MyMethodAsync` 이름으로 사용 하지 않아야 합니다.

## <a name="change-the-name-of-a-hub-method"></a>허브 메서드 이름 변경

기본적으로 서버 허브 메서드 이름은 .NET 메서드의 이름입니다. 그러나 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) 특성을 사용 하 여이 기본값을 변경 하 고 메서드의 이름을 수동으로 지정할 수 있습니다. 클라이언트는 메서드를 호출할 때 .NET 메서드 이름 대신이 이름을 사용 해야 합니다.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>연결에 대 한 이벤트 처리

SignalR허브 API는 `OnConnectedAsync` `OnDisconnectedAsync` 연결을 관리 하 고 추적 하는 및 가상 메서드를 제공 합니다. `OnConnectedAsync`클라이언트가 허브에 연결 하는 경우 (예: 그룹에 추가 하는 경우) 작업을 수행 하도록 가상 메서드를 재정의 합니다.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

클라이언트의 `OnDisconnectedAsync` 연결이 끊어질 때 작업을 수행 하도록 가상 메서드를 재정의 합니다. 예를 들어를 호출 하 여 클라이언트가 의도적으로 연결을 끊으면 `connection.stop()` `exception` 매개 변수는이 됩니다 `null` . 그러나 오류 (예: 네트워크 오류)로 인해 클라이언트의 연결이 끊어지면 `exception` 매개 변수에 오류를 설명 하는 예외가 포함 됩니다.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>오류 처리

허브 메서드에서 throw 된 예외는 메서드를 호출한 클라이언트로 전송 됩니다. JavaScript 클라이언트에서 `invoke` 메서드는 [javascript 약속](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)을 반환 합니다. 클라이언트는를 사용 하 여 약속에 연결 된 처리기와 함께 오류 `catch` 를 받으면이를 호출 하 고 JavaScript `Error` 개체로 전달 합니다.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

허브가 예외를 throw 하는 경우 연결이 닫혀 있지 않습니다. 기본적으로는 SignalR 일반 오류 메시지를 클라이언트에 반환 합니다. 예를 들면 다음과 같습니다.

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

예기치 않은 예외에는 데이터베이스 연결에 실패 한 경우 트리거된 예외의 데이터베이스 서버 이름과 같은 중요 한 정보가 포함 되어 있는 경우가 많습니다. SignalR 에서는 이러한 자세한 오류 메시지를 기본적으로 보안 조치로 노출 하지 않습니다. 예외 정보를 표시 하지 않는 이유에 대 한 자세한 내용은 [보안 고려 사항 문서](xref:signalr/security#exceptions) 를 참조 하세요.

클라이언트에 *전파 하려는 예외* 조건이 있는 경우 클래스를 사용할 수 있습니다 `HubException` . 허브 메서드에서을 throw 하는 경우는 `HubException` 수정 SignalR **되지** 않은 상태로 전체 메시지를 클라이언트에 보냅니다.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR 는 `Message` 예외의 속성만 클라이언트에 게 보냅니다. 예외에 대 한 스택 추적 및 기타 속성은 클라이언트에서 사용할 수 없습니다.

## <a name="related-resources"></a>관련 리소스

* [ASP.NET Core 소개 SignalR](xref:signalr/introduction)
* [JavaScript 클라이언트](xref:signalr/javascript-client)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
