---
title: .NET을 사용하는 Code First gRPC 서비스 및 클라이언트
author: jamesnk
description: .NET으로 Code First gRPC 서비스를 작성하는 경우의 기본 개념을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880622"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>.NET을 사용하는 Code First gRPC 서비스 및 클라이언트

작성자: [James Newton-King](https://twitter.com/jamesnk) 및 [Marc Gravell](https://twitter.com/marcgravell)

Code First gRPC는 .NET 형식을 사용하여 서비스 및 메시지 계약을 정의합니다.

Code First는 전체 시스템에서 .NET을 사용하는 경우에 적합합니다.

* .NET 서비스 및 데이터 계약 형식은 .NET 서버와 클라이언트 간에 공유할 수 있습니다.
* `.proto` 파일 및 코드 생성에서 계약을 정의하지 않아도 됩니다.

여러 언어가 있는 폴리글랏(polyglot) 시스템에서는 Code First를 사용하지 않는 것이 좋습니다. .NET 서비스 및 데이터 계약 형식은 .NET 플랫폼이 아닌 플랫폼에 사용할 수 없습니다. 다른 플랫폼은 Code First를 사용하여 작성된 gRPC 서비스를 호출하려면 서비스와 일치하는 `.proto` 계약을 만들어야 합니다.

## <a name="protobuf-netgrpc"></a>protobuf-net.Grpc

> [!IMPORTANT]
> protobuf-net.Grpc에 대한 도움말이 필요하면 [protobuf-net.Grpc 웹 사이트](https://protobuf-net.github.io/protobuf-net.Grpc/)를 방문하거나 [protobuf-net.Grpc GitHub 리포지토리](https://github.com/protobuf-net/protobuf-net.Grpc)에서 문제를 제출하세요.

[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/)는 커뮤니티 프로젝트이며 Microsoft에서 지원하지 않습니다. protobuf-net.Grpc는 `Grpc.AspNetCore` 및 `Grpc.Net.Client`에 Code First 지원을 추가합니다. 또한 특성으로 주석이 지정된 .NET 형식을 사용하여 앱의 gRPC 서비스 및 메시지를 정의합니다.

Code First gRPC 서비스를 만드는 첫 번째 단계는 코드 계약을 정의하는 것입니다.

* 서버와 클라이언트가 공유할 새 프로젝트를 만듭니다.
* [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 패키지 참조를 추가합니다.
* 서비스 및 데이터 계약 형식을 만듭니다.

[!code-csharp[](code-first/Contracts.cs)]

위의 코드는

* `HelloRequest` 및 `HelloReply` 메시지를 정의합니다.
* 단항 `SayHelloAsync` gRPC 메서드로 `IGreeterService` 계약 인터페이스를 정의합니다.

서비스 계약은 서버에서 구현되고 클라이언트에서 호출됩니다. 서비스 인터페이스에 정의된 메서드는 단항인지, 서버 스트리밍인지, 클라이언트 스트리밍인지, 양방향 스트리밍인지에 따라 특정 서명과 일치해야 합니다.

서비스 계약을 정의하는 방법에 대한 자세한 내용은 [protobuf-net.Grpc 시작 설명서](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)를 참조하세요.

## <a name="create-a-code-first-grpc-service"></a>Code First gRPC 서비스 만들기

ASP.NET Core 앱에 gRPC Code First 서비스를 추가하려면 다음을 수행합니다.

* [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) 패키지 참조를 추가합니다.
* 공유 코드 계약 프로젝트에 참조를 추가합니다.

새 `GreeterService.cs` 파일을 만들고 `IGreeterService` 서비스 인터페이스를 구현합니다.

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

`Startup.cs` 파일을 업데이트합니다.

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

앞의 코드에서 다음을 확인할 수 있습니다.

* `AddCodeFirstGrpc`는 Code First를 사용하도록 설정하는 서비스를 등록합니다.
* `MapGrpcService<GreeterService>`는 Code First 서비스 엔드포인트를 추가합니다.

Code First로 구현된 gRPC 서비스와 `.proto` 파일로 구현된 gRPC 서비스는 동일한 앱에 공존할 수 있습니다. 모든 gRPC 서비스는 [gRPC 서비스 구성](xref:grpc/configuration#configure-services-options)을 사용합니다.

## <a name="create-a-code-first-grpc-client"></a>Code First gRPC 클라이언트 만들기

Code First gRPC 클라이언트는 서비스 계약을 사용하여 gRPC 서비스를 호출합니다. Code First 클라이언트를 사용하여 gRPC 서비스를 호출하려면 다음을 수행합니다.

* [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 패키지 참조를 추가합니다.
* 공유 코드 계약 프로젝트에 참조를 추가합니다.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

위의 코드는

* gRPC 채널을 만듭니다.
* `CreateGrpcService<IGreeterService>` 확장 메서드를 사용하여 채널에서 Code First 클라이언트를 만듭니다.
* `SayHelloAsync`를 사용하여 gRPC 서비스를 호출합니다.

Code First gRPC 클라이언트가 채널에서 만들어집니다. 일반 클라이언트와 마찬가지로 Code First 클라이언트는 [채널 구성](xref:grpc/configuration#configure-client-options)을 사용합니다.

## <a name="additional-resources"></a>추가 리소스

* [protobuf-net.Grpc 웹 사이트](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net.Grpc GitHub 리포지토리](https://github.com/protobuf-net/protobuf-net.Grpc)
