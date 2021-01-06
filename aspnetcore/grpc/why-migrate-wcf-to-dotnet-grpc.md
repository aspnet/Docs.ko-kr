---
title: WCF를 ASP.NET Core gRPC로 마이그레이션하는 이유
author: markrendle
description: 이 문서에서는 최신 아키텍처 및 플랫폼으로 마이그레이션하려는 WCF(Windows Communication Foundation) 개발자에게 ASP.NET Core gRPC가 가장 적합한 이유를 간략하게 설명합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058690"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>WCF(Windows Communication Foundation) 개발자용 gRPC

이 문서에서는 최신 아키텍처 및 플랫폼으로 마이그레이션하려는 WCF(Windows Communication Foundation) 개발자에게 ASP.NET Core gRPC가 가장 적합한 이유를 간략하게 설명합니다.

## <a name="comparison-to-wcf"></a>WCF와 비교

gRPC의 구현 및 접근 방식은 다르지만 gRPC를 사용하여 서비스를 개발하고 사용하는 환경은 WCF 개발자에게 직관적이어야 합니다. WCF 및 gRPC는 목표가 같은 RPC(원격 프로시저 호출) 프레임워크입니다.

* 클라이언트와 서버가 동일한 플랫폼에 있는 것처럼 코딩할 수 있도록 합니다.
* 간소화된 이동식 네트워킹 API를 제공합니다.

인터페이스를 선언하는 프로세스는 다르지만 두 플랫폼 모두 인터페이스를 선언하고 구현하는 요구 사항을 공유합니다. gRPC에서 지원하는 많은 형식의 RPC 호출은 WCF 서비스에 사용할 수 있는 바인딩에도 매핑됩니다. 자세한 내용 및 예제는 [GRPC로 WCF 솔루션 마이그레이션](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)을 참조하세요.

## <a name="benefits-of-grpc"></a>gRPC의 이점

gRPC는 다음과 같은 이유로 다른 접근 방식보다 더 나은 프레임워크를 제공합니다.

### <a name="performance"></a>성능

gRPC는 HTTP/2를 사용합니다. HTTP/1.1과 달리 HTTP/2는 다음과 같습니다.

* 더 작고 더 빠른 이진 프로토콜입니다.
* 컴퓨터에서 구문 분석하는 데 더욱 효율적입니다.
* 단일 연결에 대해 멀티플렉싱 요청을 지원합니다. 멀티플렉싱을 사용하면 서로를 차단하는 요청 없이 하나의 연결을 통해 여러 요청을 보낼 수 있습니다. HTTP/1.1에서는 차단을 “HOL(Head-Of-Line) 차단”이라고 합니다.

gRPC는 효율적인 이진 형식인 Protobuf를 사용하여 메시지를 직렬화합니다. Protobuf 메시지는 다음과 같습니다.
* 직렬화 및 역직렬화가 빠릅니다.
* 텍스트 기반 형식보다 더 적은 대역폭을 사용합니다. 

gRPC는 대역폭 제한이 있는 모바일 디바이스 및 네트워크에 적합한 솔루션입니다.

### <a name="interoperability"></a>상호 운용성

.NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby 및 PHP를 비롯한 모든 주요 프로그래밍 언어와 플랫폼에 대한 gRPC 도구 및 라이브러리가 있습니다. 각 플랫폼의 Protobuf 이진 통신 형식 및 효율적인 코드 생성 덕분에 개발자는 플랫폼 간 고성능 앱을 빌드할 수 있습니다.

### <a name="usability-and-productivity"></a>유용성 및 생산성

gRPC는 포괄적인 RPC 솔루션입니다. 여러 언어 및 플랫폼에서 일관되게 작동합니다. 또한 대부분의 상용구 코드가 자동으로 생성되는 뛰어난 도구를 제공합니다. WCF와 마찬가지로 gRPC는 메시지와 강력한 형식의 클라이언트를 자동으로 생성합니다. 개발자 시간이 확보되어 비즈니스 논리에 집중할 수 있습니다.

### <a name="streaming"></a>스트리밍

gRPC에는 WCF의 전이중 서비스와 유사한 기능을 제공하는 전체 양방향 스트리밍 기능이 있습니다. gRPC 스트리밍은 일반적인 인터넷 연결, 부하 분산 장치 및 서비스 메시를 통해 작동할 수 있습니다.

### <a name="deadlines-timeouts-and-cancellation"></a>최종 기한, 시간 초과 및 취소

gRPC를 통해 클라이언트는 RPC가 완료될 때까지 최대 시간을 지정할 수 있습니다. 지정된 최종 기한을 초과하면 서버에서 클라이언트와 별개로 작업을 취소할 수 있습니다. 후속 gRPC 호출을 통해 최종 기한 및 취소를 전파하면 리소스 사용량 제한을 강제로 적용할 수 있습니다. 클라이언트는 최종 기한을 초과한 경우나 필요에 따라 그 이전에 작업을 중지할 수 있습니다. 예를 들어 클라이언트는 사용자 상호 작용으로 인해 작업을 중지할 수 있습니다.

### <a name="security"></a>보안

gRPC는 TLS 및 HTTP/2를 사용하여 클라이언트와 서버 간에 엔드투엔드 암호화된 연결을 제공할 수 있습니다. 클라이언트 인증서 인증을 지원하여 클라이언트와 서버 간의 보안 및 신뢰가 더욱 강화됩니다.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>WCF를 .NET Core 및 .NET 5로 마이그레이션하는 경로로서 gRPC

.NET Core 및 .NET 5는 다양한 플랫폼에서 서비스를 제공하려는 개발자에게 Microsoft가 원격 통신 솔루션을 제공하는 방식의 변화를 보여줍니다. .NET Core 및 .NET 5는 [WCF 서비스 호출](/dotnet/core/additional-tools/wcf-web-service-reference-guide)을 지원하지만 WCF 호스트에 대한 서버 쪽 지원은 제공하지 않습니다.

다음은 WCF 앱을 현대화하는 경우 권장되는 두 가지 경로입니다.

* gRPC는 최신 기술을 기반으로 하며 RPC 앱 개발자 커뮤니티에서 가장 인기 있는 선택 사항으로 등장했습니다. .NET Core 3.0부터 최신 .NET 플랫폼은 gRPC를 매우 탁월하게 지원합니다. gRPC를 사용하도록 WCF 서비스를 마이그레이션하면 최신 앱에 필요한 RPC 기능, 성능, 상호 운용성을 제공할 수 있습니다.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF)는 .NET Core 및 .NET 5로의 WCF 서비스 호스트를 지원하기 위한 커뮤니티의 노력입니다. 미리 보기 릴리스를 사용할 수 있으며 프로젝트는 프로덕션 준비를 위해 최선을 다하고 있습니다. CoreWCF는 WCF 기능의 하위 세트만 지원하며, 이 기능을 사용하기 위해 마이그레이션하는 .NET Framework 앱에서는 코드 변경 및 테스트를 성공해야 합니다. CoreWCF는 앱이 WCF 서비스를 호출하는 기존 클라이언트와의 호환성을 유지해야 하는 경우에 매우 적합합니다.

## <a name="get-started"></a>시작

WCF 개발자를 위해 ASP.NET Core에서 gRPC 서비스를 구축하는 방법에 대한 자세한 지침은 [WCF 개발자를 위한 ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)를 참조하세요.
