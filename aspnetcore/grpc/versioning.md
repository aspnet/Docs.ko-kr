---
title: gRPC 서비스 버전 관리
author: jamesnk
description: gRPC 서비스의 버전을 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058703"
---
# <a name="versioning-grpc-services"></a>gRPC 서비스 버전 관리

작성자: [James Newton-King](https://twitter.com/jamesnk)

앱에 추가되는 새로운 기능을 사용하려면 클라이언트에 제공되는 gRPC 서비스를 경우에 따라 예기치 않거나 획기적인 방식으로 변경해야 할 수 있습니다. GRPC 서비스 변경 시:

* 변경 내용이 클라이언트에 미치는 영향을 고려해야 합니다.
* 변경을 지원하기 위한 버전 관리 전략을 구현해야 합니다.

## <a name="backwards-compatibility"></a>이전 버전과의 호환성

GRPC 프로토콜은 시간이 지남에 따라 변경되는 서비스를 지원하도록 디자인되었습니다. 일반적으로 gRPC 서비스 및 메서드에 대한 추가는 호환성이 손상되지 않는 변경입니다. 호환성이 손상되지 않는 변경 내용은 기존 클라이언트가 변경 없이 계속 작동되도록 합니다. GRPC 서비스를 변경하거나 삭제하는 것은 호환성이 손상되는 변경입니다. GRPC 서비스에 호환성이 손상되는 변경이 발생하는 경우 해당 서비스를 사용하는 클라이언트를 업데이트한 후 다시 배포해야 합니다.

서비스에 대해 호환성이 손상되지 않는 변경을 수행하면 다음과 같은 여러 가지 이점이 있습니다.

* 기존 클라이언트는 계속 실행됩니다.
* 클라이언트에 호환성이 손상되지 않는 변경을 알리고 업데이트하는 것과 관련된 작업을 방지합니다.
* 서비스의 한 버전만 문서화하고 유지 관리하면 됩니다.

### <a name="non-breaking-changes"></a>호환성이 손상되지 않는 변경

이러한 변경 내용은 gRPC 프로토콜 수준 및 .NET 이진 수준에서 호환성이 손상되지 않는 변경입니다.

* **새 서비스 추가**
* **서비스에 새 메서드 추가**
* **요청 메시지에 필드 추가** - 요청 메시지에 추가된 필드는 설정하지 않을 경우 서버의 [기본값](https://developers.google.com/protocol-buffers/docs/proto3#default)으로 deserialize됩니다. 호환성이 손상되지 않는 변경을 위해서는 새 필드가 이전 클라이언트에 의해 설정되지 않은 경우 서비스가 성공해야 합니다.
* **응답 메시지에 필드 추가** - 응답 메시지에 추가된 필드는 클라이언트에 있는 메시지의 [알 수 없는 필드](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) 컬렉션으로 deserialize됩니다.
* **열거형에 값 추가** - 열거형이 숫자 값으로 serialize됩니다. 새 열거형 값은 클라이언트에서 열거형 이름 없는 열거형 값으로 deserialize됩니다. 호환성이 손상되지 않는 변경을 위해서는 이전 클라이언트가 새 열거형 값을 수신할 때 올바르게 실행되어야 합니다.

### <a name="binary-breaking-changes"></a>이진 호환성이 손상되는 변경

다음 변경 내용은 gRPC 프로토콜 수준에서 호환성이 손상되지 않는 변경이지만 최신 *.proto* 계약 또는 클라이언트 .NET 어셈블리로 업그레이드하는 경우 클라이언트를 업데이트해야 합니다. gRPC 라이브러리를 NuGet에 게시하려는 경우 이진 호환성이 중요합니다.

* **필드 제거** - 제거된 필드의 값은 메시지의 [알 수 없는 필드](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)로 deserialize됩니다. 이것은 gRPC 로토콜 관련 호환성이 손상되는 변경이 아니며 최신 계약으로 업그레이드하는 경우 클라이언트를 업데이트해야 합니다. 제거된 필드 번호를 나중에 실수로 다시 사용하지 않는 것이 중요합니다. 이 문제가 발생하지 않도록 하려면 Protobuf의 [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) 키워드를 사용하여 메시지에 삭제된 필드 번호 및 이름을 지정합니다.
* **메시지 이름 바꾸기** - 메시지 이름은 일반적으로 네트워크에서 전송되지 않으므로 gRPC 프로토콜 관련 호환성이 손상되는 변경이 아닙니다. 최신 계약으로 업그레이드하는 경우에는 클라이언트를 업데이트해야 합니다. 메시지 이름이 메시지 유형을 식별하는 데 사용되는 경우 메시지 이름이 네트워크에서 전송 **되는** 한 가지 경우는 [모든](https://developers.google.com/protocol-buffers/docs/proto3#any) 필드를 사용하는 경우입니다.
* **csharp_namespace 변경** - `csharp_namespace`를 변경하면 생성된 .NET 형식의 네임스페이스가 변경됩니다. 이것은 gRPC 프로토콜 관련 호환성이 손상되는 변경이 아니며 최신 계약으로 업그레이드하는 경우 클라이언트를 업데이트해야 합니다.

### <a name="protocol-breaking-changes"></a>프로토콜 관련 호환성이 손상되는 변경

다음 항목은 프로토콜 및 이진 관련 호환성이 손상되는 변경입니다.

* **필드 이름 바꾸기** - Protobuf 콘텐츠를 사용하면 필드 이름이 생성된 코드에서만 사용됩니다. 필드 번호는 네트워크에서 필드를 식별하는 데 사용됩니다. 필드 이름 바꾸기는 Protobuf에 대한 프로토콜 관련 호환성이 손상되는 변경이 아닙니다. 그러나 서버에서 JSON 콘텐츠를 사용하는 경우 필드 이름 바꾸기는 호환성이 손상되는 변경입니다.
* **필드 데이터 형식 변경** - 필드의 데이터 형식을 [호환되지 않는 형식](https://developers.google.com/protocol-buffers/docs/proto3#updating)으로 변경하면 메시지를 deserialize할 때 오류가 발생합니다. 새 데이터 형식이 호환되는 경우에도 최신 계약으로 업그레이드하는 경우 새 형식을 지원하도록 클라이언트를 업데이트해야 할 수 있습니다.
* **필드 번호 변경** - Protobuf 페이로드를 사용하면 필드 번호가 네트워크에서 필드를 식별하는 데 사용됩니다.
* **패키지, 서비스 또는 메서드 이름 바꾸기** - gRPC는 패키지 이름, 서비스 이름 및 메서드 이름을 사용하여 URL을 작성합니다. 클라이언트는 서버에서 *미구현* 상태를 가져옵니다.
* **서비스 또는 메서드 제거** - 클라이언트는 제거된 메서드를 호출할 때 서버에서 *미구현* 상태를 가져옵니다.

### <a name="behavior-breaking-changes"></a>동작 관련 호환성이 손상되는 변경

호환성이 손상되지 않는 변경을 수행하는 경우 이전 클라이언트가 새 서비스 동작을 계속할 수 있는지 여부도 고려해야 합니다. 예를 들어, 요청 메시지에 새 필드를 추가하는 것은

* 프로토콜 관련 호환성이 손상되는 변경이 아닙니다.
* 새 필드가 설정되지 않은 경우 서버에서 오류 상태를 반환하면 이전 클라이언트에 대해 호환성이 손상되는 변경이 됩니다.

동작 호환성은 앱별 코드에 의해 결정됩니다.

## <a name="version-number-services"></a>버전 번호 서비스

서비스는 이전 클라이언트와의 호환성을 유지하려고 해야 합니다. 결국 앱을 변경하면 호환성이 손상되는 변경이 필요할 수 있습니다. 이전 클라이언트에 대해 호환성이 손상되는 변경을 수행하고 서비스와 함께 강제로 업데이트하는 것은 좋은 사용자 환경이 아닙니다. 호환성이 손상되는 변경을 수행하면서 이전 버전과의 호환성을 유지하는 방법은 여러 버전의 서비스를 게시하는 것입니다.

gRPC는 .NET 네임스페이스와 유사하게 작동 하는 선택적 [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) 지정자를 지원합니다. 실제로 *.proto* 파일에 `option csharp_namespace`가 설정되지 않은 경우 `package`는 생성된 .NET 형식에 대한 .NET 네임스페이스로 사용됩니다. 이 패키지를 사용하여 서비스의 버전 번호와 해당 메시지를 지정할 수 있습니다.

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

서비스 주소를 식별하기 위해 패키지 이름이 서비스 이름과 결합됩니다. 서비스 주소를 사용하면 여러 버전의 서비스를 함께 호스트할 수 있습니다.

* `greet.v1.Greeter`
* `greet.v2.Greeter`

버전이 지정된 서비스의 구현은 *Startup.cs* 에 등록됩니다.

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

패키지 이름에 버전 번호를 포함하면 *v1* 버전을 호출하는 이전 클라이언트를 계속 지원하면서 호환성이 손상되는 변경을 포함하는 서비스의 *v2* 버전을 게시할 수 있습니다. *v2* 서비스를 사용하도록 클라이언트를 업데이트한 후에는 이전 버전을 제거하도록 선택할 수 있습니다. 여러 버전의 서비스를 게시할 예정인 경우:

* 타당한 경우 호환성이 손상되는 변경을 피합니다.
* 호환성이 손상되는 변경을 수행하지 않는 경우에는 버전 번호를 업데이트하지 마세요.
* 호환성이 손상되는 변경을 수행하는 경우에는 버전 번호를 업데이트합니다.

여러 버전의 서비스를 게시하면 중복됩니다. 중복을 줄이려면 서비스 구현의 비즈니스 논리를 이전 및 새 구현에서 다시 사용할 수 있는 중앙 위치로 이동하는 것이 좋습니다.

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

서로 다른 패키지 이름을 사용하여 생성된 서비스와 메시지는 **다른 .NET 형식** 이 됩니다. 비즈니스 논리를 중앙 위치에 이동하려면 메시지를 공통 형식으로 매핑해야 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:grpc/protobuf>
