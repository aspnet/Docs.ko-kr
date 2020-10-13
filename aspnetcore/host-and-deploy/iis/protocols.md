---
title: IIS의 HTTP/2에서 ASP.NET Core 사용
author: rick-anderson
description: IIS에서 HTTP/2 기능을 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654353"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>IIS의 HTTP/2에서 ASP.NET Core 사용

작성자 [Justin Kotalik](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.

* Windows Server 2016 이상/Windows 10 이상
* IIS 10 이상
* TLS 1.2 이상 연결
* [Out of Process를 호스트](xref:host-and-deploy/iis/index#out-of-process-hosting-model)하는 경우: 공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.

In-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다. Out-of-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.

In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.

HTTP/2는 HTTPS/TLS 연결에서 기본적으로 사용됩니다. HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다. IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.

## <a name="advanced-http2-features-to-support-grpc"></a>gRPC를 지원하기 위한 고급 HTTP/2 기능

IIS의 추가 HTTP/2 기능은 응답 트레일러 및 초기화 프레임 전송을 위한 지원을 포함하여 gRPC를 지원합니다.

IIS에서 gRPC를 실행하기 위한 요구 사항:

* In Process 호스팅.
* Windows 10, OS 빌드 20300.1000 이상. Windows 참가자 빌드를 사용해야 할 수 있습니다.
* TLS 1.2 이상 연결

### <a name="trailers"></a>트레일러

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>다시 설정

[!INCLUDE[](~/includes/reset.md)]
