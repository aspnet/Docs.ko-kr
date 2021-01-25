---
title: ASP.NET Core Kestrel 웹 서버에서 역방향 프록시를 사용하는 경우
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel 앞에서 역방향 프록시를 사용하는 경우에 관해 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253855"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kestrel을 역방향 프록시와 함께 사용하는 경우

Kestrel을 단독으로 사용하거나 [IIS(인터넷 정보 서비스)](https://www.iis.net/), [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)와 같은 *역방향 프록시 서버* 와 함께 사용할 수 있습니다. 역방향 프록시 서버는 네트워크에서 HTTP 요청을 받아서 Kestrel에 전달합니다.

Kestrel은 에지(인터넷 연결) 웹 서버로 사용됩니다.

![Kestrel이 역방향 프록시 서버 없이 직접 인터넷과 통신합니다.](_static/kestrel-to-internet2.png)

Kestrel은 역방향 프록시 구성에 사용됩니다.

![Kestrel이 IIS, Nginx 또는 Apache 같은 역방향 프록시 서버를 통해 간접적으로 인터넷과 통신합니다.](_static/kestrel-to-internet.png)

역방향 프록시 서버가 있는 구성과 없는 구성 모두 지원되는 호스팅 구성입니다.

Kestrel이 역방향 프록시 서버 없이 에지 서버로 사용되는 경우 여러 프로세스 간에 동일한 IP 주소 및 포트 공유는 지원되지 않습니다. Kestrel이 포트에서 수신 대기하도록 구성된 경우 Kestrel은 요청의 `Host` 헤더와 관계없이 해당 포트의 모든 트래픽을 처리합니다. 포트를 공유할 수 있는 역방향 프록시는 고유 IP 및 포트에서 Kestrel에 요청을 전달할 수 있습니다.

역방향 프록시 서버가 필요하지 않은 경우에도 역방향 프록시 서버를 사용하는 것은 적합한 선택일 수 있습니다.

역방향 프록시:

* 호스트하는 앱의 공개된 공용 노출 영역을 제한할 수 있습니다.
* 구성 및 방어의 추가 계층을 제공합니다.
* 기존 인프라와 잘 통합될 수 있습니다.
* 부하 분산 및 보안 통신(HTTPS) 구성을 간소화합니다. 역방향 프록시 서버에 X.509 인증서가 필요한 경우에만 해당 서버는 일반 HTTP를 사용하여 내부 네트워크에서 앱 서버와 통신할 수 있습니다.

> [!WARNING]
> 역방향 프록시 구성에서 호스팅하려면 [호스트 필터링](xref:fundamentals/servers/kestrel/host-filtering)이 필요합니다.

## <a name="additional-resources"></a>추가 리소스

<xref:host-and-deploy/proxy-load-balancer>

