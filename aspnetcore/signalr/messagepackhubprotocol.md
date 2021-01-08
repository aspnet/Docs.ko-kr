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
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="9941a-103">ASP.NET Core에 대해에서 MessagePack Hub 프로토콜 사용 SignalR</span><span class="sxs-lookup"><span data-stu-id="9941a-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9941a-104">이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="9941a-105">MessagePack 란?</span><span class="sxs-lookup"><span data-stu-id="9941a-105">What is MessagePack?</span></span>

<span data-ttu-id="9941a-106">[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="9941a-107">[JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="9941a-108">바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="9941a-109">SignalR 에서는 MessagePack 형식을 기본적으로 지원 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="9941a-110">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="9941a-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="9941a-111">서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="9941a-112">`Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `AddSignalR` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-113">JSON은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-113">JSON is enabled by default.</span></span> <span data-ttu-id="9941a-114">MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="9941a-115">MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="9941a-116">이 대리자에서 속성은 `SerializerOptions` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="9941a-117">해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="9941a-118">Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="9941a-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="9941a-120">예를 들어를 `.WithSecurity(MessagePackSecurity.UntrustedData)` 바꿀 때를 호출 `SerializerOptions` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="9941a-121">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="9941a-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-122">JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="9941a-123">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="9941a-124">MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="9941a-125">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-125">.NET client</span></span>

<span data-ttu-id="9941a-126">.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="9941a-127">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="9941a-128">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-128">JavaScript client</span></span>

<span data-ttu-id="9941a-129">JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="9941a-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="9941a-130">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="9941a-131">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="9941a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="9941a-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="9941a-133">브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="9941a-134">태그를 사용 `<script>` 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="9941a-135">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-136">요소를 사용 하는 경우 `<script>` 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="9941a-137">*msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="9941a-138">*signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="9941a-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="9941a-140">이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="9941a-141">Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-141">Java client</span></span>

<span data-ttu-id="9941a-142">Java로 MessagePack를 사용 하도록 설정 하려면 `com.microsoft.signalr.messagepack` 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="9941a-143">Gradle를 사용 하는 경우 `dependencies` *Gradle* 파일의 섹션에 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="9941a-144">Maven를 사용 하는 경우 `<dependencies>` *pom.xml* 파일의 요소 내에 다음 줄을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="9941a-145">`withHubProtocol(new MessagePackHubProtocol())`에 대해를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="9941a-146">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="9941a-146">MessagePack quirks</span></span>

<span data-ttu-id="9941a-147">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="9941a-148">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="9941a-149">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="9941a-150">예를 들어 다음 c # 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="9941a-151">JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="9941a-152">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="9941a-153">`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="9941a-154">특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="9941a-155">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="9941a-156">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="9941a-157">MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="9941a-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="9941a-158">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 UTC 형식으로 변환 됩니다 `DateTime.Kind` `DateTimeKind.Local` . 그렇지 않으면 시간을 건드리지 않고 그대로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="9941a-159">값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="9941a-160">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="9941a-161">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="9941a-162">JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 SignalR `timestamp96` MessagePack에서 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="9941a-163">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="9941a-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="9941a-164">값은 `DateTime.MinValue` 이며 `January 1, 0001` 값으로 인코딩해야 합니다 `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="9941a-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="9941a-165">이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="9941a-166">`DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="9941a-167">일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="9941a-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="9941a-168">MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="9941a-169">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="9941a-170">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="9941a-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="9941a-171">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="9941a-172">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="9941a-173">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="9941a-174">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="9941a-175">Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .</span><span class="sxs-lookup"><span data-stu-id="9941a-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="9941a-176">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="9941a-177">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="9941a-178">예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="9941a-179">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="9941a-180">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="9941a-181">Java의 문자 및 문자열</span><span class="sxs-lookup"><span data-stu-id="9941a-181">Chars and Strings in Java</span></span>

<span data-ttu-id="9941a-182">Java 클라이언트에서 `char` 개체는 단일 문자 개체로 serialize 됩니다 `String` .</span><span class="sxs-lookup"><span data-stu-id="9941a-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="9941a-183">이는 c # 및 JavaScript 클라이언트와 달리 개체로 serialize 합니다 `short` .</span><span class="sxs-lookup"><span data-stu-id="9941a-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="9941a-184">MessagePack 사양 자체는 개체에 대 한 동작을 정의 하지 않으므로 `char` 라이브러리 작성자가이를 serialize 하는 방법을 결정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="9941a-185">클라이언트 간의 동작 차이는 구현에 사용한 라이브러리의 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="9941a-186">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="9941a-186">Related resources</span></span>

* [<span data-ttu-id="9941a-187">시작</span><span class="sxs-lookup"><span data-stu-id="9941a-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9941a-188">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9941a-189">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9941a-190">이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="9941a-191">MessagePack 란?</span><span class="sxs-lookup"><span data-stu-id="9941a-191">What is MessagePack?</span></span>

<span data-ttu-id="9941a-192">[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="9941a-193">[JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="9941a-194">바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="9941a-195">SignalR 는 MessagePack 형식에 대 한 기본 제공 지원을 제공 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="9941a-196">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="9941a-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="9941a-197">서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="9941a-198">`Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `AddSignalR` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-199">JSON은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-199">JSON is enabled by default.</span></span> <span data-ttu-id="9941a-200">MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="9941a-201">MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="9941a-202">이 대리자에서 속성은 `FormatterResolvers` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="9941a-203">해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="9941a-204">Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="9941a-205">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="9941a-206">예를 들어 `MessagePackSecurity.Active` 정적 속성을로 설정 `MessagePackSecurity.UntrustedData` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="9941a-207">을 설정 `MessagePackSecurity.Active` 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="9941a-208">`MessagePack`버전 1.9. x 업그레이드를 설치 SignalR 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="9941a-209">`MessagePack` 버전 2.x에는 주요 변경 내용이 도입 되었으며 SignalR 3.1 이전 버전과 호환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="9941a-210">가 `MessagePackSecurity.Active` 로 설정 되어 있지 않으면 `MessagePackSecurity.UntrustedData` 악의적인 클라이언트에서 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="9941a-211">`MessagePackSecurity.Active` `Program.Main` 다음 코드와 같이에서 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="9941a-212">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="9941a-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-213">JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="9941a-214">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="9941a-215">MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="9941a-216">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-216">.NET client</span></span>

<span data-ttu-id="9941a-217">.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="9941a-218">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="9941a-219">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-219">JavaScript client</span></span>

<span data-ttu-id="9941a-220">JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="9941a-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="9941a-221">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="9941a-222">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="9941a-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="9941a-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="9941a-224">브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="9941a-225">태그를 사용 `<script>` 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="9941a-226">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-227">요소를 사용 하는 경우 `<script>` 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="9941a-228">*msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="9941a-229">*signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="9941a-230">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="9941a-231">이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="9941a-232">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="9941a-232">MessagePack quirks</span></span>

<span data-ttu-id="9941a-233">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="9941a-234">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="9941a-235">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="9941a-236">예를 들어 다음 c # 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="9941a-237">JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="9941a-238">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="9941a-239">`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="9941a-240">특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="9941a-241">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="9941a-242">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="9941a-243">MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="9941a-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="9941a-244">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="9941a-245">현지 시간에 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="9941a-246">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="9941a-247">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="9941a-248">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="9941a-249">JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 SignalR `timestamp96` MessagePack에서 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="9941a-250">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="9941a-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="9941a-251">값은 `DateTime.MinValue` 이며 `January 1, 0001` 값으로 인코딩해야 합니다 `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="9941a-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="9941a-252">이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="9941a-253">`DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="9941a-254">일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="9941a-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="9941a-255">MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="9941a-256">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="9941a-257">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="9941a-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="9941a-258">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="9941a-259">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="9941a-260">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="9941a-261">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="9941a-262">Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .</span><span class="sxs-lookup"><span data-stu-id="9941a-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="9941a-263">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="9941a-264">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="9941a-265">예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="9941a-266">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="9941a-267">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="9941a-268">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="9941a-268">Related resources</span></span>

* [<span data-ttu-id="9941a-269">시작</span><span class="sxs-lookup"><span data-stu-id="9941a-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9941a-270">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9941a-271">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9941a-272">이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="9941a-273">MessagePack 란?</span><span class="sxs-lookup"><span data-stu-id="9941a-273">What is MessagePack?</span></span>

<span data-ttu-id="9941a-274">[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="9941a-275">[JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="9941a-276">바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="9941a-277">SignalR 는 MessagePack 형식에 대 한 기본 제공 지원을 제공 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="9941a-278">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="9941a-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="9941a-279">서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="9941a-280">`Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `AddSignalR` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-281">JSON은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-281">JSON is enabled by default.</span></span> <span data-ttu-id="9941a-282">MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="9941a-283">MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="9941a-284">이 대리자에서 속성은 `FormatterResolvers` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="9941a-285">해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="9941a-286">Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="9941a-287">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="9941a-288">예를 들어 `MessagePackSecurity.Active` 정적 속성을로 설정 `MessagePackSecurity.UntrustedData` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="9941a-289">을 설정 `MessagePackSecurity.Active` 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="9941a-290">`MessagePack`버전 1.9. x 업그레이드를 설치 SignalR 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="9941a-291">`MessagePackSecurity.Active`가로 설정 되어 있지 않으면 `MessagePackSecurity.UntrustedData` 악의적인 클라이언트에서 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="9941a-292">`MessagePackSecurity.Active` `Program.Main` 다음 코드와 같이에서 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="9941a-293">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="9941a-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-294">JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="9941a-295">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="9941a-296">MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="9941a-297">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-297">.NET client</span></span>

<span data-ttu-id="9941a-298">.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="9941a-299">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="9941a-300">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-300">JavaScript client</span></span>

<span data-ttu-id="9941a-301">JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="9941a-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="9941a-302">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="9941a-303">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="9941a-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="9941a-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="9941a-305">브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="9941a-306">태그를 사용 `<script>` 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="9941a-307">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="9941a-308">요소를 사용 하는 경우 `<script>` 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="9941a-309">*msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="9941a-310">*signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="9941a-311">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="9941a-312">이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="9941a-313">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="9941a-313">MessagePack quirks</span></span>

<span data-ttu-id="9941a-314">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="9941a-315">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="9941a-316">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="9941a-317">예를 들어 다음 c # 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="9941a-318">JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="9941a-319">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="9941a-320">`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="9941a-321">특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="9941a-322">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="9941a-323">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="9941a-324">MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="9941a-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="9941a-325">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="9941a-326">현지 시간에 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="9941a-327">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="9941a-328">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="9941a-329">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="9941a-330">JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 SignalR `timestamp96` MessagePack에서 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="9941a-331">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="9941a-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="9941a-332">값은 `DateTime.MinValue` 값으로 `January 1, 0001` 인코딩해야 합니다 `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="9941a-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="9941a-333">이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="9941a-334">`DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="9941a-335">일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="9941a-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="9941a-336">MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="9941a-337">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="9941a-338">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="9941a-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="9941a-339">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="9941a-340">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="9941a-341">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="9941a-342">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="9941a-343">Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .</span><span class="sxs-lookup"><span data-stu-id="9941a-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="9941a-344">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="9941a-345">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="9941a-346">예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="9941a-347">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="9941a-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="9941a-348">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9941a-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="9941a-349">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="9941a-349">Related resources</span></span>

* [<span data-ttu-id="9941a-350">시작</span><span class="sxs-lookup"><span data-stu-id="9941a-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="9941a-351">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="9941a-352">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9941a-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
