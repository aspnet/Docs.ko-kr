---
title: 모니터링 및 디버그 - ASP.NET Core 및 Azure에서 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하여 DevOps 솔루션의 일부로 코드 모니터링 및 디버그
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056753"
---
# <a name="monitor-and-debug"></a>모니터링 및 디버그

앱을 배포하고 DevOps 파이프라인을 구축한 후에는 앱을 모니터링하고 문제를 해결하는 방법을 이해하는 것이 중요합니다.

이 섹션에서는 다음 작업을 완료하게 됩니다.

* Azure Portal에서 기본 모니터링 및 문제 해결 데이터 찾기
* Azure Monitor에서 모든 Azure 서비스의 메트릭을 좀 더 자세히 살펴보는 방법 알아보기
* 앱 프로파일링을 위해 웹앱을 Application Insights에 연결
* 로깅을 설정하고 로그를 다운로드할 위치 알아보기
* 실시간으로 로그 스트리밍
* 경고를 설정하는 위치 알아보기
* Azure App Service 웹앱을 원격으로 디버깅하는 방법을 알아봅니다.

## <a name="basic-monitoring-and-troubleshooting"></a>기본 모니터링 및 문제 해결

App Service 웹앱은 실시간으로 쉽게 모니터링할 수 있습니다. Azure Portal은 이해하기 쉬운 차트 및 그래프로 메트릭을 렌더링합니다.

