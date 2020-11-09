---
title: gRPC 서비스 버전 관리
author: jamesnk
description: gRPC 서비스의 버전을 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058703"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="16663-103">gRPC 서비스 버전 관리</span><span class="sxs-lookup"><span data-stu-id="16663-103">Versioning gRPC services</span></span>

<span data-ttu-id="16663-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="16663-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="16663-105">앱에 추가되는 새로운 기능을 사용하려면 클라이언트에 제공되는 gRPC 서비스를 경우에 따라 예기치 않거나 획기적인 방식으로 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="16663-106">GRPC 서비스 변경 시:</span><span class="sxs-lookup"><span data-stu-id="16663-106">When gRPC services change:</span></span>

* <span data-ttu-id="16663-107">변경 내용이 클라이언트에 미치는 영향을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="16663-108">변경을 지원하기 위한 버전 관리 전략을 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="16663-109">이전 버전과의 호환성</span><span class="sxs-lookup"><span data-stu-id="16663-109">Backwards compatibility</span></span>

<span data-ttu-id="16663-110">GRPC 프로토콜은 시간이 지남에 따라 변경되는 서비스를 지원하도록 디자인되었습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="16663-111">일반적으로 gRPC 서비스 및 메서드에 대한 추가는 호환성이 손상되지 않는 변경입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="16663-112">호환성이 손상되지 않는 변경 내용은 기존 클라이언트가 변경 없이 계속 작동되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="16663-113">GRPC 서비스를 변경하거나 삭제하는 것은 호환성이 손상되는 변경입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="16663-114">GRPC 서비스에 호환성이 손상되는 변경이 발생하는 경우 해당 서비스를 사용하는 클라이언트를 업데이트한 후 다시 배포해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="16663-115">서비스에 대해 호환성이 손상되지 않는 변경을 수행하면 다음과 같은 여러 가지 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="16663-116">기존 클라이언트는 계속 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="16663-117">클라이언트에 호환성이 손상되지 않는 변경을 알리고 업데이트하는 것과 관련된 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="16663-118">서비스의 한 버전만 문서화하고 유지 관리하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="16663-119">호환성이 손상되지 않는 변경</span><span class="sxs-lookup"><span data-stu-id="16663-119">Non-breaking changes</span></span>

<span data-ttu-id="16663-120">이러한 변경 내용은 gRPC 프로토콜 수준 및 .NET 이진 수준에서 호환성이 손상되지 않는 변경입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="16663-121">**새 서비스 추가**</span><span class="sxs-lookup"><span data-stu-id="16663-121">**Adding a new service**</span></span>
* <span data-ttu-id="16663-122">**서비스에 새 메서드 추가**</span><span class="sxs-lookup"><span data-stu-id="16663-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="16663-123">**요청 메시지에 필드 추가** - 요청 메시지에 추가된 필드는 설정하지 않을 경우 서버의 [기본값](https://developers.google.com/protocol-buffers/docs/proto3#default)으로 deserialize됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="16663-124">호환성이 손상되지 않는 변경을 위해서는 새 필드가 이전 클라이언트에 의해 설정되지 않은 경우 서비스가 성공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="16663-125">**응답 메시지에 필드 추가** - 응답 메시지에 추가된 필드는 클라이언트에 있는 메시지의 [알 수 없는 필드](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) 컬렉션으로 deserialize됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="16663-126">**열거형에 값 추가** - 열거형이 숫자 값으로 serialize됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="16663-127">새 열거형 값은 클라이언트에서 열거형 이름 없는 열거형 값으로 deserialize됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="16663-128">호환성이 손상되지 않는 변경을 위해서는 이전 클라이언트가 새 열거형 값을 수신할 때 올바르게 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="16663-129">이진 호환성이 손상되는 변경</span><span class="sxs-lookup"><span data-stu-id="16663-129">Binary breaking changes</span></span>

<span data-ttu-id="16663-130">다음 변경 내용은 gRPC 프로토콜 수준에서 호환성이 손상되지 않는 변경이지만 최신 *.proto* 계약 또는 클라이언트 .NET 어셈블리로 업그레이드하는 경우 클라이언트를 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="16663-131">gRPC 라이브러리를 NuGet에 게시하려는 경우 이진 호환성이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="16663-132">**필드 제거** - 제거된 필드의 값은 메시지의 [알 수 없는 필드](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)로 deserialize됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="16663-133">이것은 gRPC 로토콜 관련 호환성이 손상되는 변경이 아니며 최신 계약으로 업그레이드하는 경우 클라이언트를 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="16663-134">제거된 필드 번호를 나중에 실수로 다시 사용하지 않는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="16663-135">이 문제가 발생하지 않도록 하려면 Protobuf의 [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) 키워드를 사용하여 메시지에 삭제된 필드 번호 및 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="16663-136">**메시지 이름 바꾸기** - 메시지 이름은 일반적으로 네트워크에서 전송되지 않으므로 gRPC 프로토콜 관련 호환성이 손상되는 변경이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="16663-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="16663-137">최신 계약으로 업그레이드하는 경우에는 클라이언트를 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="16663-138">메시지 이름이 메시지 유형을 식별하는 데 사용되는 경우 메시지 이름이 네트워크에서 전송 **되는** 한 가지 경우는 [모든](https://developers.google.com/protocol-buffers/docs/proto3#any) 필드를 사용하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="16663-139">**csharp_namespace 변경** - `csharp_namespace`를 변경하면 생성된 .NET 형식의 네임스페이스가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="16663-140">이것은 gRPC 프로토콜 관련 호환성이 손상되는 변경이 아니며 최신 계약으로 업그레이드하는 경우 클라이언트를 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="16663-141">프로토콜 관련 호환성이 손상되는 변경</span><span class="sxs-lookup"><span data-stu-id="16663-141">Protocol breaking changes</span></span>

