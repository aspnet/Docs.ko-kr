---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core 앱을 구성 하는 방법을 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326589"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a>ASP.NET Core SignalR 구성

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serialization 옵션

ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다. 각 프로토콜에는 serialization 구성 옵션이 있습니다.

JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다. `AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` . `AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` . 해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다. 자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.

예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. `Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Js로 전환

`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.

### <a name="messagepack-serialization-options"></a>MessagePack serialization 옵션

MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다. 자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.

> [!NOTE]
> 이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다. 이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다. 권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .|
| `HandshakeTimeout` | 15초 | 클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다. 변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다. 권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .  |
| `SupportedProtocols` | 설치 된 모든 프로토콜 | 이 허브에서 지원 되는 프로토콜입니다. 기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다. |
| `StreamBufferCapacity` | `10` | 클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다. 이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.|
| `MaximumReceiveMessageSize` | 32KB | 들어오는 단일 허브 메시지의 최대 크기입니다. |
| `MaximumParallelInvocationsPerClient` | 1 | 각 클라이언트가 큐에 대기 하기 전에 병렬로 호출할 수 있는 최대 허브 메서드 수입니다. |

에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다. 이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다. 이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다. |
| `AuthorizationData` | 허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다. | 클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다. 이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `Transports` | 모든 전송을 사용할 수 있습니다. | `HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다. |
| `LongPolling` | 아래 내용을 참조하세요. | 긴 폴링 전송과 관련 된 추가 옵션입니다. |
| `WebSockets` | 아래 내용을 참조하세요. | Websocket 전송과 관련 된 추가 옵션입니다. |
| `MinimumProtocolVersion` | 0 | Negotiate 프로토콜의 최소 버전을 지정 합니다. 클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다. |

긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 초 | 단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다. |

WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .

### <a name="configure-logging"></a>로깅 구성

로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` . 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.

> [!NOTE]
> 로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다. 전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.

예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다. 확장 메서드를 호출 합니다 `AddConsole` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` . `LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다. 로그는 브라우저 콘솔 창에 기록 됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다. 이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

다음 표에서는 사용 가능한 로그 수준을 나열 합니다. 사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다. 이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**또는**`information` | `LogLevel.Information` |
| `warn`**또는**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.

로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.

SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

### <a name="configure-allowed-transports"></a>허용 된 전송 구성

에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` . 값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다. 모든 전송은 기본적으로 사용 하도록 설정 됩니다.

예를 들어 Server-Sent 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.

Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` . Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.

### <a name="configure-bearer-authentication"></a>전달자 인증 구성

요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다. .NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` . JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 Server-Sent 이벤트와 websocket 요청). 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .

.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>제한 시간 및 연결 유지 옵션 구성

시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` . 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `ClientCertificates` | Empty | 인증 요청에 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 전송할 자격 증명입니다. |
| `CloseTimeout` | 5초 | Websocket 전용입니다. 서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다. 이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` . WebSocket 연결에는 사용 되지 않습니다. 이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다. 해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다. **처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다. 옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>연결에 사용할 전송을 지정 하는 값입니다. |
| `headers` | `null` | 모든 HTTP 요청과 함께 전송 되는 헤더의 사전입니다. 브라우저에서 헤더 보내기가 Websocket 또는 스트림에 대해 작동 하지 않습니다 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> . |
| `logMessageContent` | `null` | `true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다. |
| `skipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `withCredentials` | `true` | CORS 요청과 함께 자격 증명을 보낼지 여부를 지정 합니다. Azure App Service는 cookie 고정 세션에 s를 사용 하 고 올바르게 작동 하려면이 옵션을 사용 하도록 설정 해야 합니다. CORS에 대 한 자세한 SignalR 내용은을 참조 하십시오 <xref:signalr/security#cross-origin-resource-sharing> . |

# <a name="java"></a>[Java](#tab/java)

| Java 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 리소스

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serialization 옵션

ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다. 각 프로토콜에는 serialization 구성 옵션이 있습니다.

JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다. `AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` . `AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` . 해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다. 자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.

예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. `Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Js로 전환

`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.

### <a name="messagepack-serialization-options"></a>MessagePack serialization 옵션

MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다. 자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.

> [!NOTE]
> 이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다. 이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다. 권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .|
| `HandshakeTimeout` | 15초 | 클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다. 변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다. 권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .  |
| `SupportedProtocols` | 설치 된 모든 프로토콜 | 이 허브에서 지원 되는 프로토콜입니다. 기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다. |
| `StreamBufferCapacity` | `10` | 클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다. 이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.|
| `MaximumReceiveMessageSize` | 32KB | 들어오는 단일 허브 메시지의 최대 크기입니다. |

에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다. 이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다. 이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다. |
| `AuthorizationData` | 허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다. | 클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다. 이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `Transports` | 모든 전송을 사용할 수 있습니다. | `HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다. |
| `LongPolling` | 아래 내용을 참조하세요. | 긴 폴링 전송과 관련 된 추가 옵션입니다. |
| `WebSockets` | 아래 내용을 참조하세요. | Websocket 전송과 관련 된 추가 옵션입니다. |
| `MinimumProtocolVersion` | 0 | Negotiate 프로토콜의 최소 버전을 지정 합니다. 클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다. |

긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 초 | 단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다. |

WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .

### <a name="configure-logging"></a>로깅 구성

로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` . 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.

> [!NOTE]
> 로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다. 전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.

예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다. 확장 메서드를 호출 합니다 `AddConsole` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` . `LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다. 로그는 브라우저 콘솔 창에 기록 됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다. 이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

다음 표에서는 사용 가능한 로그 수준을 나열 합니다. 사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다. 이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**또는**`information` | `LogLevel.Information` |
| `warn`**또는**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.

로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.

SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

### <a name="configure-allowed-transports"></a>허용 된 전송 구성

에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` . 값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다. 모든 전송은 기본적으로 사용 하도록 설정 됩니다.

예를 들어 Server-Sent 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.

Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` . Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.

### <a name="configure-bearer-authentication"></a>전달자 인증 구성

요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다. .NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` . JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 Server-Sent 이벤트와 websocket 요청). 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .

.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>제한 시간 및 연결 유지 옵션 구성

시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` . 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `ClientCertificates` | Empty | 인증 요청에 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 전송할 자격 증명입니다. |
| `CloseTimeout` | 5초 | Websocket 전용입니다. 서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다. 이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` . WebSocket 연결에는 사용 되지 않습니다. 이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다. 해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다. **처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다. 옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>연결에 사용할 전송을 지정 하는 값입니다. |
| `logMessageContent` | `null` | `true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다. |
| `skipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Java 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 리소스

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serialization 옵션

ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다. 각 프로토콜에는 serialization 구성 옵션이 있습니다.

JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다. `AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` . `AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` . 해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다. 자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.

예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. `Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Js로 전환

`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.

### <a name="messagepack-serialization-options"></a>MessagePack serialization 옵션

MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다. 자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.

> [!NOTE]
> 이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다. 이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다. 권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .|
| `HandshakeTimeout` | 15초 | 클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다. 변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다. 권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .  |
| `SupportedProtocols` | 설치 된 모든 프로토콜 | 이 허브에서 지원 되는 프로토콜입니다. 기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다. |
| `StreamBufferCapacity` | `10` | 클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다. 이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.|
| `MaximumReceiveMessageSize` | 32KB | 들어오는 단일 허브 메시지의 최대 크기입니다. |

에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다. 이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다. 이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다. |
| `AuthorizationData` | 허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다. | 클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다. 이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `Transports` | 모든 전송을 사용할 수 있습니다. | `HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다. |
| `LongPolling` | 아래 내용을 참조하세요. | 긴 폴링 전송과 관련 된 추가 옵션입니다. |
| `WebSockets` | 아래 내용을 참조하세요. | Websocket 전송과 관련 된 추가 옵션입니다. |

긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 초 | 단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다. |

WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .

### <a name="configure-logging"></a>로깅 구성

로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` . 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.

> [!NOTE]
> 로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다. 전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.

예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다. 확장 메서드를 호출 합니다 `AddConsole` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` . `LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다. 로그는 브라우저 콘솔 창에 기록 됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다. 이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

다음 표에서는 사용 가능한 로그 수준을 나열 합니다. 사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다. 이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**또는**`information` | `LogLevel.Information` |
| `warn`**또는**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.

로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.

SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

### <a name="configure-allowed-transports"></a>허용 된 전송 구성

에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` . 값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다. 모든 전송은 기본적으로 사용 하도록 설정 됩니다.

예를 들어 Server-Sent 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.

Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` . Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.

### <a name="configure-bearer-authentication"></a>전달자 인증 구성

요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다. .NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` . JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 Server-Sent 이벤트와 websocket 요청). 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .

.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>제한 시간 및 연결 유지 옵션 구성

시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` . 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `ClientCertificates` | Empty | 인증 요청에 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 전송할 자격 증명입니다. |
| `CloseTimeout` | 5초 | Websocket 전용입니다. 서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다. 이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` . WebSocket 연결에는 사용 되지 않습니다. 이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다. 해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다. **처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다. 옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>연결에 사용할 전송을 지정 하는 값입니다. |
| `logMessageContent` | `null` | `true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다. |
| `skipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Java 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 리소스

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serialization 옵션

ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다. 각 프로토콜에는 serialization 구성 옵션이 있습니다.

[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` . `AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` . 해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다. 자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.
 
