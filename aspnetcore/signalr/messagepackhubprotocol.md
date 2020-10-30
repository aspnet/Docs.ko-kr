---
title: 'ASP.NET Core에 대해에서 MessagePack Hub 프로토콜 사용 :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core에 MessagePack Hub 프로토콜을 추가 :::no-loc(SignalR)::: 합니다.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058170"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="eadd8-103">ASP.NET Core에 대해에서 MessagePack Hub 프로토콜 사용 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="eadd8-103">Use MessagePack Hub Protocol in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="eadd8-104">이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eadd8-105">MessagePack 란?</span><span class="sxs-lookup"><span data-stu-id="eadd8-105">What is MessagePack?</span></span>

<span data-ttu-id="eadd8-106">[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eadd8-107">[JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eadd8-108">바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eadd8-109">:::no-loc(SignalR)::: 에서는 MessagePack 형식을 기본적으로 지원 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-109">:::no-loc(SignalR)::: has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eadd8-110">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="eadd8-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="eadd8-111">서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 앱에 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eadd8-112">`Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `Add:::no-loc(SignalR):::` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-113">JSON은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-113">JSON is enabled by default.</span></span> <span data-ttu-id="eadd8-114">MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eadd8-115">MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eadd8-116">이 대리자에서 속성은 `SerializerOptions` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eadd8-117">해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eadd8-118">Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="eadd8-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eadd8-120">예를 들어를 `.WithSecurity(MessagePackSecurity.UntrustedData)` 바꿀 때를 호출 `SerializerOptions` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eadd8-121">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="eadd8-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-122">JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eadd8-123">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="eadd8-124">MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eadd8-125">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-125">.NET client</span></span>

<span data-ttu-id="eadd8-126">.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eadd8-127">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eadd8-128">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-128">JavaScript client</span></span>

<span data-ttu-id="eadd8-129">JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="eadd8-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eadd8-130">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="eadd8-131">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eadd8-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eadd8-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="eadd8-133">브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eadd8-134">태그를 사용 `<script>` 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eadd8-135">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-136">요소를 사용 하는 경우 `<script>` 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eadd8-137">*msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eadd8-138">*signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eadd8-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eadd8-140">이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eadd8-141">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="eadd8-141">MessagePack quirks</span></span>

<span data-ttu-id="eadd8-142">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eadd8-143">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eadd8-144">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eadd8-145">예를 들어 다음 c # 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eadd8-146">JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eadd8-147">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eadd8-148">`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eadd8-149">특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eadd8-150">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eadd8-151">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eadd8-152">MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eadd8-153">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 UTC 형식으로 변환 됩니다 `DateTime.Kind` `DateTimeKind.Local` . 그렇지 않으면 시간을 건드리지 않고 그대로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="eadd8-154">값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eadd8-155">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eadd8-156">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eadd8-157">JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 :::no-loc(SignalR)::: `timestamp96` MessagePack에서 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eadd8-158">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="eadd8-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eadd8-159">값은 `DateTime.MinValue` 이며 `January 1, 0001` 값으로 인코딩해야 합니다 `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eadd8-160">이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eadd8-161">`DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eadd8-162">일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eadd8-163">MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eadd8-164">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-164">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eadd8-165">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="eadd8-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eadd8-166">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eadd8-167">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eadd8-168">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eadd8-169">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="eadd8-170">Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eadd8-171">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eadd8-172">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eadd8-173">예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eadd8-174">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eadd8-175">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-175">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eadd8-176">관련 리소스</span><span class="sxs-lookup"><span data-stu-id="eadd8-176">Related resources</span></span>

* [<span data-ttu-id="eadd8-177">시작</span><span class="sxs-lookup"><span data-stu-id="eadd8-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eadd8-178">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eadd8-179">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="eadd8-180">이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eadd8-181">MessagePack 란?</span><span class="sxs-lookup"><span data-stu-id="eadd8-181">What is MessagePack?</span></span>

<span data-ttu-id="eadd8-182">[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eadd8-183">[JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eadd8-184">바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eadd8-185">:::no-loc(SignalR)::: 는 MessagePack 형식에 대 한 기본 제공 지원을 제공 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-185">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eadd8-186">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="eadd8-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="eadd8-187">서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 앱에 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eadd8-188">`Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `Add:::no-loc(SignalR):::` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-189">JSON은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-189">JSON is enabled by default.</span></span> <span data-ttu-id="eadd8-190">MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eadd8-191">MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eadd8-192">이 대리자에서 속성은 `FormatterResolvers` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eadd8-193">해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eadd8-194">Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="eadd8-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eadd8-196">예를 들어 `MessagePackSecurity.Active` 정적 속성을로 설정 `MessagePackSecurity.UntrustedData` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="eadd8-197">을 설정 `MessagePackSecurity.Active` 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="eadd8-198">`MessagePack`버전 1.9. x 업그레이드를 설치 :::no-loc(SignalR)::: 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-198">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="eadd8-199">`MessagePack` 버전 2.x에는 주요 변경 내용이 도입 되었으며 :::no-loc(SignalR)::: 3.1 이전 버전과 호환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with :::no-loc(SignalR)::: versions 3.1 and earlier.</span></span> <span data-ttu-id="eadd8-200">가 `MessagePackSecurity.Active` 로 설정 되어 있지 않으면 `MessagePackSecurity.UntrustedData` 악의적인 클라이언트에서 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="eadd8-201">`MessagePackSecurity.Active` `Program.Main` 다음 코드와 같이에서 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eadd8-202">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="eadd8-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-203">JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eadd8-204">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="eadd8-205">MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eadd8-206">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-206">.NET client</span></span>

<span data-ttu-id="eadd8-207">.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eadd8-208">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eadd8-209">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-209">JavaScript client</span></span>

<span data-ttu-id="eadd8-210">JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="eadd8-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eadd8-211">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="eadd8-212">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eadd8-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eadd8-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="eadd8-214">브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eadd8-215">태그를 사용 `<script>` 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eadd8-216">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-217">요소를 사용 하는 경우 `<script>` 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eadd8-218">*msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eadd8-219">*signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eadd8-220">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eadd8-221">이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eadd8-222">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="eadd8-222">MessagePack quirks</span></span>

<span data-ttu-id="eadd8-223">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eadd8-224">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eadd8-225">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eadd8-226">예를 들어 다음 c # 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eadd8-227">JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eadd8-228">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eadd8-229">`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eadd8-230">특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eadd8-231">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eadd8-232">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eadd8-233">MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eadd8-234">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="eadd8-235">현지 시간에 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eadd8-236">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="eadd8-237">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-237">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eadd8-238">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eadd8-239">JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 :::no-loc(SignalR)::: `timestamp96` MessagePack에서 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eadd8-240">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="eadd8-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eadd8-241">값은 `DateTime.MinValue` 이며 `January 1, 0001` 값으로 인코딩해야 합니다 `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eadd8-242">이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eadd8-243">`DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eadd8-244">일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eadd8-245">MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eadd8-246">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-246">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eadd8-247">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="eadd8-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eadd8-248">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eadd8-249">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eadd8-250">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eadd8-251">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="eadd8-252">Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eadd8-253">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eadd8-254">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eadd8-255">예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eadd8-256">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eadd8-257">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-257">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eadd8-258">관련 리소스</span><span class="sxs-lookup"><span data-stu-id="eadd8-258">Related resources</span></span>

* [<span data-ttu-id="eadd8-259">시작</span><span class="sxs-lookup"><span data-stu-id="eadd8-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eadd8-260">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eadd8-261">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eadd8-262">이 문서에서는 독자가 [시작](xref:tutorials/signalr)에 설명 된 항목에 대해 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eadd8-263">MessagePack 란?</span><span class="sxs-lookup"><span data-stu-id="eadd8-263">What is MessagePack?</span></span>

<span data-ttu-id="eadd8-264">[MessagePack](https://msgpack.org/index.html) 는 빠르고 압축 된 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eadd8-265">[JSON](https://www.json.org/)과 비교 하 여 더 작은 메시지를 만들기 때문에 성능 및 대역폭이 중요 한 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eadd8-266">바이트를 MessagePack 파서를 통해 전달 하지 않는 한, 네트워크 추적 및 로그를 살펴보면 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eadd8-267">:::no-loc(SignalR)::: 는 MessagePack 형식에 대 한 기본 제공 지원을 제공 하며 클라이언트 및 서버에서 사용할 수 있는 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-267">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eadd8-268">서버에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="eadd8-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="eadd8-269">서버에서 MessagePack Hub 프로토콜을 사용 하도록 설정 하려면 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 앱에 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eadd8-270">`Startup.ConfigureServices`메서드에서 `AddMessagePackProtocol` `Add:::no-loc(SignalR):::` 서버에 대 한 MessagePack 지원을 사용 하도록 호출에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-271">JSON은 기본적으로 사용 하도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-271">JSON is enabled by default.</span></span> <span data-ttu-id="eadd8-272">MessagePack를 추가 하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eadd8-273">MessagePack가 데이터의 형식을 지정 하는 방법을 사용자 지정 하려면에서 옵션을 구성 하는 `AddMessagePackProtocol` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eadd8-274">이 대리자에서 속성은 `FormatterResolvers` MessagePack serialization 옵션을 구성 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eadd8-275">해결 프로그램의 작동 방식에 대 한 자세한 내용은 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)에서 MessagePack 라이브러리를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eadd8-276">Serialize 할 개체에 특성을 사용 하 여 해당 특성을 처리 하는 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="eadd8-277">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 을 검토 하 고 권장 되는 패치를 적용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eadd8-278">예를 들어 `MessagePackSecurity.Active` 정적 속성을로 설정 `MessagePackSecurity.UntrustedData` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="eadd8-279">을 설정 `MessagePackSecurity.Active` 하려면 [MessagePack의 1.9 버전](https://www.nuget.org/packages/MessagePack/1.9.3)을 수동으로 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="eadd8-280">`MessagePack`버전 1.9. x 업그레이드를 설치 :::no-loc(SignalR)::: 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-280">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="eadd8-281">`MessagePackSecurity.Active`가로 설정 되어 있지 않으면 `MessagePackSecurity.UntrustedData` 악의적인 클라이언트에서 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="eadd8-282">`MessagePackSecurity.Active` `Program.Main` 다음 코드와 같이에서 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eadd8-283">클라이언트에서 MessagePack 구성</span><span class="sxs-lookup"><span data-stu-id="eadd8-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-284">JSON은 지원 되는 클라이언트에 대해 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eadd8-285">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="eadd8-286">MessagePack 지원을 추가 하면 이전에 구성 된 모든 프로토콜이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eadd8-287">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-287">.NET client</span></span>

<span data-ttu-id="eadd8-288">.NET 클라이언트에서 MessagePack를 사용 하도록 설정 하려면 패키지를 설치 하 `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` 고 `AddMessagePackProtocol` 에서를 호출 `HubConnectionBuilder` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eadd8-289">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성 하기 위한 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eadd8-290">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-290">JavaScript client</span></span>

<span data-ttu-id="eadd8-291">JavaScript 클라이언트에 대 한 MessagePack 지원은 npm 패키지에서 제공 됩니다 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) .</span><span class="sxs-lookup"><span data-stu-id="eadd8-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eadd8-292">명령 셸에서 다음 명령을 실행 하 여 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="eadd8-293">Npm 패키지를 설치한 후 다음 파일을 참조 하 여 JavaScript 모듈 로더를 통해 직접 모듈을 사용 하거나 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eadd8-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eadd8-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="eadd8-295">브라우저에서 `msgpack5` 라이브러리도 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eadd8-296">태그를 사용 `<script>` 하 여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eadd8-297">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js* 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="eadd8-298">요소를 사용 하는 경우 `<script>` 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eadd8-299">*msgpack5.js* 전에 *signalr-protocol-msgpack.js* 참조 된 경우 MessagePack에 연결 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eadd8-300">*signalr-protocol-msgpack.js* 하기 전에 *signalr.js* 도 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eadd8-301">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`에를 추가 하면 `HubConnectionBuilder` 서버에 연결할 때 MessagePack 프로토콜을 사용 하도록 클라이언트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eadd8-302">이번에는 JavaScript 클라이언트에 MessagePack 프로토콜에 대 한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eadd8-303">MessagePack 특수</span><span class="sxs-lookup"><span data-stu-id="eadd8-303">MessagePack quirks</span></span>

<span data-ttu-id="eadd8-304">MessagePack Hub 프로토콜을 사용할 때 알아야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eadd8-305">MessagePack은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eadd8-306">MessagePack 프로토콜은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eadd8-307">예를 들어 다음 c # 클래스를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eadd8-308">JavaScript 클라이언트에서 전송 하는 경우 `PascalCased` 대/소문자 구분이 c # 클래스와 정확 하 게 일치 해야 하므로 속성 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eadd8-309">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eadd8-310">`camelCased`이름 사용은 c # 클래스에 올바르게 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eadd8-311">특성을 사용 하 여 `Key` MessagePack 속성에 다른 이름을 지정 하면이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eadd8-312">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp#object-serialization)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eadd8-313">Serialize/deserialize 할 때에는 DateTime이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eadd8-314">MessagePack 프로토콜은의 값을 인코딩하는 방법을 제공 하지 않습니다 `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eadd8-315">결과적으로, 날짜를 deserialize 할 때 MessagePack Hub 프로토콜은 들어오는 날짜가 UTC 형식인 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="eadd8-316">현지 시간에 값을 사용 하 여 작업 하는 경우에는 `DateTime` 값을 보내기 전에 UTC로 변환 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eadd8-317">받은 UTC에서 현지 시간으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="eadd8-318">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-318">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eadd8-319">MinValue는 JavaScript의 MessagePack에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eadd8-320">JavaScript 클라이언트에서 사용 하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 :::no-loc(SignalR)::: `timestamp96` MessagePack에서 형식을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eadd8-321">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용 됩니다 (이전에는 매우 일찍 또는 나중에 매우 오래 됨).</span><span class="sxs-lookup"><span data-stu-id="eadd8-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eadd8-322">값은 `DateTime.MinValue` 값으로 `January 1, 0001` 인코딩해야 합니다 `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eadd8-323">이로 인해 `DateTime.MinValue` JavaScript 클라이언트로 보내기가 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eadd8-324">`DateTime.MinValue`가 JavaScript 클라이언트에서 수신 되 면 다음 오류가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eadd8-325">일반적으로는 `DateTime.MinValue` "missing" 또는 값을 인코딩하는 데 사용 됩니다 `null` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eadd8-326">MessagePack에서 해당 값을 인코딩해야 하는 경우 null을 허용 하는 `DateTime` 값 ()을 사용 `DateTime?` 하거나 `bool` 날짜가 있는지를 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eadd8-327">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-327">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eadd8-328">"사전" 컴파일 환경에서 MessagePack 지원</span><span class="sxs-lookup"><span data-stu-id="eadd8-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eadd8-329">.NET 클라이언트 및 서버에서 사용 하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용 하 여 serialization을 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eadd8-330">따라서 "미리" 컴파일 (예: Xamarin iOS 또는 Unity)을 사용 하는 환경에서는 기본적으로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eadd8-331">직렬 변환기/역직렬 변환기 코드를 "미리 생성" 하 여 이러한 환경에서 MessagePack를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eadd8-332">자세한 내용은 [MessagePack-CSharp 설명서](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="eadd8-333">Serializer를 미리 생성 한 후에는에 전달 된 구성 대리자를 사용 하 여 해당 serializer를 등록할 수 있습니다 `AddMessagePackProtocol` .</span><span class="sxs-lookup"><span data-stu-id="eadd8-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eadd8-334">MessagePack에서 형식 검사가 더 엄격 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eadd8-335">JSON 허브 프로토콜은 deserialization 중에 형식 변환을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eadd8-336">예를 들어 들어오는 개체의 속성 값이 숫자 ( `{ foo: 42 }` ) 이지만 .net 클래스의 속성이 형식인 경우 `string` 값이 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eadd8-337">그러나 MessagePack는이 변환을 수행 하지 않으며 서버 쪽 로그 (및 콘솔)에서 볼 수 있는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="eadd8-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eadd8-338">이 제한 사항에 대 한 자세한 내용은 GitHub 문제 [aspnet/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eadd8-338">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eadd8-339">관련 리소스</span><span class="sxs-lookup"><span data-stu-id="eadd8-339">Related resources</span></span>

* [<span data-ttu-id="eadd8-340">시작</span><span class="sxs-lookup"><span data-stu-id="eadd8-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eadd8-341">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eadd8-342">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="eadd8-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
