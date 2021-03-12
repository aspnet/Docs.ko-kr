---
title: .NET 앱에 대한 Protobuf 메시지 만들기
author: jamesnk
description: .NET 앱에 대한 Protobuf 메시지를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/12/2021
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
ms.openlocfilehash: 65a84c34e182b7a330d14e1f7ace17790ee4ed47
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110172"
---
# <a name="create-protobuf-messages-for-net-apps"></a>.NET 앱에 대한 Protobuf 메시지 만들기

작성자: [James Newton-King](https://twitter.com/jamesnk) 및 [Mark Rendle](https://twitter.com/markrendle)

gRPC는 IDL(Interface Design Language)로 [Protobuf](https://developers.google.com/protocol-buffers)를 사용합니다. Protobuf IDL은 gRPC 서비스에서 보내고 받는 메시지를 지정하기 위한 언어 중립적인 형식입니다. Protobuf 메시지는 `.proto` 파일에서 정의됩니다. 이 문서에서는 Protobuf 개념이 .NET에 어떻게 매핑되는지 설명합니다.

## <a name="protobuf-messages"></a>Protobuf 메시지

메시지는 Protobuf의 기본 데이터 전송 개체입니다. 개념상 .NET 클래스와 유사합니다.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

위의 메시지 정의는 세 필드를 이름-값 쌍으로 지정합니다. .NET 형식의 속성과 마찬가지로 각 필드에는 이름과 형식이 있습니다. 필드 형식은 [Protobuf 스칼라 값 형식](#scalar-value-types)(예: `int32` 또는 다른 메시지)일 수 있습니다.

[Protobuf 스타일 가이드](https://developers.google.com/protocol-buffers/docs/style)에서는 필드 이름에 `underscore_separated_names`를 사용하도록 권장합니다. .NET 앱용으로 생성된 새 Protobuf 메시지는 Protobuf 스타일 지침을 따라야 합니다. .NET 도구는 .NET 명명 표준을 사용하는 .NET 형식을 자동으로 생성합니다. 예를 들어 `first_name` Protobuf 필드는 `FirstName` .NET 속성을 생성합니다.

메시지 정의의 각 필드에는 이름 외에도 고유한 번호가 있습니다. 필드 번호는 메시지가 Protobuf로 직렬화될 때 필드를 식별하는 데 사용됩니다. 작은 수를 직렬화하는 것은 전체 필드 이름을 직렬화하는 것보다 더 빠릅니다. 필드 번호는 필드를 식별하기 때문에 필드를 변경할 때는 주의해야 합니다. Protobuf 메시지 변경에 대한 자세한 내용은 <xref:grpc/versioning>를 참조하세요.

앱을 빌드할 때 Protobuf 도구는 `.proto` 파일에서 .NET 형식을 생성합니다. `Person` 메시지는 .NET 클래스를 생성합니다.

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Protobuf 메시지에 대한 자세한 내용은 [Protobuf 언어 가이드](https://developers.google.com/protocol-buffers/docs/proto3#simple)를 참조하세요.

## <a name="scalar-value-types"></a>스칼라 값 형식

Protobuf는 네이티브 스칼라 값 형식의 범위를 지원합니다. 모든 값 형식 및 이와 동등한 C# 형식이 다음 표에 나와 있습니다.

| Protobuf 형식 | C# 형식      |
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

스칼라 값에는 항상 기본값이 있으며 `null`로 설정할 수 없습니다. 이 제약 조건에는 C# 클래스인 `string`과 `ByteString`이 포함됩니다. `string` 기본값은 빈 문자열 값이며 `ByteString` 기본값은 빈 바이트 값입니다. 기본값을 `null`로 설정하려고 하면 오류가 throw됩니다.

[null 허용 래퍼 형식](#nullable-types)을 사용하여 null 값을 지원할 수 있습니다.

### <a name="dates-and-times"></a>날짜 및 시간

네이티브 스칼라 형식은 .NET의 <xref:System.DateTimeOffset>, <xref:System.DateTime>, <xref:System.TimeSpan>에 해당하는 날짜 및 시간 값을 제공하지 않습니다. 해당 형식은 Protobuf의 ‘잘 알려진 형식’ 확장 중 일부를 사용하여 지정할 수 있습니다. 이 확장은 지원되는 플랫폼에서 복합 필드 형식을 위한 코드 생성과 런타임을 지원합니다.

다음 표에는 날짜 및 시간 형식이 나와 있습니다.

| .NET 형식        | Protobuf 잘 알려진 형식    |
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

C# 클래스에서 생성되는 속성은 .NET 날짜 및 시간 형식이 아닙니다. 해당 속성은 `Google.Protobuf.WellKnownTypes` 네임스페이스의 `Timestamp` 클래스와 `Duration` 클래스를 사용하며, 이 클래스는 `DateTimeOffset`, `DateTime`, `TimeSpan`으로/에서 변환하는 메서드를 제공합니다.

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
> `Timestamp` 형식은 UTC 시간으로 작동합니다. `DateTimeOffset` 값의 오프셋은 항상 0이며 `DateTime.Kind` 속성은 항상 `DateTimeKind.Utc`입니다.

### <a name="nullable-types"></a>Nullable 유형

C#에 해당하는 Protobuf 코드를 생성하는 데에는 네이티브 형식을 사용합니다(예: `int32`에 대해 `int` 사용). 따라서 값이 항상 포함되며 `null`일 수 없습니다.

C# 코드에 `int?`가 사용되는 것과 같이 명시적인 `null`이 필요한 값의 경우 Protobuf의 잘 알려진 형식은 null 허용 C# 형식으로 컴파일되는 래퍼를 포함합니다. 이 래퍼를 사용하려면 다음 코드와 같이 `.proto` 파일에 `wrappers.proto`를 가져옵니다.

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

`wrappers.proto` 형식은 생성된 속성에 노출되지 않습니다. Protobuf는 C# 메시지에서 적절한 .NET null 허용 형식에 자동으로 매핑합니다. 예를 들어 `google.protobuf.Int32Value` 필드는 `int?` 속성을 생성합니다. `string` 및 `ByteString` 같은 참조 형식 속성은 오류 없이 `null`을 할당할 수 있는 경우를 제외하고는 변경되지 않습니다.

다음 표에는 래퍼 형식의 전체 목록 및 이와 동등한 C# 형식이 나와 있습니다.

| C# 형식      | 잘 알려진 형식 래퍼       |
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

### <a name="bytes"></a>바이트

이진 페이로드는 Protobuf에서 `bytes` 스칼라 값 형식으로 지원됩니다. C#에서 생성된 속성은 `ByteString`을 속성 형식으로 사용합니다.

`ByteString.CopyFrom(byte[] data)`을 사용하여 바이트 배열에서 새 인스턴스를 만듭니다.

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

`ByteString` 데이터는 `ByteString.Span` 또는 `ByteString.Memory`를 사용하여 직접 액세스합니다. 또는 `ByteString.ToByteArray()`를 호출하여 인스턴스를 바이트 배열로 다시 변환합니다.

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>10진수

Protobuf는 기본적으로 .NET `decimal` 형식을 지원하지 않으며 `double` 및 `float`만 지원합니다. Protobuf 프로젝트에서는 표준 10진수 형식을 잘 알려진 형식에 추가하고 언어에 대한 플랫폼 지원 및 이 플랫폼을 지원하는 프레임워크를 포함하는 가능성에 대해 지속해서 논의하고 있습니다. 아직 아무것도 구현되지 않았습니다.

.NET 클라이언트와 서버 간에 안전하게 직렬화하는 데 사용할 수 있는 `decimal` 형식을 나타내는 메시지 정의를 만들 수 있습니다. 그러나 다른 플랫폼의 개발자는 사용되는 형식을 이해하고 이 형식에 대한 고유한 처리를 구현해야 합니다.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Protobuf에 대한 사용자 지정 10진수 형식 만들기

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

`nanos` 필드는 `0.999_999_999`부터 `-0.999_999_999`까지의 값을 나타냅니다. 예를 들어 `decimal` 값 `1.5m`은 `{ units = 1, nanos = 500_000_000 }`으로 표시됩니다. 이런 이유로 이 예제에서 비교적 큰 값에 대해 `int32`보다 더 효율적으로 인코딩 `sfixed32` 형식을 `nanos` 필드에 사용합니다. `units` 필드가 음수이면 `nanos` 필드도 음수여야 합니다.

> [!NOTE]
> `decimal` 값을 바이트 문자열로 인코딩하기 위한 다른 여러 알고리즘이 있지만, 이 메시지가 더 쉽게 이해됩니다. 값은 다양한 플랫폼에서 big-endian 또는 little-endian의 영향을 받지 않습니다.

이 형식과 BCL `decimal` 형식 간의 변환은 다음과 같이 C#로 구현될 수 있습니다.

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

## <a name="collections"></a>컬렉션

### <a name="lists"></a>목록

Protobuf의 목록은 필드에 `repeated` 접두사 키워드를 사용하여 지정합니다. 다음 예에서는 목록을 만드는 방법을 보여 줍니다.

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

생성된 코드에서 `repeated` 필드는 `Google.Protobuf.Collections.RepeatedField<T>` 제네릭 형식으로 표시됩니다.

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>`는 <xref:System.Collections.Generic.IList%601>를 구현합니다. 따라서 LINQ 쿼리를 사용하거나 배열 또는 목록으로 변환할 수 있습니다. `RepeatedField<T>` 속성에는 public setter가 없습니다. 항목을 기존 컬렉션에 추가해야 합니다.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>사전

.NET <xref:System.Collections.Generic.IDictionary%602> 형식은 Protobuf에서 `map<key_type, value_type>`을 사용하여 표시됩니다.

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

생성된 .NET 코드에서 `map` 필드는 `Google.Protobuf.Collections.MapField<TKey, TValue>` 제네릭 형식으로 표시됩니다. `MapField<TKey, TValue>`는 <xref:System.Collections.Generic.IDictionary%602>를 구현합니다. `repeated` 속성과 마찬가지로 `map` 속성에도 public setter가 없습니다. 항목을 기존 컬렉션에 추가해야 합니다.

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

## <a name="unstructured-and-conditional-messages"></a>비구조적 메시지 및 조건부 메시지

Protobuf는 계약 중심 메시징 형식입니다. 필드와 유형을 포함하여 앱의 메시지는 앱을 빌드할 때 `.proto` 파일에서 지정해야 합니다. Protobuf의 계약 중심 디자인은 메시지 콘텐츠를 적용하는 데 적합하지만 다음과 같이 엄격한 계약이 필요하지 않은 시나리오를 제한할 수 있습니다.

* 알 수 없는 페이로드가 포함된 메시지. 예를 들어 메시지를 포함할 수 있는 필드가 있는 메시지입니다.
* 조건부 메시지. 예를 들어 gRPC 서비스에서 반환된 메시지는 성공 결과 또는 오류 결과일 수 있습니다.
* 동적 값. 예를 들어 JSON과 유사한 비구조적 값 컬렉션을 포함하는 필드가 있는 메시지입니다.

Protobuf는 해당 시나리오를 지원하기 위한 언어 기능과 형식을 제공합니다.

### <a name="any"></a>모두

`Any` 형식을 사용하면 `.proto` 정의 없이도 메시지를 포함된 형식으로 사용할 수 있습니다. `Any` 형식을 사용하려면 `any.proto`를 가져옵니다.

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

### <a name="oneof"></a>Oneof

`oneof` 필드는 언어 기능입니다. 컴파일러는 메시지 클래스를 생성할 때 `oneof` 키워드를 처리합니다. `oneof`를 사용하여 `Person` 또는 `Error`를 반환할 수 있는 응답 메시지를 지정하는 것은 다음과 같습니다.

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

`oneof` 집합 내의 필드는 전체 메시지 선언에서 고유한 필드 번호가 있어야 합니다.

`oneof`를 사용하면 생성된 C# 코드에는 설정된 필드를 지정하는 열거형이 포함됩니다. 열거형을 테스트하여 설정된 필드를 찾을 수 있습니다. 설정되지 않은 필드는 예외를 throw하는 대신 `null` 또는 기본값을 반환합니다.

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

### <a name="value"></a>값

`Value` 형식은 동적으로 형식화된 값을 나타냅니다. `null`, 숫자, 문자열, 부울, 값의 사전(`Struct`)이거나 값의 목록(`ValueList`)일 수 있습니다. `Value`는 앞에서 설명한 `oneof` 기능을 사용하는 Protobuf 잘 알려진 형식입니다. `Value` 형식을 사용하려면 `struct.proto`를 가져옵니다.

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

`Value`를 직접 사용하면 길어질 수 있습니다. `Value`의 대안으로 Protobuf에서는 메시지를 JSON에 매핑하도록 지원을 기본 제공합니다. Protobuf의 `JsonFormatter` 형식과 `JsonWriter` 형식을 Protobuf 메시지에 사용할 수 있습니다. `Value`는 JSON으로/에서 변환하는 데 특히 적합합니다.

다음은 위의 코드와 동등한 JSON 코드입니다.

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Data);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>추가 리소스

* [Protobuf 언어 가이드](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
