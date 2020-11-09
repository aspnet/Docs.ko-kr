---
title: 모니터링 및 디버그 - ASP.NET Core 및 Azure에서 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용하여 DevOps 솔루션의 일부로 코드 모니터링 및 디버그
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056753"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="5d3b9-103">모니터링 및 디버그</span><span class="sxs-lookup"><span data-stu-id="5d3b9-103">Monitor and debug</span></span>

<span data-ttu-id="5d3b9-104">앱을 배포하고 DevOps 파이프라인을 구축한 후에는 앱을 모니터링하고 문제를 해결하는 방법을 이해하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="5d3b9-105">이 섹션에서는 다음 작업을 완료하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="5d3b9-106">Azure Portal에서 기본 모니터링 및 문제 해결 데이터 찾기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="5d3b9-107">Azure Monitor에서 모든 Azure 서비스의 메트릭을 좀 더 자세히 살펴보는 방법 알아보기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="5d3b9-108">앱 프로파일링을 위해 웹앱을 Application Insights에 연결</span><span class="sxs-lookup"><span data-stu-id="5d3b9-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="5d3b9-109">로깅을 설정하고 로그를 다운로드할 위치 알아보기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="5d3b9-110">실시간으로 로그 스트리밍</span><span class="sxs-lookup"><span data-stu-id="5d3b9-110">Stream logs in real time</span></span>
* <span data-ttu-id="5d3b9-111">경고를 설정하는 위치 알아보기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="5d3b9-112">Azure App Service 웹앱을 원격으로 디버깅하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="5d3b9-113">기본 모니터링 및 문제 해결</span><span class="sxs-lookup"><span data-stu-id="5d3b9-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="5d3b9-114">App Service 웹앱은 실시간으로 쉽게 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="5d3b9-115">Azure Portal은 이해하기 쉬운 차트 및 그래프로 메트릭을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="5d3b9-116">[Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="5d3b9-117">**개요** 탭은 최근 메트릭을 표시하는 그래프를 포함하여 유용한 "한눈에 보기" 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![개요 패널을 보여 주는 스크린샷](./media/monitoring/overview.png)

    * <span data-ttu-id="5d3b9-119">**Http 5xx** : 서버 쪽 오류(일반적으로 ASP.NET Core 코드의 예외)입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-119">**Http 5xx** : Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="5d3b9-120">**데이터 입력** : 웹앱으로의 데이터 수신입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-120">**Data In** : Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="5d3b9-121">**데이터 출력** : 웹앱에서 클라이언트로의 데이터 송신입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-121">**Data Out** : Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="5d3b9-122">**요청** : HTTP 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-122">**Requests** : Count of HTTP requests.</span></span>
    * <span data-ttu-id="5d3b9-123">**평균 응답 시간** : 웹앱이 HTTP 요청에 응답하는 평균 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-123">**Average Response Time** : Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="5d3b9-124">이 페이지에는 문제 해결 및 최적화를 위한 여러 셀프 서비스 도구도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![셀프 서비스 도구를 보여 주는 스크린샷](./media/monitoring/wizards.png)

    * <span data-ttu-id="5d3b9-126">**문제 진단 및 해결** 은 셀프 서비스 문제 해결사입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="5d3b9-127">**Application Insights** 는 프로파일링 성능 및 앱 동작을 위한 것이며, 이 섹션의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="5d3b9-128">**App Service Advisor** 는 앱 환경을 조정하기 위한 권장 사항을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="5d3b9-129">고급 모니터링</span><span class="sxs-lookup"><span data-stu-id="5d3b9-129">Advanced monitoring</span></span>

