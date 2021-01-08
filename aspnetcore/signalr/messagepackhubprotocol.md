---
title: ASP.NET Core에 대해에서 MessagePack Hub 프로토콜 사용 SignalR
author: bradygaster
description: ASP.NET Core에 MessagePack Hub 프로토콜을 추가 SignalR 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024693"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>ASP.NET Core에 대해에서 MessagePack Hub 프로토콜 사용 SignalR

::: moniker range=">= aspnetcore-5.0"

이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.

## <a name="what-is-messagepack"></a>MessagePack 란?

[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다. [JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다. 바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다. SignalR 에서는 MessagePack 형식을 기본적으로 지원 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 MessagePack 구성

서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치 합니다. `Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `AddSignalR` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.

> [!NOTE]
> JSON은 기본적으로 사용 하도록 설정 되어 있습니다. MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다. 이 대리자에서 속성은 `SerializerOptions` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다. 해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요. Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다. 예를 들어를 `.WithSecurity(MessagePackSecurity.UntrustedData)` 바꿀 때를 호출 `SerializerOptions` 합니다.

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 MessagePack 구성

> [!NOTE]
> JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) . 명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다. 태그를 사용 `<script>` 하 여 참조를 만듭니다. 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.

> [!NOTE]
> 요소를 사용 하는 경우 `<script>` 순서가 중요 합니다. *msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다. *signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.

### <a name="java-client"></a>Java 클라이언트

Java로 MessagePack를 사용 하도록 설정 하려면 `com.microsoft.signalr.messagepack` 패키지를 설치 합니다. Gradle를 사용 하는 경우 `dependencies` *Gradle* 파일의 섹션에 다음 줄을 추가 합니다.

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

Maven를 사용 하는 경우 `<dependencies>` *pom.xml* 파일의 요소 내에 다음 줄을 추가 합니다.

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

`withHubProtocol(new MessagePackHubProtocol())`에 대해를 호출 `HubConnectionBuilder` 합니다.

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a>MessagePack 특수

MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>MessagePack은 대/소문자를 구분 합니다.

MessagePack 프로토콜은 대/소문자를 구분 합니다. 예를 들어 다음 c # 클래스를 살펴보세요.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다. 예를 들면 다음과 같습니다.

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다. 특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.

MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` . 결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 UTC 형식으로 변환 됩니다 `DateTime.Kind` `DateTimeKind.Local` . 그렇지 않으면 시간을 건드리지 않고 그대로 전달 됩니다. 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다. 받은 UTC에서 현지 시간으로 변환 합니다.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.

JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 SignalR `timestamp96` MessagePack에서 형식을 지원 하지 않습니다. 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨). 값은 `DateTime.MinValue` 이며 `January 1, 0001` 값으로 인코딩해야 합니다 `timestamp96` . 이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다. `DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` . MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 MessagePack 지원

.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다. 따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다. 직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)를 참조 하세요. Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack에서 형식 검사가 더 엄격 합니다.

JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다. 예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다. 그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.

### <a name="chars-and-strings-in-java"></a>Java의 문자 및 문자열

Java 클라이언트에서 `char` 개체는 단일 문자 개체로 serialize 됩니다 `String` . 이는 c # 및 JavaScript 클라이언트와 달리 개체로 serialize 합니다 `short` . MessagePack 사양 자체는 개체에 대 한 동작을 정의 하지 않으므로 `char` 라이브러리 작성자가이를 serialize 하는 방법을 결정 하는 것이 좋습니다. 클라이언트 간의 동작 차이는 구현에 사용한 라이브러리의 결과입니다.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.

## <a name="what-is-messagepack"></a>MessagePack 란?

