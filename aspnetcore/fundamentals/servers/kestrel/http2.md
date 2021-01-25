---
title: ASP.NET Core Kestrel 웹 서버에서 HTTP/2 사용
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel에서 HTTP/2를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253871"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel 웹 서버에서 HTTP/2 사용

다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해 [HTTP/2](https://httpwg.org/specs/rfc7540.html)를 사용할 수 있습니다.

* 운영 체제&dagger;
  * Windows Server 2016/Windows 10 이상&Dagger;
  * Linux 및 OpenSSL 1.0.2 이상(예: Ubuntu 16.04 이상)
* 대상 프레임워크: .NET Core 2.2 이상
* [ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결
* TLS 1.2 이상 연결

이후 릴리스에서는 macOS에서 &dagger;HTTP/2가 지원됩니다.
&Dagger;Kestrel은 Windows Server 2012 R2와 Windows 8.1에서의 HTTP/2 지원을 제한했습니다. 이러한 운영 체제에서 사용할 수 있는 지원 가능 TLS 암호 그룹 목록이 제한되므로 지원이 제한됩니다. TLS 연결을 보호하는 데 ECDSA(타원 곡선 디지털 서명 알고리즘)를 사용하여 생성된 인증서가 필요할 수 있습니다.

HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)에서 `HTTP/2`을 보고합니다.

.NET Core 3.0부터 HTTP/2는 기본적으로 사용됩니다. 구성에 관한 자세한 내용은 [Kestrel HTTP/2 제한](xref:fundamentals/servers/kestrel/options#http2-limits) 및 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 섹션을 참조하세요.

## <a name="advanced-http2-features"></a>고급 HTTP/2 기능

Kestrel의 추가 HTTP/2 기능은 초기화 프레임 전송, 응답 트레일러, gRPC를 지원합니다.

### <a name="trailers"></a>트레일러

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>다시 설정

[!INCLUDE[](~/includes/reset.md)]
