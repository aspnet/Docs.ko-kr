---
title: ASP.NET Core 부하/스트레스 테스트
author: Jeremy-Meng
description: ASP.NET Core 앱의 부하 테스트 및 스트레스 테스트를 위한 몇 가지 주요 도구와 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060718"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 부하/스트레스 테스트

부하 테스트와 스트레스 테스트는 웹앱의 성능과 확장성을 보장하는 데 중요합니다. 유사한 테스트를 공유하는 경우가 많지만, 두 테스트의 목표는 서로 다릅니다.

**부하 테스트** : 앱에서 응답 목표를 충족하면서 특정 시나리오에서 지정된 사용자 부하를 처리할 수 있는지를 테스트합니다. 정상 조건으로 앱을 실행합니다.

**스트레스 테스트** : 극단적인 조건에서 앱을 실행할 때의 앱 안정성을 테스트하며, 오랫동안 실행하는 경우가 많습니다. 이 테스트는 앱에서 부하를 급하게 또는 점진적으로 늘려 높은 사용자 부하를 적용하거나, 앱의 컴퓨팅 리소스를 제한합니다.

스트레스 테스트는 스트레스 상태의 앱이 오류를 복구하고 정상적으로 예상 동작으로 돌아올 수 있는지를 확인합니다. 스트레스 상태에서는 정상 조건으로 앱을 실행하지 않습니다.

Visual Studio 2019는 [부하 테스트 사용 중단](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) 계획을 발표했습니다. 해당하는 Azure DevOps 클라우드 기반 부하 테스트 서비스가 중지되었습니다.

## <a name="third-party-tools"></a>타사 도구

다음 목록에는 다양한 기능 집합을 포함하는 타사 웹 성능 도구가 나와 있습니다.

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench](https://httpd.apache.org/docs/2.4/programs/ab.html)(ab)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)