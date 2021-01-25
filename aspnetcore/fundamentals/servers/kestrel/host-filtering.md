---
title: ASP.NET Core Kestrel 웹 서버를 사용한 호스트 필터링
author: rick-anderson
description: ASP.NET Core의 플랫폼 간 웹 서버인 Kestrel에서 호스트 필터링을 사용하는 방법을 알아봅니다.
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253878"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel 웹 서버를 사용한 호스트 필터링

Kestrel은 `http://example.com:5000`과 같은 접두사에 따라 구성을 지원하지만 일반적으로 호스트 이름을 무시합니다. 호스트 `localhost`은 루프백 주소에 바인딩하는 데 사용된 특별한 경우입니다. 명시적 IP 주소를 제외한 모든 호스트는 모든 공용 IP 주소에 바인딩합니다. `Host` 헤더의 유효성이 검사되지 않았습니다.

해결 방법으로 호스트 필터링 미들웨어를 사용합니다. 호스트 필터링 미들웨어는 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱을 위해 암시적으로 제공됩니다. 미들웨어는 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>을 호출하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>에 의해 추가됩니다.

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

호스트 필터링 미들웨어는 기본적으로 비활성화됩니다. 미들웨어를 활성화하려면 *appsettings.json* /*appsettings.\<EnvironmentName>.json* 에서 `AllowedHosts` 키를 정의합니다. 값은 포트 번호 없이 세미콜론으로 구분된 호스트 이름 목록입니다.

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer)에는 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 옵션도 있습니다. 전달된 헤더 미들웨어 및 호스트 필터링 미들웨어는 다양한 시나리오에 대해 유사한 기능을 제공합니다. 전달된 헤더 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 역방향 프록시 서버 또는 부하 분산 장치를 사용하여 요청을 전달하는 동안 `Host` 헤더가 유지되지 않는 경우에 적합합니다. 호스트 필터링 미들웨어를 사용하여 `AllowedHosts`를 설정하는 작업은 Kestrel을 공용 에지 서버로 사용하는 경우 또는 `Host` 헤더를 직접 전달하는 경우에 적합합니다.
>
> 전달된 헤더 미들웨어에 대한 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.
