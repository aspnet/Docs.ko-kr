---
title: 웹 팜에 ASP.NET Core 호스트
author: rick-anderson
description: 웹 팜 환경에서 공유 리소스가 있는 ASP.NET Core 앱의 여러 인스턴스를 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: ee78e80a4eda3089943765700aa6bb62c6c1e07d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057520"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>웹 팜에 ASP.NET Core 호스트

작성자: [Chris Ross](https://github.com/Tratcher)

‘웹 팜’은 여러 앱 인스턴스를 호스트하는 둘 이상의 웹 서버(또는 ‘노드’)의 그룹입니다.  사용자의 요청이 웹 팜에 도착하면 ‘부하 분산 장치’가 요청을 웹 팜의 노드에 배포합니다. 웹 팜은 다음을 개선합니다.

* **안정성/가용성** : 하나 이상의 노드가 실패하면 부하 분산 장치는 요청을 다른 작동 노드로 라우팅하여 요청 처리를 계속할 수 있습니다.
* **용량/성능** : 여러 노드에서 단일 서버보다 많은 요청을 처리할 수 있습니다. 부하 분산 장치는 노드에 요청을 배포하여 워크로드를 분산합니다.
* **확장성** : 더 많거나 더 적은 용량이 필요할 경우 워크로드에 맞게 활성 노드 수가 증가하거나 감소할 수 있습니다. [Azure App Service](https://azure.microsoft.com/services/app-service/)와 같은 웹 팜 플랫폼 기술은 시스템 관리자의 요청 시 또는 사용자 개입 없이 자동으로 노드를 자동으로 추가하거나 제거할 수 있습니다.
* **유지 관리** : 웹 팜의 노드는 공유 서비스 집합을 사용하므로 시스템을 더 쉽게 관리할 수 있습니다. 예를 들어 웹 팜의 노드는 단일 데이터베이스 서버 및 정적 리소스(예: 이미지 및 다운로드 가능한 파일)의 일반 네트워크 위치를 사용할 수 있습니다.

이 항목에서는 공유 리소스를 사용하는 웹 팜에 호스트된 ASP.NET Core 앱의 구성 및 종속성을 설명합니다.

## <a name="general-configuration"></a>일반 구성

<xref:host-and-deploy/index>  
호스팅 환경을 설정하고 ASP.NET Core 앱을 배포하는 방법을 알아봅니다. 웹 팜의 각 노드에서 프로세스 관리자를 구성하여 앱 시작 및 다시 시작을 자동화합니다. 각 노드에는 ASP.NET Core 런타임이 필요합니다. 자세한 내용은 문서의 [호스트 및 배포](xref:host-and-deploy/index) 영역에 있는 항목을 참조하세요.

<xref:host-and-deploy/proxy-load-balancer>  
중요한 요청 정보를 종종 숨기는 프록시 서버 및 부하 분산 장치 뒤에 호스트되는 앱의 구성에 대해 알아봅니다.

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/)는 ASP.NET Core를 비롯한 웹앱을 호스트하기 위한 [Microsoft 클라우드 컴퓨팅 플랫폼 서비스](https://azure.microsoft.com/)입니다. App Service는 자동 크기 조정, 부하 분산, 패치 적용 및 지속적인 배포를 제공하는 완전히 관리되는 플랫폼입니다.

## <a name="app-data"></a>앱 데이터

앱이 여러 인스턴스로 확장되는 경우 노드 간에 앱 상태를 공유해야 할 수 있습니다. 일시적 상태인 경우 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)를 공유하는 것이 좋습니다. 공유 상태에 지속성이 필요한 경우 데이터베이스에 공유 상태를 저장하는 것이 좋습니다.

## <a name="required-configuration"></a>필수 구성

데이터 보호 및 캐싱에는 웹 팜에 배포된 앱에 대한 구성이 필요합니다.

### <a name="data-protection"></a>데이터 보호

[ASP.NET Core 데이터 보호 시스템](xref:security/data-protection/introduction)은 앱에서 데이터를 보호하는 데 사용됩니다. 데이터 보호에는 ‘키 링’에 저장된 암호화 키 집합을 사용합니다. 데이터 보호 시스템이 초기화되면 키 링을 로컬로 저장하는 [기본 설정](xref:security/data-protection/configuration/default-settings)을 적용합니다. 기본 구성에서는 고유 키 링이 웹 팜의 각 노드에 저장됩니다. 따라서 각 웹 팜 노드는 다른 노드의 앱으로 암호화된 데이터를 암호 해독할 수 없습니다. 일반적으로 웹 팜에서 앱을 호스트하는 경우에는 기본 구성이 적합하지 않습니다. 공유 키 링을 구현하는 대신 항상 사용자 요청을 동일한 노드로 라우팅할 수 있습니다. 웹 팜 배포의 데이터 보호 시스템 구성에 대한 자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.

### <a name="caching"></a>캐싱

웹 팜 환경에서 캐싱 메커니즘은 웹 팜의 노드에서 캐시된 항목을 공유해야 합니다. 캐싱은 일반적인 Redis 캐시, 공유 SQL Server 데이터베이스 또는 웹 팜에서 캐시된 항목을 공유하는 사용자 지정 캐싱 구현을 사용해야 합니다. 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.

## <a name="dependent-components"></a>종속 구성 요소

다음 시나리오에서는 추가 구성이 필요하지 않지만 웹 팜 구성이 필요한 기술을 사용합니다.

| 시나리오 | &hellip; 사용 |
| -------- | ------------------- |
| 인증 | 데이터 보호(<xref:security/data-protection/configuration/overview> 참조).<br><br>자세한 내용은 <xref:security/authentication/cookie> 및 <xref:security/cookie-sharing>를 참조하세요. |
| Identity | 인증 및 데이터베이스 구성.<br><br>자세한 내용은 <xref:security/authentication/identity>를 참조하세요. |
| 세션 | 데이터 보호(암호화된 cookie)(<xref:security/data-protection/configuration/overview> 참조) 및 캐싱(<xref:performance/caching/distributed> 참조).<br><br>자세한 내용은 [세션 및 앱 상태: 세션 상태](xref:fundamentals/app-state#session-state)를 참조하세요. |
| TempData | 데이터 보호(암호화된 cookie)(<xref:security/data-protection/configuration/overview> 참조) 또는 세션([세션 및 앱 상태: 세션 상태](xref:fundamentals/app-state#session-state) 참조).<br><br>자세한 내용은 [세션 및 앱 상태: TempData](xref:fundamentals/app-state#tempdata)를 참조하세요. |
| 위조 방지 | 데이터 보호(<xref:security/data-protection/configuration/overview> 참조).<br><br>자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요. |

## <a name="troubleshoot"></a>문제 해결

### <a name="data-protection-and-caching"></a>데이터 보호 및 캐싱

데이터 보호 또는 캐싱이 웹 팜 환경에 맞게 구성되어 있지 않으면 요청을 처리할 때 간헐적인 오류가 발생합니다. 이 오류의 원인은 노드가 동일한 리소스를 공유하지 않고 사용자 요청이 항상 동일한 노드로 다시 라우팅되지는 않기 때문입니다.

cookie 인증을 사용하여 앱에 로그인하는 사용자를 가정해 보겠습니다. 사용자는 하나의 웹 팜 노드에서 앱에 로그인합니다. 다음 요청이 사용자가 로그인한 동일한 노드에 도착하면 앱은 인증 cookie를 암호 해독할 수 있고 앱의 리소스에 액세스할 수 있습니다. 다음 요청이 다른 노드에 도착하면 앱은 사용자가 로그인한 노드의 인증 cookie를 암호 해독할 수 없으며 요청된 리소스에 대한 권한 부여가 실패합니다.

다음 증상 중 하나라도 **간헐적** 으로 발생할 경우 일반적으로 웹 팜 환경에 대한 부적절한 데이터 보호 또는 캐싱 구성 문제로 추적됩니다.

* 인증 중단: 인증 cookie가 잘못 구성되었거나 암호를 해독할 수 없습니다. OAuth(Facebook, Microsoft, Twitter) 또는 OpenIdConnect 로그인이 “상관 관계 실패” 오류로 인해 실패합니다.
* 권한 부여 중단: Identity가 손실되었습니다.
* 세션 상태에서 데이터가 손실됩니다.
* 캐시된 항목이 사라집니다.
* TempData가 실패합니다.
* POST 실패: 위조 방지 검사가 실패합니다.

웹 팜 배포의 데이터 보호 구성에 대한 자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요. 웹 팜 배포의 캐싱 구성에 대한 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.

## <a name="obtain-data-from-apps"></a>앱에서 데이터 얻기

웹 팜 앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다. 자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [Windows용 사용자 지정 스크립트 확장](/azure/virtual-machines/extensions/custom-script-windows): Azure 가상 머신에서 스크립트를 다운로드하여 실행합니다. 이는 배포 후 구성 및 소프트웨어 설치에 유용합니다.
* <xref:host-and-deploy/proxy-load-balancer>
 