[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다. [JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다. 바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다. SignalR 는 MessagePack 형식에 대 한 기본 제공 지원을 제공 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 MessagePack 구성

서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치 합니다. `Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `AddSignalR` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.

> [!NOTE]
> JSON은 기본적으로 사용 하도록 설정 되어 있습니다. MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다. 이 대리자에서 속성은 `FormatterResolvers` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다. 해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요. Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다. 예를 들어 `MessagePackSecurity.Active` 정적 속성을로 설정 `MessagePackSecurity.UntrustedData` 합니다. 을 설정 `MessagePackSecurity.Active` 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다. `MessagePack`버전 1.9. x 업그레이드를 설치 SignalR 하는 데 사용 됩니다. `MessagePack` 버전 2.x에는 주요 변경 내용이 도입 되었으며 SignalR 3.1 이전 버전과 호환 되지 않습니다. 가 `MessagePackSecurity.Active` 로 설정 되어 있지 않으면 `MessagePackSecurity.UntrustedData` 악의적인 클라이언트에서 서비스 거부를 유발할 수 있습니다. `MessagePackSecurity.Active` `Program.Main` 다음 코드와 같이에서 설정 합니다.

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 MessagePack 구성

> [!NOTE]
> JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) . 명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다. 태그를 사용 `<script>` 하 여 참조를 만듭니다. 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.

> [!NOTE]
> 요소를 사용 하는 경우 `<script>` 순서가 중요 합니다. *msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다. *signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.

## <a name="messagepack-quirks"></a>MessagePack 특수

MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>MessagePack은 대/소문자를 구분 합니다.

MessagePack 프로토콜은 대/소문자를 구분 합니다. 예를 들어 다음 c # 클래스를 살펴보세요.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다. 예를 들면 다음과 같습니다.

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다. 특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.

MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` . 결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다. 현지 시간에 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다. 받은 UTC에서 현지 시간으로 변환 합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)를 참조 하세요.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.

JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 SignalR `timestamp96` MessagePack에서 형식을 지원 하지 않습니다. 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨). 값은 `DateTime.MinValue` 이며 `January 1, 0001` 값으로 인코딩해야 합니다 `timestamp96` . 이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다. `DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` . MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 MessagePack 지원

.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다. 따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다. 직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)를 참조 하세요. Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack에서 형식 검사가 더 엄격 합니다.

JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다. 예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다. 그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.

## <a name="what-is-messagepack"></a>MessagePack 란?

[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다. [JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다. 바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다. SignalR 는 MessagePack 형식에 대 한 기본 제공 지원을 제공 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 MessagePack 구성

서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치 합니다. `Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `AddSignalR` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.

> [!NOTE]
> JSON은 기본적으로 사용 하도록 설정 되어 있습니다. MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다. 이 대리자에서 속성은 `FormatterResolvers` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다. 해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요. Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다. 예를 들어 `MessagePackSecurity.Active` 정적 속성을로 설정 `MessagePackSecurity.UntrustedData` 합니다. 을 설정 `MessagePackSecurity.Active` 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다. `MessagePack`버전 1.9. x 업그레이드를 설치 SignalR 하는 데 사용 됩니다. `MessagePackSecurity.Active`가로 설정 되어 있지 않으면 `MessagePackSecurity.UntrustedData` 악의적인 클라이언트에서 서비스 거부를 유발할 수 있습니다. `MessagePackSecurity.Active` `Program.Main` 다음 코드와 같이에서 설정 합니다.

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 MessagePack 구성

> [!NOTE]
> JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) . 명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다. 태그를 사용 `<script>` 하 여 참조를 만듭니다. 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.

> [!NOTE]
> 요소를 사용 하는 경우 `<script>` 순서가 중요 합니다. *msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다. *signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.

## <a name="messagepack-quirks"></a>MessagePack 특수

MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>MessagePack은 대/소문자를 구분 합니다.

MessagePack 프로토콜은 대/소문자를 구분 합니다. 예를 들어 다음 c # 클래스를 살펴보세요.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다. 예를 들면 다음과 같습니다.

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다. 특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.

MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` . 결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다. 현지 시간에 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다. 받은 UTC에서 현지 시간으로 변환 합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)를 참조 하세요.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.

JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 SignalR `timestamp96` MessagePack에서 형식을 지원 하지 않습니다. 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨). 값은 `DateTime.MinValue` 값으로 `January 1, 0001` 인코딩해야 합니다 `timestamp96` . 이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다. `DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` . MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 MessagePack 지원

.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다. 따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다. 직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)를 참조 하세요. Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack에서 형식 검사가 더 엄격 합니다.

JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다. 예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다. 그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)

::: moniker-end
