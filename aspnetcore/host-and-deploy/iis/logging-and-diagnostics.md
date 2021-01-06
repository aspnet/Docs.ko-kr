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
# <a name="log-creation-and-redirection"></a>로그 만들기 및 리디렉션

ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다. `stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).

프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다. 로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.

stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.

일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요. ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다. 로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( `.log`)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 `stdout`)에 추가하여 작성됩니다. `stdoutLogFile` 경로가 `stdout`으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 `stdout_20180205194132_1934.log`입니다.

`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다. 시작 후에는 모든 추가 로그가 삭제됩니다.

다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다. AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다. `%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.

로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.

경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.

## <a name="enhanced-diagnostic-logs"></a>개선된 진단 로그

ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다. `web.config`에서 `<handlerSettings>` 요소를 `<aspNetCore>` 요소에 추가합니다. `debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.

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

경로에 있는 모든 폴더(위 예제의 `logs`)가 로그 파일을 만들 때 모듈에서 생성됩니다. 앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\{APP POOL NAME}`를 사용하여 쓰기 권한 제공, 여기서 자리 표시자 `{APP POOL NAME}`는 앱 풀 이름임).

디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.

수준(최소한에서 가장 자세한 정보까지 순서대로 ):

* 오류
* 경고
* 정보
* TRACE

위치(여러 위치가 허용됨):

* CONSOLE
* EVENTLOG
* FILE

처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.

* `ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다. (기본값: `aspnetcore-debug.log`)
* `ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.

> [!WARNING]
> 배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**. 로그의 크기는 제한되지 않습니다. 디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.

`web.config` 파일에 있는 `aspNetCore` 요소의 예제는 [`web.config`를 사용한 ASP.NET Core 모듈 구성](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig)을 참조하세요.
