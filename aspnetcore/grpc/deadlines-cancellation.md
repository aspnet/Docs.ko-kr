---
title: 최종 기한 및 취소를 사용하여 안정적인 gRPC 서비스
author: jamesnk
description: .NET에서 최종 기한 및 취소를 사용하여 안정적인 gRPC 서비스를 만드는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059925"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>최종 기한 및 취소를 사용하여 안정적인 gRPC 서비스

작성자: [James Newton-King](https://twitter.com/jamesnk)

최종 기한 및 취소는 gRPC 클라이언트에서 진행 중인 호출을 중단하는 데 사용하는 기능입니다. 이 문서에서는 최종 기한 및 취소가 중요한 이유와 .NET gRPC 앱에서 사용하는 방법에 대해 설명합니다.

## <a name="deadlines"></a>최종 기한

최종 기한을 사용하면 gRPC 클라이언트에서 호출이 완료될 때까지 대기하는 기간을 지정할 수 있습니다. 최종 기한이 초과하면 호출이 취소됩니다. 최종 기한 설정은 호출을 실행할 수 있는 기간에 대한 상한을 제공하기 때문에 중요합니다. 또한 오작동하는 서비스가 계속 실행되어 서버 리소스가 소진되는 것을 방지합니다. 최종 기한은 안정적인 앱을 빌드하는 데 유용한 도구이므로 구성해야 합니다.

최종 기한 구성:

* 최종 기한은 호출할 때 `CallOptions.Deadline`을 사용하여 구성합니다.
* 기본 최종 기한 값은 없습니다. gRPC 호출은 최종 기한을 지정하지 않는 한 시간이 제한되지 않습니다.
* 최종 기한은 최종 기한이 초과하는 시점의 UTC 시간입니다. 예를 들어 `DateTime.UtcNow.AddSeconds(5)`는 최종 기한이 지금부터 5초임을 나타냅니다.
* 이전 또는 현재 시간을 사용하면 호출이 바로 최종 기한을 초과하게 됩니다.
* 최종 기한은 gRPC 호출을 통해 서비스에 전송되고 클라이언트와 서비스 모두에서 독립적으로 추적됩니다. 한 머신에서는 gRPC 호출이 완료되었지만 응답이 클라이언트에 반환된 시간까지 최종 기한이 초과하였을 수 있습니다.

최종 기한을 초과하는 경우 클라이언트와 서비스의 동작이 다릅니다.

* 클라이언트는 기본 HTTP 요청을 즉시 중단하고 `DeadlineExceeded` 오류를 throw합니다. 클라이언트 앱에서는 오류를 catch하고 사용자에게 시간 제한 메시지를 표시하도록 선택할 수 있습니다.
* 서버에서는 실행 중인 HTTP 요청이 중단되고 [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken)이 생성됩니다. HTTP 요청은 중단되지만 gRPC 호출은 메서드가 완료될 때까지 서버에서 계속 실행됩니다. 취소 토큰을 비동기 메서드에 전달하여 호출과 함께 메서드가 취소되도록 해야 합니다. 예를 들어 비동기 데이터베이스 쿼리와 HTTP 요청에 취소 토큰을 전달합니다. 취소 토큰을 전달하면 취소된 호출이 서버에서 신속하게 완료되어 다른 호출을 위한 리소스를 확보할 수 있습니다.

`CallOptions.Deadline`을 구성하여 gRPC 호출의 최종 기한을 설정합니다.

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

gRPC 서비스에서 `ServerCallContext.CancellationToken`을 사용합니다.

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>최종 기한 전파

실행 중인 gRPC 서비스에서 gRPC를 호출하는 경우 최종 기한을 전파해야 합니다. 예를 들어:

1. 클라이언트 앱에서 최종 기한이 있는 `FrontendService.GetUser`를 호출합니다.
2. `FrontendService`은 `UserService.GetUser`를 호출합니다. 클라이언트에 지정된 최종 기한을 새 gRPC 호출에 지정해야 합니다.
3. `UserService.GetUser`에서 최종 기한을 받습니다. 클라이언트 앱의 최종 기한이 초과하면 올바르게 시간 초과됩니다.

호출 컨텍스트에서는 `ServerCallContext.Deadline`에 최종 기한을 제공합니다.

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

수동으로 최종 기한을 전파하면 복잡할 수 있습니다. 최종 기한을 모든 호출에 전달해야 하므로 실수로 누락하기가 쉽습니다. 자동 솔루션은 gRPC 클라이언트 팩터리에서 사용할 수 있습니다. `EnableCallContextPropagation`을 지정합니다.

* 최종 기한 및 취소 토큰을 자식 호출에 자동으로 전파합니다.
* 복잡한 중첩 gRPC 시나리오에서 최종 기한 및 취소를 항상 전파하도록 하는 가장 좋은 방법입니다.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

자세한 내용은 <xref:grpc/clientfactory#deadline-and-cancellation-propagation>를 참조하세요.

## <a name="cancellation"></a>취소

취소를 사용하면 gRPC 클라이언트에서 더 이상 필요 없는 장기 실행 호출을 취소할 수 있습니다. 예를 들어 사용자가 웹 사이트의 페이지를 방문하면 실시간 업데이트를 스트리밍하는 gRPC 호출이 시작됩니다. 사용자가 페이지를 벗어나면 이 스트림을 취소해야 합니다.

호출 시 [CallOptions.CancellationToken](xref:System.Threading.CancellationToken)으로 취소 토큰을 전달하거나 `Dispose`를 호출하여 클라이언트에서 gRPC 호출을 취소할 수 있습니다.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

취소할 수 있는 gRPC 서비스는 다음을 수행해야 합니다.
* `ServerCallContext.CancellationToken`을 비동기 메서드에 전달합니다. 비동기 메서드를 취소하면 서버 호출을 신속하게 완료할 수 있습니다.
* 취소 토큰을 자식 호출에 전파합니다. 취소 토큰을 전파하면 자식 호출이 부모와 함께 취소됩니다. [gRPC 클라이언트 팩터리](xref:grpc/clientfactory) 및 `EnableCallContextPropagation()`은 취소 토큰을 자동으로 전파합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:grpc/client>
* <xref:grpc/clientfactory>