예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. `Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.

### <a name="messagepack-serialization-options"></a>MessagePack serialization 옵션

MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다. 자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.

> [!NOTE]
> 이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다. 이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다. 권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .|
| `HandshakeTimeout` | 15초 | 클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다. 변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다. 권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .  |
| `SupportedProtocols` | 설치 된 모든 프로토콜 | 이 허브에서 지원 되는 프로토콜입니다. 기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다. |

에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다. 이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. |
| `AuthorizationData` | 허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다. | 클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다. 이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. |
| `Transports` | 모든 전송을 사용할 수 있습니다. | `HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다. |
| `LongPolling` | 아래 내용을 참조하세요. | 긴 폴링 전송과 관련 된 추가 옵션입니다. |
| `WebSockets` | 아래 내용을 참조하세요. | Websocket 전송과 관련 된 추가 옵션입니다. |

긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 초 | 단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다. |

WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .

### <a name="configure-logging"></a>로깅 구성

로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` . 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.

> [!NOTE]
> 로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다. 전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.

예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다. 확장 메서드를 호출 합니다 `AddConsole` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` . `LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다. 로그는 브라우저 콘솔 창에 기록 됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.

로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.

SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

### <a name="configure-allowed-transports"></a>허용 된 전송 구성

에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` . 값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다. 모든 전송은 기본적으로 사용 하도록 설정 됩니다.

예를 들어 Server-Sent 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.

### <a name="configure-bearer-authentication"></a>전달자 인증 구성

요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다. .NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` . JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 Server-Sent 이벤트와 websocket 요청). 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .

.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>제한 시간 및 연결 유지 옵션 구성

시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` . 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 초 (15000 밀리초) | 클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다. 클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다. 클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `ClientCertificates` | Empty | 인증 요청에 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 전송할 자격 증명입니다. |
| `CloseTimeout` | 5초 | Websocket 전용입니다. 서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다. 이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` . WebSocket 연결에는 사용 되지 않습니다. 이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다. 해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다. **처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다. 옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>연결에 사용할 전송을 지정 하는 값입니다. |
| `logMessageContent` | `null` | `true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다. |
| `skipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Java 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 리소스

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serialization 옵션

ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다. 각 프로토콜에는 serialization 구성 옵션이 있습니다.

[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` . `AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` . 해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다. 자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.
 
예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. `Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.

### <a name="messagepack-serialization-options"></a>MessagePack serialization 옵션

MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다. 자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.

> [!NOTE]
> 이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15초 | 클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |
| `KeepAliveInterval` | 15초 | 서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다. 변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다. 권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .  |
| `SupportedProtocols` | 설치 된 모든 프로토콜 | 이 허브에서 지원 되는 프로토콜입니다. 기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다. 기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다. |

에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다. 이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. |
| `AuthorizationData` | 허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다. | 클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다. 이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다. |
| `Transports` | 모든 전송을 사용할 수 있습니다. | `HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다. |
| `LongPolling` | 아래 내용을 참조하세요. | 긴 폴링 전송과 관련 된 추가 옵션입니다. |
| `WebSockets` | 아래 내용을 참조하세요. | Websocket 전송과 관련 된 추가 옵션입니다. |

긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 초 | 단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다. |

WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .

### <a name="configure-logging"></a>로깅 구성

로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` . 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.

> [!NOTE]
> 로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다. 전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.

예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다. 확장 메서드를 호출 합니다 `AddConsole` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` . `LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다. 로그는 브라우저 콘솔 창에 기록 됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.

로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.

SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

### <a name="configure-allowed-transports"></a>허용 된 전송 구성

에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` . 값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다. 모든 전송은 기본적으로 사용 하도록 설정 됩니다.

예를 들어 Server-Sent 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>전달자 인증 구성

요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다. .NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` . JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 Server-Sent 이벤트와 websocket 요청). 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .

.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>제한 시간 및 연결 유지 옵션 구성

시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다. 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |

.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 초 (3만 밀리초) | 서버 작업에 대 한 시간 제한입니다. 서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` . 이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다. 권장 값은 `KeepAliveInterval` ping이 도착할 때까지 시간을 허용 하는 서버 값을 두 번 이상 표시 하는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대 한 시간 제한입니다. 서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` . 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `ClientCertificates` | Empty | 인증 요청에 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 전송할 자격 증명입니다. |
| `CloseTimeout` | 5초 | Websocket 전용입니다. 서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다. 이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` . WebSocket 연결에는 사용 되지 않습니다. 이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다. 해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다. **처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다. 옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>연결에 사용할 전송을 지정 하는 값입니다. |
| `logMessageContent` | `null` | `true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다. |
| `skipNegotiation` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Java 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 건너뛰려면이를로 설정 `true` 합니다. **Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**. Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR . |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 리소스

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
