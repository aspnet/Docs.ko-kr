---
title: 성능 진단 도구
author: mjrousos
description: ASP.NET Core 앱의 성능 문제를 진단 하는 데 유용한 도구입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060861"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="301ad-103">성능 진단 도구</span><span class="sxs-lookup"><span data-stu-id="301ad-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="301ad-104">[Mike Rousos](https://github.com/mjrousos) 작성</span><span class="sxs-lookup"><span data-stu-id="301ad-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="301ad-105">이 문서에는 ASP.NET Core의 성능 문제를 진단 하기 위한 도구가 나열 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="301ad-106">Visual Studio 진단 도구</span><span class="sxs-lookup"><span data-stu-id="301ad-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="301ad-107">Visual Studio에 기본 제공 되는 [프로 파일링 및 진단 도구](/visualstudio/profiling) 는 성능 문제 조사를 시작 하는 데 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="301ad-108">이러한 도구는 Visual Studio 개발 환경에서 강력 하 고 편리 하 게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="301ad-109">도구를 사용 하 여 ASP.NET Core 앱에서 CPU 사용량, 메모리 사용량 및 성능 이벤트를 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="301ad-110">기본 제공 되는 경우 개발 시 쉽게 프로 파일링을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="301ad-111">자세한 내용은 [Visual Studio 설명서](/visualstudio/profiling/profiling-overview)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="301ad-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="301ad-112">Application Insights</span></span>

<span data-ttu-id="301ad-113">[Application Insights](/azure/application-insights/app-insights-overview) 는 앱에 대 한 심층 성능 데이터를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="301ad-114">Application Insights는 응답 속도, 실패 비율, 종속성 응답 시간 등에 대 한 데이터를 자동으로 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="301ad-115">Application Insights는 사용자 지정 이벤트 및 응용 프로그램 관련 메트릭의 로깅을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="301ad-116">Azure 애플리케이션 Insights는 모니터링 되는 앱에 대 한 정보를 제공 하는 여러 가지 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="301ad-117">[응용 프로그램 맵](/azure/application-insights/app-insights-app-map) – 분산 된 앱의 모든 구성 요소에서 성능 병목 현상을 발생 하거나 핫 스폿을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="301ad-118">[Azure 메트릭 탐색기](/azure/azure-monitor/platform/metrics-getting-started) 는 차트를 그릴 수 있는 Microsoft Azure portal의 구성 요소 이며, 추세를 시각적으로 상호 연결 하 고 메트릭 값의 스파이크 및 dip를 조사 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="301ad-119">[Application Insights 포털의 성능 블레이드](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="301ad-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="301ad-120">모니터링 되는 응용 프로그램의 다양 한 작업에 대 한 성능 세부 정보를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="301ad-121">단일 작업으로 드릴 하 여 긴 기간에 기여 하는 모든 파트/종속성을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="301ad-122">여기에서 프로파일러를 호출 하 여 요청 시 성능 추적을 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="301ad-123">[Azure 애플리케이션 Insights Profiler](/azure/azure-monitor/app/profiler) 를 사용 하면 .net 앱의 일반 및 주문형 프로 파일링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="301ad-124">Azure Portal는 호출 스택 및 실행 부하 과다 경로를 사용 하 여 캡처된 성능 추적을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="301ad-125">PerfView를 사용 하 여 심층 분석을 위해 추적 파일을 다운로드할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="301ad-126">Application Insights는 다음과 같은 다양 한 환경에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="301ad-127">Azure에서 작동 하도록 최적화 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="301ad-128">프로덕션, 개발 및 스테이징에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="301ad-129">[Visual Studio](/azure/application-insights/app-insights-visual-studio) 또는 다른 호스팅 환경에서 로컬로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="301ad-130">자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="301ad-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="301ad-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="301ad-131">PerfView</span></span>

<span data-ttu-id="301ad-132">[Perfview](https://github.com/Microsoft/perfview) 는 .net 팀에서 .net 성능 문제를 진단 하는 데 특별히 만든 성능 분석 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="301ad-133">PerfView를 사용 하면 CPU 사용량, 메모리 및 GC 동작, 성능 이벤트 및 벽 시계 시간을 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="301ad-134">PerfView 및 [perfview 비디오 자습서](https://channel9.msdn.com/Series/PerfView-Tutorial) 를 시작 하거나 도구 또는 [GitHub](https://github.com/Microsoft/perfview)에서 사용 가능한 사용자 가이드를 참조 하 여 시작 하는 방법에 대해 자세히 알아볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="301ad-135">Windows 성능 도구 키트</span><span class="sxs-lookup"><span data-stu-id="301ad-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="301ad-136">Wpt ( [Windows 성능 도구 키트](/windows-hardware/test/wpt/) )는 Windows 성능 레코더 (WPR)와 Windows 성능 분석기 (WPA)의 두 가지 구성 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="301ad-137">이 도구는 Windows 운영 체제 및 앱에 대 한 심층 성능 프로필을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="301ad-138">WPT에는 데이터를 시각화 하는 다양 한 방법이 있지만 데이터 수집이 PerfView의 보다 강력 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="301ad-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="301ad-139">PerfCollect</span></span>

<span data-ttu-id="301ad-140">PerfView는 .NET 시나리오에 유용한 성능 분석 도구 이지만 Windows 에서만 실행 되므로 Linux 환경에서 실행 되는 ASP.NET Core 앱에서 추적을 수집 하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="301ad-141">[Perfcollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) 는 기본 linux 프로 파일링 도구 ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) 및 [LTTng](https://lttng.org/))를 사용 하 여 perfcollect로 분석할 수 있는 linux에서 추적을 수집 하는 bash 스크립트입니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="301ad-142">PerfCollect는 Perfcollect를 직접 사용할 수 없는 Linux 환경에서 성능 문제가 표시 되는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="301ad-143">대신 PerfCollect는 .NET Core 앱에서 추적을 수집한 다음 Perfcollect를 사용 하 여 Windows 컴퓨터에서 분석을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="301ad-144">PerfCollect를 설치 하 고 시작 하는 방법에 대 한 자세한 내용은 [GitHub에서](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="301ad-145">다른 타사 성능 도구</span><span class="sxs-lookup"><span data-stu-id="301ad-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="301ad-146">다음 목록에는 .NET Core 응용 프로그램의 성능 조사에 유용한 일부 타사 성능 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="301ad-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="301ad-147">Miniprofiler 활용</span><span class="sxs-lookup"><span data-stu-id="301ad-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="301ad-148">[JetBrains](https://www.jetbrains.com/) 의 [dotTrace](https://www.jetbrains.com/profiler/) 및 [dotMemory](https://www.jetbrains.com/dotmemory/)</span><span class="sxs-lookup"><span data-stu-id="301ad-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="301ad-149">Intel의 [VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)</span><span class="sxs-lookup"><span data-stu-id="301ad-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
