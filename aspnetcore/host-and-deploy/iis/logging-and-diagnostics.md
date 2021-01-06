---
title: ASP.NET Core 모듈을 사용하여 로그 만들기 및 리디렉션
author: rick-anderson
description: 로그 및 진단 정보를 캡처하도록 IIS 및 ASP.NET Core 모듈을 구성합니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057442"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="d6c04-103">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="d6c04-103">Log creation and redirection</span></span>

<span data-ttu-id="d6c04-104">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="d6c04-105">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="d6c04-106">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).</span><span class="sxs-lookup"><span data-stu-id="d6c04-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="d6c04-107">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="d6c04-108">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="d6c04-109">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="d6c04-110">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d6c04-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="d6c04-111">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d6c04-112">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6c04-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="d6c04-113">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="d6c04-114">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 `stdout`)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="d6c04-115">`stdoutLogFile` 경로가 `stdout`으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 `stdout_20180205194132_1934.log`입니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="d6c04-116">`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="d6c04-117">시작 후에는 모든 추가 로그가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="d6c04-118">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="d6c04-119">AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="d6c04-120">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="d6c04-121">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="d6c04-122">로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6c04-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="d6c04-123">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6c04-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="d6c04-124">개선된 진단 로그</span><span class="sxs-lookup"><span data-stu-id="d6c04-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="d6c04-125">ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="d6c04-126">`web.config`에서 `<handlerSettings>` 요소를 `<aspNetCore>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="d6c04-127">`debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="d6c04-128">경로에 있는 모든 폴더(위 예제의 `logs`)가 로그 파일을 만들 때 모듈에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="d6c04-129">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).</span><span class="sxs-lookup"><span data-stu-id="d6c04-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="d6c04-130">디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="d6c04-131">수준(최소한에서 가장 자세한 정보까지 순서대로 ):</span><span class="sxs-lookup"><span data-stu-id="d6c04-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="d6c04-132">오류</span><span class="sxs-lookup"><span data-stu-id="d6c04-132">ERROR</span></span>
* <span data-ttu-id="d6c04-133">경고</span><span class="sxs-lookup"><span data-stu-id="d6c04-133">WARNING</span></span>
* <span data-ttu-id="d6c04-134">정보</span><span class="sxs-lookup"><span data-stu-id="d6c04-134">INFO</span></span>
* <span data-ttu-id="d6c04-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="d6c04-135">TRACE</span></span>

<span data-ttu-id="d6c04-136">위치(여러 위치가 허용됨):</span><span class="sxs-lookup"><span data-stu-id="d6c04-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="d6c04-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="d6c04-137">CONSOLE</span></span>
* <span data-ttu-id="d6c04-138">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="d6c04-138">EVENTLOG</span></span>
* <span data-ttu-id="d6c04-139">FILE</span><span class="sxs-lookup"><span data-stu-id="d6c04-139">FILE</span></span>

<span data-ttu-id="d6c04-140">처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="d6c04-141">`ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="d6c04-142">(기본값: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="d6c04-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="d6c04-143">`ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="d6c04-144">배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="d6c04-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="d6c04-145">로그의 크기는 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-145">The size of the log isn't limited.</span></span> <span data-ttu-id="d6c04-146">디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d6c04-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="d6c04-147">`web.config` 파일에 있는 `aspNetCore` 요소의 예제는 [`web.config`를 사용한 ASP.NET Core 모듈 구성](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d6c04-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
