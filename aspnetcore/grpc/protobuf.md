---
title: .NET 앱에 대한 Protobuf 메시지 만들기
author: jamesnk
description: .NET 앱에 대한 Protobuf 메시지를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: b70a5ee00405eecfce900b86dc631a54682dce1a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058898"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="3be1a-103">.NET 앱에 대한 Protobuf 메시지 만들기</span><span class="sxs-lookup"><span data-stu-id="3be1a-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="3be1a-104">작성자: [James Newton-King](https://twitter.com/jamesnk) 및 [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="3be1a-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="3be1a-105">gRPC는 IDL(Interface Design Language)로 [Protobuf](https://developers.google.com/protocol-buffers)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="3be1a-106">Protobuf IDL은 gRPC 서비스에서 보내고 받는 메시지를 지정하기 위한 언어 중립적인 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="3be1a-107">Protobuf 메시지는 `.proto` 파일에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="3be1a-108">이 문서에서는 Protobuf 개념이 .NET에 어떻게 매핑되는지 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="3be1a-109">Protobuf 메시지</span><span class="sxs-lookup"><span data-stu-id="3be1a-109">Protobuf messages</span></span>

<span data-ttu-id="3be1a-110">메시지는 Protobuf의 기본 데이터 전송 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="3be1a-111">개념상 .NET 클래스와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="3be1a-112">위의 메시지 정의는 세 필드를 이름-값 쌍으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="3be1a-113">.NET 형식의 속성과 마찬가지로 각 필드에는 이름과 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="3be1a-114">필드 형식은 [Protobuf 스칼라 값 형식](#scalar-value-types)(예: `int32` 또는 다른 메시지)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="3be1a-115">메시지 정의의 각 필드에는 이름 외에도 고유한 번호가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="3be1a-116">필드 번호는 메시지가 Protobuf로 직렬화될 때 필드를 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="3be1a-117">작은 수를 직렬화하는 것은 전체 필드 이름을 직렬화하는 것보다 더 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="3be1a-118">필드 번호는 필드를 식별하기 때문에 필드를 변경할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="3be1a-119">Protobuf 메시지 변경에 대한 자세한 내용은 <xref:grpc/versioning>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3be1a-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="3be1a-120">앱을 빌드할 때 Protobuf 도구는 `.proto` 파일에서 .NET 형식을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="3be1a-121">`Person` 메시지는 .NET 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="3be1a-122">Protobuf 메시지에 대한 자세한 내용은 [Protobuf 언어 가이드](https://developers.google.com/protocol-buffers/docs/proto3#simple)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3be1a-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="3be1a-123">스칼라 값 형식</span><span class="sxs-lookup"><span data-stu-id="3be1a-123">Scalar Value Types</span></span>

<span data-ttu-id="3be1a-124">Protobuf는 네이티브 스칼라 값 형식의 범위를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="3be1a-125">모든 값 형식 및 이와 동등한 C# 형식이 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="3be1a-126">Protobuf 형식</span><span class="sxs-lookup"><span data-stu-id="3be1a-126">Protobuf type</span></span> | <span data-ttu-id="3be1a-127">C# 형식</span><span class="sxs-lookup"><span data-stu-id="3be1a-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

<span data-ttu-id="3be1a-128">스칼라 값에는 항상 기본값이 있으며 `null`로 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-128">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="3be1a-129">이 제약 조건에는 C# 클래스인 `string`과 `ByteString`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-129">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="3be1a-130">`string` 기본값은 빈 문자열 값이며 `ByteString` 기본값은 빈 바이트 값입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-130">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="3be1a-131">기본값을 `null`로 설정하려고 하면 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-131">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="3be1a-132">[null 허용 래퍼 형식](#nullable-types)을 사용하여 null 값을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-132">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="3be1a-133">날짜 및 시간</span><span class="sxs-lookup"><span data-stu-id="3be1a-133">Dates and times</span></span>

<span data-ttu-id="3be1a-134">네이티브 스칼라 형식은 .NET의 <xref:System.DateTimeOffset>, <xref:System.DateTime>, <xref:System.TimeSpan>에 해당하는 날짜 및 시간 값을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-134">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="3be1a-135">해당 형식은 Protobuf의 ‘잘 알려진 형식’ 확장 중 일부를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-135">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="3be1a-136">이 확장은 지원되는 플랫폼에서 복합 필드 형식을 위한 코드 생성과 런타임을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-136">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="3be1a-137">다음 표에는 날짜 및 시간 형식이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-137">The following table shows the date and time types:</span></span>

| <span data-ttu-id="3be1a-138">.NET 형식</span><span class="sxs-lookup"><span data-stu-id="3be1a-138">.NET type</span></span>        | <span data-ttu-id="3be1a-139">Protobuf 잘 알려진 형식</span><span class="sxs-lookup"><span data-stu-id="3be1a-139">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="3be1a-140">C# 클래스에서 생성되는 속성은 .NET 날짜 및 시간 형식이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-140">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="3be1a-141">해당 속성은 `Google.Protobuf.WellKnownTypes` 네임스페이스의 `Timestamp` 클래스와 `Duration` 클래스를 사용하며,</span><span class="sxs-lookup"><span data-stu-id="3be1a-141">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="3be1a-142">이 클래스는 `DateTimeOffset`, `DateTime`, `TimeSpan`으로/에서 변환하는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-142">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="3be1a-143">`Timestamp` 형식은 UTC 시간으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-143">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="3be1a-144">`DateTimeOffset` 값의 오프셋은 항상 0이며 `DateTime.Kind` 속성은 항상 `DateTimeKind.Utc`입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-144">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="3be1a-145">Nullable 유형</span><span class="sxs-lookup"><span data-stu-id="3be1a-145">Nullable types</span></span>

<span data-ttu-id="3be1a-146">C#에 해당하는 Protobuf 코드를 생성하는 데에는 네이티브 형식을 사용합니다(예: `int32`에 대해 `int` 사용).</span><span class="sxs-lookup"><span data-stu-id="3be1a-146">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="3be1a-147">따라서 값이 항상 포함되며 `null`일 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-147">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="3be1a-148">C# 코드에 `int?`가 사용되는 것과 같이 명시적인 `null`이 필요한 값의 경우 Protobuf의 잘 알려진 형식은 null 허용 C# 형식으로 컴파일되는 래퍼를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-148">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="3be1a-149">이 래퍼를 사용하려면 다음 코드와 같이 `.proto` 파일에 `wrappers.proto`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-149">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="3be1a-150">`wrappers.proto` 형식은 생성된 속성에 노출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-150">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="3be1a-151">Protobuf는 C# 메시지에서 적절한 .NET null 허용 형식에 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-151">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="3be1a-152">예를 들어 `google.protobuf.Int32Value` 필드는 `int?` 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-152">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="3be1a-153">`string` 및 `ByteString` 같은 참조 형식 속성은 오류 없이 `null`을 할당할 수 있는 경우를 제외하고는 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-153">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="3be1a-154">다음 표에는 래퍼 형식의 전체 목록 및 이와 동등한 C# 형식이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-154">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="3be1a-155">C# 형식</span><span class="sxs-lookup"><span data-stu-id="3be1a-155">C# type</span></span>      | <span data-ttu-id="3be1a-156">잘 알려진 형식 래퍼</span><span class="sxs-lookup"><span data-stu-id="3be1a-156">Well-Known Type wrapper</span></span>       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a><span data-ttu-id="3be1a-157">바이트</span><span class="sxs-lookup"><span data-stu-id="3be1a-157">Bytes</span></span>

<span data-ttu-id="3be1a-158">이진 페이로드는 Protobuf에서 `bytes` 스칼라 값 형식으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-158">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="3be1a-159">C#에서 생성된 속성은 `ByteString`을 속성 형식으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-159">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="3be1a-160">`ByteString.CopyFrom(byte[] data)`을 사용하여 바이트 배열에서 새 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-160">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="3be1a-161">`ByteString` 데이터는 `ByteString.Span` 또는 `ByteString.Memory`를 사용하여 직접 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-161">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="3be1a-162">또는 `ByteString.ToByteArray()`를 호출하여 인스턴스를 바이트 배열로 다시 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-162">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="3be1a-163">10진수</span><span class="sxs-lookup"><span data-stu-id="3be1a-163">Decimals</span></span>

<span data-ttu-id="3be1a-164">Protobuf는 기본적으로 .NET `decimal` 형식을 지원하지 않으며 `double` 및 `float`만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-164">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="3be1a-165">Protobuf 프로젝트에서는 표준 10진수 형식을 잘 알려진 형식에 추가하고 언어에 대한 플랫폼 지원 및 이 플랫폼을 지원하는 프레임워크를 포함하는 가능성에 대해 지속해서 논의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-165">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="3be1a-166">아직 아무것도 구현되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-166">Nothing has been implemented yet.</span></span>

<span data-ttu-id="3be1a-167">.NET 클라이언트와 서버 간에 안전하게 직렬화하는 데 사용할 수 있는 `decimal` 형식을 나타내는 메시지 정의를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-167">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="3be1a-168">그러나 다른 플랫폼의 개발자는 사용되는 형식을 이해하고 이 형식에 대한 고유한 처리를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-168">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="3be1a-169">Protobuf에 대한 사용자 지정 10진수 형식 만들기</span><span class="sxs-lookup"><span data-stu-id="3be1a-169">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="3be1a-170">`nanos` 필드는 `0.999_999_999`부터 `-0.999_999_999`까지의 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-170">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="3be1a-171">예를 들어 `decimal` 값 `1.5m`은 `{ units = 1, nanos = 500_000_000 }`으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-171">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="3be1a-172">이런 이유로 이 예제에서 비교적 큰 값에 대해 `int32`보다 더 효율적으로 인코딩 `sfixed32` 형식을 `nanos` 필드에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-172">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="3be1a-173">`units` 필드가 음수이면 `nanos` 필드도 음수여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-173">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="3be1a-174">`decimal` 값을 바이트 문자열로 인코딩하기 위한 다른 여러 알고리즘이 있지만, 이 메시지가 더 쉽게 이해됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-174">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="3be1a-175">값은 다양한 플랫폼에서 big-endian 또는 little-endian의 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-175">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="3be1a-176">이 형식과 BCL `decimal` 형식 간의 변환은 다음과 같이 C#로 구현될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-176">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="3be1a-177">컬렉션</span><span class="sxs-lookup"><span data-stu-id="3be1a-177">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="3be1a-178">목록</span><span class="sxs-lookup"><span data-stu-id="3be1a-178">Lists</span></span>

<span data-ttu-id="3be1a-179">Protobuf의 목록은 필드에 `repeated` 접두사 키워드를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-179">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="3be1a-180">다음 예에서는 목록을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-180">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="3be1a-181">생성된 코드에서 `repeated` 필드는 `Google.Protobuf.Collections.RepeatedField<T>` 제네릭 형식으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-181">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="3be1a-182">`RepeatedField<T>`는 <xref:System.Collections.Generic.IList%601>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-182">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="3be1a-183">따라서 LINQ 쿼리를 사용하거나 배열 또는 목록으로 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-183">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="3be1a-184">`RepeatedField<T>` 속성에는 public setter가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-184">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="3be1a-185">항목을 기존 컬렉션에 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-185">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="3be1a-186">사전</span><span class="sxs-lookup"><span data-stu-id="3be1a-186">Dictionaries</span></span>

<span data-ttu-id="3be1a-187">.NET <xref:System.Collections.Generic.IDictionary%602> 형식은 Protobuf에서 `map<key_type, value_type>`을 사용하여 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-187">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="3be1a-188">생성된 .NET 코드에서 `map` 필드는 `Google.Protobuf.Collections.MapField<TKey, TValue>` 제네릭 형식으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-188">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="3be1a-189">`MapField<TKey, TValue>`는 <xref:System.Collections.Generic.IDictionary%602>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-189">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="3be1a-190">`repeated` 속성과 마찬가지로 `map` 속성에도 public setter가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-190">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="3be1a-191">항목을 기존 컬렉션에 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-191">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="3be1a-192">비구조적 메시지 및 조건부 메시지</span><span class="sxs-lookup"><span data-stu-id="3be1a-192">Unstructured and conditional messages</span></span>

<span data-ttu-id="3be1a-193">Protobuf는 계약 중심 메시징 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-193">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="3be1a-194">필드와 유형을 포함하여 앱의 메시지는 앱을 빌드할 때 `.proto` 파일에서 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-194">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="3be1a-195">Protobuf의 계약 중심 디자인은 메시지 콘텐츠를 적용하는 데 적합하지만 다음과 같이 엄격한 계약이 필요하지 않은 시나리오를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-195">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="3be1a-196">알 수 없는 페이로드가 포함된 메시지.</span><span class="sxs-lookup"><span data-stu-id="3be1a-196">Messages with unknown payloads.</span></span> <span data-ttu-id="3be1a-197">예를 들어 메시지를 포함할 수 있는 필드가 있는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-197">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="3be1a-198">조건부 메시지.</span><span class="sxs-lookup"><span data-stu-id="3be1a-198">Conditional messages.</span></span> <span data-ttu-id="3be1a-199">예를 들어 gRPC 서비스에서 반환된 메시지는 성공 결과 또는 오류 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-199">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="3be1a-200">동적 값.</span><span class="sxs-lookup"><span data-stu-id="3be1a-200">Dynamic values.</span></span> <span data-ttu-id="3be1a-201">예를 들어 JSON과 유사한 비구조적 값 컬렉션을 포함하는 필드가 있는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-201">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="3be1a-202">Protobuf는 해당 시나리오를 지원하기 위한 언어 기능과 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-202">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="3be1a-203">모두</span><span class="sxs-lookup"><span data-stu-id="3be1a-203">Any</span></span>

<span data-ttu-id="3be1a-204">`Any` 형식을 사용하면 `.proto` 정의 없이도 메시지를 포함된 형식으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-204">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="3be1a-205">`Any` 형식을 사용하려면 `any.proto`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-205">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="3be1a-206">Oneof</span><span class="sxs-lookup"><span data-stu-id="3be1a-206">Oneof</span></span>

<span data-ttu-id="3be1a-207">`oneof` 필드는 언어 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-207">`oneof` fields are a language feature.</span></span> <span data-ttu-id="3be1a-208">컴파일러는 메시지 클래스를 생성할 때 `oneof` 키워드를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-208">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="3be1a-209">`oneof`를 사용하여 `Person` 또는 `Error`를 반환할 수 있는 응답 메시지를 지정하는 것은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-209">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="3be1a-210">`oneof` 집합 내의 필드는 전체 메시지 선언에서 고유한 필드 번호가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-210">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="3be1a-211">`oneof`를 사용하면 생성된 C# 코드에는 설정된 필드를 지정하는 열거형이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-211">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="3be1a-212">열거형을 테스트하여 설정된 필드를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-212">You can test the enum to find which field is set.</span></span> <span data-ttu-id="3be1a-213">설정되지 않은 필드는 예외를 throw하는 대신 `null` 또는 기본값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-213">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="3be1a-214">값</span><span class="sxs-lookup"><span data-stu-id="3be1a-214">Value</span></span>

<span data-ttu-id="3be1a-215">`Value` 형식은 동적으로 형식화된 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-215">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="3be1a-216">`null`, 숫자, 문자열, 부울, 값의 사전(`Struct`)이거나 값의 목록(`ValueList`)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-216">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="3be1a-217">`Value`는 앞에서 설명한 `oneof` 기능을 사용하는 Protobuf 잘 알려진 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-217">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="3be1a-218">`Value` 형식을 사용하려면 `struct.proto`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-218">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="3be1a-219">`Value`를 직접 사용하면 길어질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-219">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="3be1a-220">`Value`의 대안으로 Protobuf에서는 메시지를 JSON에 매핑하도록 지원을 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-220">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="3be1a-221">Protobuf의 `JsonFormatter` 형식과 `JsonWriter` 형식을 Protobuf 메시지에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-221">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="3be1a-222">`Value`는 JSON으로/에서 변환하는 데 특히 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-222">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="3be1a-223">다음은 위의 코드와 동등한 JSON 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3be1a-223">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="3be1a-224">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="3be1a-224">Additional resources</span></span>

* [<span data-ttu-id="3be1a-225">Protobuf 언어 가이드</span><span class="sxs-lookup"><span data-stu-id="3be1a-225">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
