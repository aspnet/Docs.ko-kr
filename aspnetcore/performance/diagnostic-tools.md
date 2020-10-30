---
title: 성능 진단 도구
author: mjrousos
description: ASP.NET Core 앱의 성능 문제를 진단 하는 데 유용한 도구입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060861"
---
# <a name="performance-diagnostic-tools"></a>성능 진단 도구

[Mike Rousos](https://github.com/mjrousos) 작성

이 문서에는 ASP.NET Core의 성능 문제를 진단 하기 위한 도구가 나열 되어 있습니다.

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio 진단 도구

Visual Studio에 기본 제공 되는 [프로 파일링 및 진단 도구](/visualstudio/profiling) 는 성능 문제 조사를 시작 하는 데 적합 합니다. 이러한 도구는 Visual Studio 개발 환경에서 강력 하 고 편리 하 게 사용할 수 있습니다. 도구를 사용 하 여 ASP.NET Core 앱에서 CPU 사용량, 메모리 사용량 및 성능 이벤트를 분석할 수 있습니다. 기본 제공 되는 경우 개발 시 쉽게 프로 파일링을 수행 합니다.

자세한 내용은 [Visual Studio 설명서](/visualstudio/profiling/profiling-overview)에서 확인할 수 있습니다.

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) 는 앱에 대 한 심층 성능 데이터를 제공 합니다. Application Insights는 응답 속도, 실패 비율, 종속성 응답 시간 등에 대 한 데이터를 자동으로 수집 합니다. Application Insights는 사용자 지정 이벤트 및 응용 프로그램 관련 메트릭의 로깅을 지원 합니다.

Azure 애플리케이션 Insights는 모니터링 되는 앱에 대 한 정보를 제공 하는 여러 가지 방법을 제공 합니다.

- [응용 프로그램 맵](/azure/application-insights/app-insights-app-map) – 분산 된 앱의 모든 구성 요소에서 성능 병목 현상을 발생 하거나 핫 스폿을 지원 합니다.
- [Azure 메트릭 탐색기](/azure/azure-monitor/platform/metrics-getting-started) 는 차트를 그릴 수 있는 Microsoft Azure portal의 구성 요소 이며, 추세를 시각적으로 상호 연결 하 고 메트릭 값의 스파이크 및 dip를 조사 합니다.
- [Application Insights 포털의 성능 블레이드](/azure/application-insights/app-insights-tutorial-performance):

  - 모니터링 되는 응용 프로그램의 다양 한 작업에 대 한 성능 세부 정보를 표시 합니다.
  - 단일 작업으로 드릴 하 여 긴 기간에 기여 하는 모든 파트/종속성을 확인할 수 있습니다.
  - 여기에서 프로파일러를 호출 하 여 요청 시 성능 추적을 수집할 수 있습니다.

- [Azure 애플리케이션 Insights Profiler](/azure/azure-monitor/app/profiler) 를 사용 하면 .net 앱의 일반 및 주문형 프로 파일링을 수행할 수 있습니다.  Azure Portal는 호출 스택 및 실행 부하 과다 경로를 사용 하 여 캡처된 성능 추적을 보여 줍니다. PerfView를 사용 하 여 심층 분석을 위해 추적 파일을 다운로드할 수도 있습니다.

Application Insights는 다음과 같은 다양 한 환경에서 사용할 수 있습니다.

- Azure에서 작동 하도록 최적화 되었습니다.
- 프로덕션, 개발 및 스테이징에서 작동 합니다.
- [Visual Studio](/azure/application-insights/app-insights-visual-studio) 또는 다른 호스팅 환경에서 로컬로 작동 합니다.

자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.

## <a name="perfview"></a>PerfView

[Perfview](https://github.com/Microsoft/perfview) 는 .net 팀에서 .net 성능 문제를 진단 하는 데 특별히 만든 성능 분석 도구입니다. PerfView를 사용 하면 CPU 사용량, 메모리 및 GC 동작, 성능 이벤트 및 벽 시계 시간을 분석할 수 있습니다.

PerfView 및 [perfview 비디오 자습서](https://channel9.msdn.com/Series/PerfView-Tutorial) 를 시작 하거나 도구 또는 [GitHub](https://github.com/Microsoft/perfview)에서 사용 가능한 사용자 가이드를 참조 하 여 시작 하는 방법에 대해 자세히 알아볼 수 있습니다.

## <a name="windows-performance-toolkit"></a>Windows 성능 도구 키트

Wpt ( [Windows 성능 도구 키트](/windows-hardware/test/wpt/) )는 Windows 성능 레코더 (WPR)와 Windows 성능 분석기 (WPA)의 두 가지 구성 요소로 구성 됩니다. 이 도구는 Windows 운영 체제 및 앱에 대 한 심층 성능 프로필을 생성 합니다. WPT에는 데이터를 시각화 하는 다양 한 방법이 있지만 데이터 수집이 PerfView의 보다 강력 하지 않습니다.

## <a name="perfcollect"></a>PerfCollect

PerfView는 .NET 시나리오에 유용한 성능 분석 도구 이지만 Windows 에서만 실행 되므로 Linux 환경에서 실행 되는 ASP.NET Core 앱에서 추적을 수집 하는 데 사용할 수 없습니다.

[Perfcollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) 는 기본 linux 프로 파일링 도구 ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) 및 [LTTng](https://lttng.org/))를 사용 하 여 perfcollect로 분석할 수 있는 linux에서 추적을 수집 하는 bash 스크립트입니다. PerfCollect는 Perfcollect를 직접 사용할 수 없는 Linux 환경에서 성능 문제가 표시 되는 경우에 유용 합니다. 대신 PerfCollect는 .NET Core 앱에서 추적을 수집한 다음 Perfcollect를 사용 하 여 Windows 컴퓨터에서 분석을 수행할 수 있습니다.

PerfCollect를 설치 하 고 시작 하는 방법에 대 한 자세한 내용은 [GitHub에서](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)제공 됩니다.

## <a name="other-third-party-performance-tools"></a>다른 타사 성능 도구

다음 목록에는 .NET Core 응용 프로그램의 성능 조사에 유용한 일부 타사 성능 도구가 나와 있습니다.

- [Miniprofiler 활용](https://miniprofiler.com/)
- [JetBrains](https://www.jetbrains.com/) 의 [dotTrace](https://www.jetbrains.com/profiler/) 및 [dotMemory](https://www.jetbrains.com/dotmemory/)
- Intel의 [VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)