1. [Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.

1. **개요** 탭은 최근 메트릭을 표시하는 그래프를 포함하여 유용한 "한눈에 보기" 정보를 표시합니다.

    ![개요 패널을 보여 주는 스크린샷](./media/monitoring/overview.png)

    * **Http 5xx** : 서버 쪽 오류(일반적으로 ASP.NET Core 코드의 예외)입니다.
    * **데이터 입력** : 웹앱으로의 데이터 수신입니다.
    * **데이터 출력** : 웹앱에서 클라이언트로의 데이터 송신입니다.
    * **요청** : HTTP 요청 수입니다.
    * **평균 응답 시간** : 웹앱이 HTTP 요청에 응답하는 평균 시간입니다.

    이 페이지에는 문제 해결 및 최적화를 위한 여러 셀프 서비스 도구도 있습니다.

    ![셀프 서비스 도구를 보여 주는 스크린샷](./media/monitoring/wizards.png)

    * **문제 진단 및 해결** 은 셀프 서비스 문제 해결사입니다.
    * **Application Insights** 는 프로파일링 성능 및 앱 동작을 위한 것이며, 이 섹션의 뒷부분에서 설명합니다.
    * **App Service Advisor** 는 앱 환경을 조정하기 위한 권장 사항을 만듭니다.

## <a name="advanced-monitoring"></a>고급 모니터링

[Azure Monitor](/azure/monitoring-and-diagnostics/)는 모든 메트릭을 모니터링하고 Azure 서비스에서 경고를 설정하기 위한 중앙 집중식 서비스입니다. Azure Monitor 내에서 관리자는 성능을 세부적으로 추적하고 추세를 식별할 수 있습니다. 각 Azure 서비스는 Azure Monitor에 고유한 [메트릭 세트](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)를 제공합니다.

## <a name="profile-with-application-insights"></a>Application Insights로 프로파일링

[Application Insights](/azure/application-insights/app-insights-overview)는 웹앱의 성능 및 안정성과 사용 방법을 분석하기 위한 Azure 서비스입니다. Application Insights의 데이터는 Azure Monitor의 데이터보다 더 광범위하고 더 자세합니다. 데이터는 개발자와 관리자에게 앱 개선을 위한 주요 정보를 제공할 수 있습니다. 코드를 변경하지 않고 Azure App Service 리소스에 Application Insights를 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.
1. **개요** 탭에서 **Application Insights** 타일을 클릭합니다.

    ![Application Insights 타일](./media/monitoring/app-insights.png)

1. **새 리소스 만들기** 라디오 단추를 선택합니다. 기본 리소스 이름을 사용하고 Application Insights 리소스의 위치를 선택합니다. 이 위치는 웹앱의 위치와 일치하지 않아도 됩니다.

    ![Application Insights 설정](./media/monitoring/new-app-insights.png)

1. **런타임/프레임워크** 로 **ASP.NET Core** 를 선택합니다. 기본 설정을 적용합니다.
1. **확인** 을 선택합니다. 확인하라는 메시지가 표시되면 **계속** 을 선택합니다.
1. 리소스를 만든 후 Application Insights 리소스의 이름을 클릭하여 Application Insights 페이지로 직접 이동합니다.

    ![새 Application Insights 리소스가 준비 완료되었습니다.](./media/monitoring/new-app-insights-done.png)

앱이 사용되면 데이터가 누적됩니다. **새로 고침** 을 선택하여 새 데이터로 블레이드를 다시 로드합니다.

![Application Insights 개요 탭](./media/monitoring/app-insights-overview.png)

Application Insights는 추가 구성 없이 유용한 서버 쪽 정보를 제공합니다. Application Insights를 최대한 활용하려면 [Application Insights SDK를 사용하여 앱을 계측](/azure/application-insights/app-insights-asp-net-core)합니다. 올바르게 구성된 경우 서비스는 웹 서버와 브라우저에서 클라이언트 쪽 성능을 비롯한 종단 간 모니터링을 제공합니다. 자세한 내용은 [Application Insights 설명서](/azure/application-insights/app-insights-overview)를 참조하세요.

## <a name="logging"></a>로깅

웹 서버 및 앱 로그는 Azure App Service에서 기본적으로 사용하지 않도록 설정되어 있습니다. 다음 단계를 수행하여 로그를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.
1. 왼쪽의 메뉴에서 **모니터링** 섹션까지 아래로 스크롤합니다. **진단 로그** 를 선택합니다.

    ![진단 로그 링크](./media/monitoring/logging.png)

1. **애플리케이션 로깅(파일 시스템)** 을 켭니다. 메시지가 표시되면 확장을 설치하여 웹앱에서 앱 로깅을 사용하도록 설정하기 위한 확인란을 클릭합니다.
1. **웹 서버 로깅** 을 **파일 시스템** 으로 설정합니다.
1. **보존 기간** (일)을 입력합니다. 예로 30을 들 수 있습니다.
1. **저장** 을 클릭합니다.

웹앱에 대해 ASP.NET Core 및 웹 서버(App Service) 로그가 생성됩니다. 표시된 FTP/FTPS 정보를 사용하여 다운로드할 수 있습니다. 암호는 이 가이드의 앞부분에서 만든 배포 자격 증명과 동일합니다. 로그는 [PowerShell 또는 Azure CLI를 사용하여 로컬 컴퓨터에 직접 스트리밍](/azure/app-service/web-sites-enable-diagnostic-log#download)할 수 있습니다. 로그를 [Application Insights에서 볼 수도 있습니다](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>스트리밍 로그

앱 및 웹 서버 로그를 포털을 통해 실시간으로 스트리밍할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.
1. 왼쪽의 메뉴에서 **모니터링** 섹션까지 아래로 스크롤한 후 **로그 스트림** 을 선택합니다.

    ![로그 스트림 링크를 보여 주는 스크린샷](./media/monitoring/log-stream.png)

Cloud Shell을 비롯한 [Azure CLI 또는 Azure PowerShell을 통해 로그를 스트리밍할 수도 있습니다](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs).

## <a name="alerts"></a>경고

또한 Azure Monitor는 메트릭, 관리 이벤트 및 기타 기준에 따라 [실시간 경고](/azure/monitoring-and-diagnostics/insights-alerts-portal)를 제공합니다.

> *참고: 현재 웹앱 메트릭에 대한 경고는 경고(클래식) 서비스에서만 사용할 수 있습니다.*

[경고(클래식) 서비스](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)는 Azure Monitor 또는 App Service 설정의 **모니터링** 섹션에서 찾을 수 있습니다.

![경고(클래식) 링크](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>라이브 디버깅

로그가 충분한 정보를 제공하지 않는 경우 Azure App Service는 [Visual Studio를 사용하여 원격으로 디버그](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)할 수 있습니다. 그러나 원격 디버깅을 사용하려면 디버그 기호를 사용하여 앱을 컴파일해야 합니다. 최후의 수단인 경우가 아니면 프로덕션 환경에서는 디버깅을 수행하지 않아야 합니다.

## <a name="conclusion"></a>결론

이 섹션에서는 다음 작업을 완료했습니다.

* Azure Portal에서 기본 모니터링 및 문제 해결 데이터 찾기
* Azure Monitor에서 모든 Azure 서비스의 메트릭을 좀 더 자세히 살펴보는 방법 알아보기
* 앱 프로파일링을 위해 웹앱을 Application Insights에 연결
* 로깅을 설정하고 로그를 다운로드할 위치 알아보기
* 실시간으로 로그 스트리밍
* 경고를 설정하는 위치 알아보기
* Azure App Service 웹앱을 원격으로 디버깅하는 방법을 알아봅니다.

## <a name="additional-reading"></a>추가 참조 항목

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Application Insights를 사용한 Azure 웹앱 성능 모니터링](/azure/application-insights/app-insights-azure-web-apps)
* [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)
* [Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure 서비스에 대한 Azure Monitor에서 클래식 메트릭 경고 만들기 - Azure Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