<span data-ttu-id="16663-142">다음 항목은 프로토콜 및 이진 관련 호환성이 손상되는 변경입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="16663-143">**필드 이름 바꾸기** - Protobuf 콘텐츠를 사용하면 필드 이름이 생성된 코드에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="16663-144">필드 번호는 네트워크에서 필드를 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="16663-145">필드 이름 바꾸기는 Protobuf에 대한 프로토콜 관련 호환성이 손상되는 변경이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="16663-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="16663-146">그러나 서버에서 JSON 콘텐츠를 사용하는 경우 필드 이름 바꾸기는 호환성이 손상되는 변경입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="16663-147">**필드 데이터 형식 변경** - 필드의 데이터 형식을 [호환되지 않는 형식](https://developers.google.com/protocol-buffers/docs/proto3#updating)으로 변경하면 메시지를 deserialize할 때 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="16663-148">새 데이터 형식이 호환되는 경우에도 최신 계약으로 업그레이드하는 경우 새 형식을 지원하도록 클라이언트를 업데이트해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="16663-149">**필드 번호 변경** - Protobuf 페이로드를 사용하면 필드 번호가 네트워크에서 필드를 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="16663-150">**패키지, 서비스 또는 메서드 이름 바꾸기** - gRPC는 패키지 이름, 서비스 이름 및 메서드 이름을 사용하여 URL을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="16663-151">클라이언트는 서버에서 *미구현* 상태를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="16663-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="16663-152">**서비스 또는 메서드 제거** - 클라이언트는 제거된 메서드를 호출할 때 서버에서 *미구현* 상태를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="16663-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="16663-153">동작 관련 호환성이 손상되는 변경</span><span class="sxs-lookup"><span data-stu-id="16663-153">Behavior breaking changes</span></span>

<span data-ttu-id="16663-154">호환성이 손상되지 않는 변경을 수행하는 경우 이전 클라이언트가 새 서비스 동작을 계속할 수 있는지 여부도 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="16663-155">예를 들어, 요청 메시지에 새 필드를 추가하는 것은</span><span class="sxs-lookup"><span data-stu-id="16663-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="16663-156">프로토콜 관련 호환성이 손상되는 변경이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="16663-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="16663-157">새 필드가 설정되지 않은 경우 서버에서 오류 상태를 반환하면 이전 클라이언트에 대해 호환성이 손상되는 변경이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="16663-158">동작 호환성은 앱별 코드에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="16663-159">버전 번호 서비스</span><span class="sxs-lookup"><span data-stu-id="16663-159">Version number services</span></span>

<span data-ttu-id="16663-160">서비스는 이전 클라이언트와의 호환성을 유지하려고 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="16663-161">결국 앱을 변경하면 호환성이 손상되는 변경이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="16663-162">이전 클라이언트에 대해 호환성이 손상되는 변경을 수행하고 서비스와 함께 강제로 업데이트하는 것은 좋은 사용자 환경이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="16663-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="16663-163">호환성이 손상되는 변경을 수행하면서 이전 버전과의 호환성을 유지하는 방법은 여러 버전의 서비스를 게시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="16663-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="16663-164">gRPC는 .NET 네임스페이스와 유사하게 작동 하는 선택적 [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) 지정자를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="16663-165">실제로 *.proto* 파일에 `option csharp_namespace`가 설정되지 않은 경우 `package`는 생성된 .NET 형식에 대한 .NET 네임스페이스로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="16663-166">이 패키지를 사용하여 서비스의 버전 번호와 해당 메시지를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="16663-167">서비스 주소를 식별하기 위해 패키지 이름이 서비스 이름과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="16663-168">서비스 주소를 사용하면 여러 버전의 서비스를 함께 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="16663-169">버전이 지정된 서비스의 구현은 *Startup.cs* 에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-169">Implementations of the versioned service are registered in *Startup.cs* :</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="16663-170">패키지 이름에 버전 번호를 포함하면 *v1* 버전을 호출하는 이전 클라이언트를 계속 지원하면서 호환성이 손상되는 변경을 포함하는 서비스의 *v2* 버전을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="16663-171">*v2* 서비스를 사용하도록 클라이언트를 업데이트한 후에는 이전 버전을 제거하도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="16663-172">여러 버전의 서비스를 게시할 예정인 경우:</span><span class="sxs-lookup"><span data-stu-id="16663-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="16663-173">타당한 경우 호환성이 손상되는 변경을 피합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="16663-174">호환성이 손상되는 변경을 수행하지 않는 경우에는 버전 번호를 업데이트하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="16663-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="16663-175">호환성이 손상되는 변경을 수행하는 경우에는 버전 번호를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="16663-176">여러 버전의 서비스를 게시하면 중복됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="16663-177">중복을 줄이려면 서비스 구현의 비즈니스 논리를 이전 및 새 구현에서 다시 사용할 수 있는 중앙 위치로 이동하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="16663-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="16663-178">서로 다른 패키지 이름을 사용하여 생성된 서비스와 메시지는 **다른 .NET 형식** 이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="16663-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="16663-179">비즈니스 논리를 중앙 위치에 이동하려면 메시지를 공통 형식으로 매핑해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="16663-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16663-180">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="16663-180">Additional resources</span></span>

* <xref:grpc/protobuf>