<span data-ttu-id="5d3b9-130">[Azure Monitor](/azure/monitoring-and-diagnostics/)는 모든 메트릭을 모니터링하고 Azure 서비스에서 경고를 설정하기 위한 중앙 집중식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="5d3b9-131">Azure Monitor 내에서 관리자는 성능을 세부적으로 추적하고 추세를 식별할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="5d3b9-132">각 Azure 서비스는 Azure Monitor에 고유한 [메트릭 세트](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="5d3b9-133">Application Insights로 프로파일링</span><span class="sxs-lookup"><span data-stu-id="5d3b9-133">Profile with Application Insights</span></span>

<span data-ttu-id="5d3b9-134">[Application Insights](/azure/application-insights/app-insights-overview)는 웹앱의 성능 및 안정성과 사용 방법을 분석하기 위한 Azure 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="5d3b9-135">Application Insights의 데이터는 Azure Monitor의 데이터보다 더 광범위하고 더 자세합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="5d3b9-136">데이터는 개발자와 관리자에게 앱 개선을 위한 주요 정보를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="5d3b9-137">코드를 변경하지 않고 Azure App Service 리소스에 Application Insights를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="5d3b9-138">[Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="5d3b9-139">**개요** 탭에서 **Application Insights** 타일을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights 타일](./media/monitoring/app-insights.png)

1. <span data-ttu-id="5d3b9-141">**새 리소스 만들기** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="5d3b9-142">기본 리소스 이름을 사용하고 Application Insights 리소스의 위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="5d3b9-143">이 위치는 웹앱의 위치와 일치하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights 설정](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="5d3b9-145">**런타임/프레임워크** 로 **ASP.NET Core** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-145">For **Runtime/Framework** , select **ASP.NET Core**.</span></span> <span data-ttu-id="5d3b9-146">기본 설정을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-146">Accept the default settings.</span></span>
1. <span data-ttu-id="5d3b9-147">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-147">Select **OK**.</span></span> <span data-ttu-id="5d3b9-148">확인하라는 메시지가 표시되면 **계속** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="5d3b9-149">리소스를 만든 후 Application Insights 리소스의 이름을 클릭하여 Application Insights 페이지로 직접 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![새 Application Insights 리소스가 준비 완료되었습니다.](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="5d3b9-151">앱이 사용되면 데이터가 누적됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="5d3b9-152">**새로 고침** 을 선택하여 새 데이터로 블레이드를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights 개요 탭](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="5d3b9-154">Application Insights는 추가 구성 없이 유용한 서버 쪽 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="5d3b9-155">Application Insights를 최대한 활용하려면 [Application Insights SDK를 사용하여 앱을 계측](/azure/application-insights/app-insights-asp-net-core)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="5d3b9-156">올바르게 구성된 경우 서비스는 웹 서버와 브라우저에서 클라이언트 쪽 성능을 비롯한 종단 간 모니터링을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="5d3b9-157">자세한 내용은 [Application Insights 설명서](/azure/application-insights/app-insights-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="5d3b9-158">로깅</span><span class="sxs-lookup"><span data-stu-id="5d3b9-158">Logging</span></span>

<span data-ttu-id="5d3b9-159">웹 서버 및 앱 로그는 Azure App Service에서 기본적으로 사용하지 않도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="5d3b9-160">다음 단계를 수행하여 로그를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="5d3b9-161">[Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="5d3b9-162">왼쪽의 메뉴에서 **모니터링** 섹션까지 아래로 스크롤합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="5d3b9-163">**진단 로그** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-163">Select **Diagnostics logs**.</span></span>

    ![진단 로그 링크](./media/monitoring/logging.png)

1. <span data-ttu-id="5d3b9-165">**애플리케이션 로깅(파일 시스템)** 을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="5d3b9-166">메시지가 표시되면 확장을 설치하여 웹앱에서 앱 로깅을 사용하도록 설정하기 위한 확인란을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="5d3b9-167">**웹 서버 로깅** 을 **파일 시스템** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="5d3b9-168">**보존 기간** (일)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="5d3b9-169">예로 30을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-169">For example, 30.</span></span>
1. <span data-ttu-id="5d3b9-170">**저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-170">Click **Save**.</span></span>

<span data-ttu-id="5d3b9-171">웹앱에 대해 ASP.NET Core 및 웹 서버(App Service) 로그가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="5d3b9-172">표시된 FTP/FTPS 정보를 사용하여 다운로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="5d3b9-173">암호는 이 가이드의 앞부분에서 만든 배포 자격 증명과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="5d3b9-174">로그는 [PowerShell 또는 Azure CLI를 사용하여 로컬 컴퓨터에 직접 스트리밍](/azure/app-service/web-sites-enable-diagnostic-log#download)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="5d3b9-175">로그를 [Application Insights에서 볼 수도 있습니다](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="5d3b9-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="5d3b9-176">스트리밍 로그</span><span class="sxs-lookup"><span data-stu-id="5d3b9-176">Log streaming</span></span>

<span data-ttu-id="5d3b9-177">앱 및 웹 서버 로그를 포털을 통해 실시간으로 스트리밍할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="5d3b9-178">[Azure Portal](https://portal.azure.com)을 열고 *mywebapp\<unique_number\>* App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="5d3b9-179">왼쪽의 메뉴에서 **모니터링** 섹션까지 아래로 스크롤한 후 **로그 스트림** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![로그 스트림 링크를 보여 주는 스크린샷](./media/monitoring/log-stream.png)

<span data-ttu-id="5d3b9-181">Cloud Shell을 비롯한 [Azure CLI 또는 Azure PowerShell을 통해 로그를 스트리밍할 수도 있습니다](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs).</span><span class="sxs-lookup"><span data-stu-id="5d3b9-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="5d3b9-182">경고</span><span class="sxs-lookup"><span data-stu-id="5d3b9-182">Alerts</span></span>

<span data-ttu-id="5d3b9-183">또한 Azure Monitor는 메트릭, 관리 이벤트 및 기타 기준에 따라 [실시간 경고](/azure/monitoring-and-diagnostics/insights-alerts-portal)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="5d3b9-184">*참고: 현재 웹앱 메트릭에 대한 경고는 경고(클래식) 서비스에서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="5d3b9-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="5d3b9-185">[경고(클래식) 서비스](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)는 Azure Monitor 또는 App Service 설정의 **모니터링** 섹션에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![경고(클래식) 링크](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="5d3b9-187">라이브 디버깅</span><span class="sxs-lookup"><span data-stu-id="5d3b9-187">Live debugging</span></span>

<span data-ttu-id="5d3b9-188">로그가 충분한 정보를 제공하지 않는 경우 Azure App Service는 [Visual Studio를 사용하여 원격으로 디버그](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="5d3b9-189">그러나 원격 디버깅을 사용하려면 디버그 기호를 사용하여 앱을 컴파일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="5d3b9-190">최후의 수단인 경우가 아니면 프로덕션 환경에서는 디버깅을 수행하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="5d3b9-191">결론</span><span class="sxs-lookup"><span data-stu-id="5d3b9-191">Conclusion</span></span>

<span data-ttu-id="5d3b9-192">이 섹션에서는 다음 작업을 완료했습니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="5d3b9-193">Azure Portal에서 기본 모니터링 및 문제 해결 데이터 찾기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="5d3b9-194">Azure Monitor에서 모든 Azure 서비스의 메트릭을 좀 더 자세히 살펴보는 방법 알아보기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="5d3b9-195">앱 프로파일링을 위해 웹앱을 Application Insights에 연결</span><span class="sxs-lookup"><span data-stu-id="5d3b9-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="5d3b9-196">로깅을 설정하고 로그를 다운로드할 위치 알아보기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="5d3b9-197">실시간으로 로그 스트리밍</span><span class="sxs-lookup"><span data-stu-id="5d3b9-197">Stream logs in real time</span></span>
* <span data-ttu-id="5d3b9-198">경고를 설정하는 위치 알아보기</span><span class="sxs-lookup"><span data-stu-id="5d3b9-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="5d3b9-199">Azure App Service 웹앱을 원격으로 디버깅하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="5d3b9-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="5d3b9-200">추가 참조 항목</span><span class="sxs-lookup"><span data-stu-id="5d3b9-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="5d3b9-201">Application Insights를 사용한 Azure 웹앱 성능 모니터링</span><span class="sxs-lookup"><span data-stu-id="5d3b9-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="5d3b9-202">Azure App Service에서 웹앱에 대한 진단 로깅 사용</span><span class="sxs-lookup"><span data-stu-id="5d3b9-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="5d3b9-203">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="5d3b9-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="5d3b9-204">Azure 서비스에 대한 Azure Monitor에서 클래식 메트릭 경고 만들기 - Azure Portal</span><span class="sxs-lookup"><span data-stu-id="5d3b9-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
