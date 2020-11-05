---
title: Azure App Service 및 IIS에서 ASP.NET Core 문제 해결
author: rick-anderson
description: ASP.NET Core 앱의 Azure App Service 및 IIS(인터넷 정보 서비스) 배포에 대한 문제 진단 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: d51a4a43f585b0a0b7e3aab2c5de1b2d215de494
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059600"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Azure App Service 및 IIS에서 ASP.NET Core 문제 해결

작성자 [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.

[앱 시작 오류](#app-startup-errors)  
일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.

[Azure App Service 문제 해결](#troubleshoot-on-azure-app-service)  
Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.

[IIS에 대한 문제 해결](#troubleshoot-on-iis)  
IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다. 이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.

[패키지 캐시 지우기](#clear-package-caches)  
주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.

[추가 리소스](#additional-resources)  
추가 문제 해결 항목을 나열합니다.

## <a name="app-startup-errors"></a>앱 시작 오류

Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다. 로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패* 는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.

### <a name="40314-forbidden"></a>403.14 사용할 수 없음

앱이 시작되지 않았습니다. 다음 오류가 로깅됩니다.

```
The Web server is configured to not list the contents of this directory.
```

이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.

* 앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.
* 배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.
* *web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.

다음 단계를 수행합니다.

1. 호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.
1. Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.
   * *web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.
   * Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.
   * IIS에서 앱을 호스트하는 경우 **IIS 관리자** 의 **기본 설정** 에 표시되는 IIS **실제 경로** 에 앱이 배포되었는지 확인합니다.
1. 호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.

게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요. *web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.

### <a name="500-internal-server-error"></a>500 내부 서버 오류

앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.

이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다. 응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. 애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다. 서버의 관점에서 보면 맞습니다. 앱이 시작되었지만 유효한 응답을 생성할 수 없습니다. 서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.

### <a name="5000-in-process-handler-load-failure"></a>500.0 In-Process 처리기 로드 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 구성 요소를 로드하는 중 알 수 없는 오류가 발생했습니다. 다음 작업 중 하나를 수행합니다.

* [Microsoft 지원](https://support.microsoft.com/oas/default.aspx?prid=15832)에 문의하세요( **개발자 도구** 를 선택한 다음, **ASP.NET Core** 선택).
* Stack Overflow에 대해 질문하세요.
* [GitHub 리포지토리](https://github.com/dotnet/AspNetCore)에 문제를 제기하세요.

### <a name="50030-in-process-startup-failure"></a>500.30 In-Process 시작 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 .NET Core CLR in-process를 시작하려고 하지만 시작할 수 없습니다. 프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.

일반적인 오류 조건:

* 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었습니다. 대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.
* Azure Key Vault를 사용할 경우 Key Vault에 대한 사용 권한이 부족합니다. 대상 Key Vault의 액세스 정책을 확인하여 올바른 사용 권한이 부여되었는지 확인합니다.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 ANCM 네이티브 종속성을 찾지 못함

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다. 이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다. 앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다. 예제 오류 메시지는 다음과 같습니다.

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다. 이 오류를 해결하려면 다음 중 하나를 수행합니다.

* 머신에 적절한 버전의 .NET Core를 설치합니다.
* 머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.
* [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.

개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다. 프로세스 시작 실패의 원인은 애플리케이션 이벤트 로그에도 있습니다.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM dll을 로드하지 못함

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다. 작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.

이 오류를 해결하려면 다음 중 하나를 수행합니다.

* 작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.
* 앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 ANCM 요청 처리기 로드 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다. `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는 앱만 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)에서 호스트할 수 있습니다.

이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다. `.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음

작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.

이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션

작업자 프로세스는 동일한 프로세스에서 여러 in-process 앱을 실행할 수 없습니다.

이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 ANCM Out-Of-Process 처리기 로드 실패

Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll* 이 *aspnetcorev2.dll* 파일 옆에 없습니다. 이는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 손상된 설치를 나타냅니다.

이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 ANCM 시작 시간 제한 내에 시작하지 못함

제공된 시작 시간 제한 내에 ANCM을 시작하지 못했습니다. 기본적으로 제한 시간은 120초입니다.

이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다. 시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다. 여러 앱의 시작 프로세스를 분산해야 합니다.

### <a name="50038-ancm-application-dll-not-found"></a>500.38 ANCM 애플리케이션 DLL을 찾을 수 없음

ANCM에서 실행 파일 옆에 있어야 하는 애플리케이션 DLL을 찾지 못했습니다.

이 오류는 [단일 파일 실행 파일](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)로 패키지된 앱을 In-process 호스팅 모델을 사용하여 호스트할 때 발생합니다. In-process 모델을 사용하려면 ANCM에서 .NET Core 앱을 기존 IIS 프로세스로 로드해야 합니다. 단일 파일 배포 모델에서는 이 시나리오가 지원되지 않습니다. 앱의 프로젝트 파일에서 다음 방법 중 **하나** 를 사용하여 이 오류를 해결합니다.

1. `PublishSingleFile` MSBuild 속성을 `false`로 설정하여 단일 파일 게시를 사용하지 않도록 설정합니다.
1. `AspNetCoreHostingModel` MSBuild 속성을 `OutOfProcess`로 설정하여 out-of-process 호스팅 모델로 전환합니다.

### <a name="5025-process-failure"></a>502.5 프로세스 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다. 프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.

일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다. 대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다. 공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다. 메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다. 자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.

호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

앱의 어셈블리( *.dll* )를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.

게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.

앱 풀의 32비트 설정이 올바른지 확인합니다.

1. IIS 관리자의 **애플리케이션 풀** 에서 앱 풀을 선택합니다.
1. **작업** 패널의 **애플리케이션 풀 편집** 에서 **고급 설정** 을 선택합니다.
1. **32비트 애플리케이션 사용** 을 설정합니다.
   * 32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.
   * 64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.

프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.

### <a name="connection-reset"></a>연결 다시 설정

헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류** 를 전송하는 것은 너무 늦은 것입니다. 응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다. 이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. [애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.

### <a name="default-startup-limits"></a>기본 시작 제한

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit* 가 120초로 구성됩니다. 기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다. 모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.

## <a name="troubleshoot-on-azure-app-service"></a>Azure App Service 문제 해결

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>애플리케이션 이벤트 로그(Azure App Service)

애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.

1. Azure Portal에서 **Azure App Services** 의 앱을 엽니다.
1. **문제 진단 및 해결** 을 선택합니다.
1. **진단 도구** 제목을 선택합니다.
1. **지원 도구** 에서 **애플리케이션 이벤트** 단추를 선택합니다.
1. **원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.

**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.

1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **LogFiles** 폴더를 엽니다.
1. *eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.
1. 로그를 검토합니다. 로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.

### <a name="run-the-app-in-the-kudu-console"></a>Kudu 콘솔에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. [Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.

1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.

#### <a name="test-a-32-bit-x86-app"></a>32비트(x86) 앱 테스트

**현재 릴리스**

1. `cd d:\home\site\wwwroot`
1. 앱을 실행합니다.
   * 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

     ```console
     {ASSEMBLY NAME}.exe
     ```

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**

ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)
1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

#### <a name="test-a-64-bit-x64-app"></a>64비트(x64) 앱 테스트

**현재 릴리스**

* 앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:
  1. `cd D:\Program Files\dotnet`
  1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:
  1. `cd D:\home\site\wwwroot`
  1. 앱 실행: `{ASSEMBLY NAME}.exe`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**

ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)
1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Core 모듈 stdout 로그(Azure App Service)

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다. 앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다. stdout 로그를 사용하고 보려면:

1. Azure Portal에서 웹앱으로 이동합니다.
1. **App Service** 블레이드에서 검색 상자에 **kudu** 를 입력합니다.
1. **고급 도구** > **이동** 을 선택합니다.
1. **디버그 콘솔 > CMD** 를 선택합니다.
1. *site/wwwroot* 로 이동합니다.
1. 연필 아이콘을 선택하여 *web.config* 파일을 편집합니다.
1. `<aspNetCore />` 요소에서 `stdoutLogEnabled="true"`를 설정하고 **저장** 를 선택합니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 `stdoutLogEnabled="false"`를 설정합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>ASP.NET Core 모듈 디버그 로그(Azure App Service)

ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다. stdout 로그를 사용하고 보려면:

1. 개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.
   * [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다. 앱을 다시 배포합니다.
   * Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.
     1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
     1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
     1. 폴더를 **site** > **wwwroot** 경로로 엽니다. 연필 단추를 선택하여 *web.config* 파일을 편집합니다. [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다. **저장** 단추를 선택합니다.
1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. 폴더를 **site** > **wwwroot** 경로로 엽니다. *aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다. 경로를 제공한 경우 로그 파일의 위치로 이동합니다.
1. 파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.

문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.

향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.

* *web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.
* Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다. 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.

> [!WARNING]
> 디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기에는 제한이 없습니다. 앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="slow-or-hanging-app-azure-app-service"></a>느리거나 중단된 앱(Azure App Service)

요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.

* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>모니터링 블레이드

모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다. 이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.

ASP.NET Core 확장이 설치되어 있는지 확인합니다. 확장이 설치되어 있지 않으면 수동으로 설치합니다.

1. **개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.
1. **ASP.NET Core 확장** 이 목록에 표시됩니다.
1. 확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.
1. 목록에서 **ASP.NET Core 확장** 을 선택합니다.
1. **확인** 을 선택하여 적합한 조건을 적용합니다.
1. **확장 추가** 블레이드에서 **확인** 을 선택합니다.
1. 정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.

stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.

1. Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.

계속해서 진단 로깅을 활성화합니다.

1. Azure Portal에서 **진단 로그** 블레이드를 선택합니다.
1. **애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지** 에 대해 **켜기** 스위치를 선택합니다. 블레이드 위쪽에 있는 **저장** 단추를 선택합니다.
1. FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적** 에 대해 **켜기** 스위치를 선택합니다.
1. 포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.
1. 앱에 대한 요청을 실행합니다.
1. 로그 스트림 데이터 내에 오류의 원인이 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.

실패한 요청 추적 로그(FREB 로그)를 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. 사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그** 를 선택합니다.

자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.

자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

## <a name="troubleshoot-on-iis"></a>IIS에 대한 문제 해결

### <a name="application-event-log-iis"></a>애플리케이션 이벤트 로그(IIS)

애플리케이션 이벤트 로그에 액세스합니다.

1. 시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.
1. **이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.
1. **애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.
1. 실패한 앱과 연결된 오류를 검색합니다. 오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.

### <a name="run-the-app-at-a-command-prompt"></a>명령 프롬프트에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. 호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.

#### <a name="framework-dependent-deployment"></a>프레임워크 종속 배포

앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe* 로 앱의 어셈블리를 실행하여 앱을 실행합니다. `dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

#### <a name="self-contained-deployment"></a>자체 포함 배포

앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다. `<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Core 모듈 stdout 로그(IIS)

stdout 로그를 사용하고 보려면:

1. 호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.
1. *logs* 폴더가 없으면 폴더를 만듭니다. MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.
1. *web.config* 파일을 편집합니다. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`). 경로의 `stdout`은 로그 파일 이름 접두사입니다. 로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다. `stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log* 로 지정됩니다.
1. 애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.
1. 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. *logs* 폴더로 이동합니다. 가장 최근의 stdout 로그를 찾아서 엽니다.
1. 오류에 대한 로그를 검토합니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. *web.config* 파일을 편집합니다.
1. **stdoutLogEnabled** 를 `false`로 설정합니다.
1. 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET Core 모듈 디버그 로그(IIS)

앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용하도록 설정합니다.

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.

### <a name="enable-the-developer-exception-page"></a>개발자 예외 페이지 사용

`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다. 앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다. 문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다. *web.config* 에서 환경 변수를 설정하는 방법에 대한 자세한 내용은 [aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.

### <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다. 자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.

### <a name="slow-or-hanging-app-iis"></a>앱이 느리거나 중단됨(IIS)

*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.

#### <a name="app-crashes-or-encounters-an-exception"></a>앱 충돌 또는 예외 발생

[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.

1. `c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다. 앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.
1. [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:
   * 앱에서 [ 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. 충돌이 발생하는 조건에서 앱을 실행합니다.
1. 충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:
   * 앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:

     ```console
     .\DisableDumps dotnet.exe
     ```

앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다. PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.

> [!WARNING]
> 크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>앱 중단 시작 중에 실패 또는 정상적으로 실행

앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.

#### <a name="analyze-the-dump"></a>덤프 분석

덤프는 여러 방법을 사용하여 분석할 수 있습니다. 자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.

## <a name="clear-package-caches"></a>패키지 캐시 지우기

개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다. 경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다. 이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.

1. *bin* 및 *obj* 폴더를 삭제합니다.
1. 명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.

   [nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다. *nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.

1. 프로젝트를 복원하고 다시 빌드합니다.
1. 앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure 설명서

* [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics)
* [방법: Azure App Service에서 앱 모니터링](/azure/app-service/web-sites-monitor)
* [Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure의 웹앱에 대한 애플리케이션 성능 FAQ](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))
* [Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio 설명서

* [Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio를 사용하여 디버깅하는 자세한 내용](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code 설명서

* [Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.

[앱 시작 오류](#app-startup-errors)  
일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.

[Azure App Service 문제 해결](#troubleshoot-on-azure-app-service)  
Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.

[IIS에 대한 문제 해결](#troubleshoot-on-iis)  
IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다. 이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.

[패키지 캐시 지우기](#clear-package-caches)  
주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.

[추가 리소스](#additional-resources)  
추가 문제 해결 항목을 나열합니다.

## <a name="app-startup-errors"></a>앱 시작 오류

Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다. 로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패* 는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.

### <a name="40314-forbidden"></a>403.14 사용할 수 없음

앱이 시작되지 않았습니다. 다음 오류가 로깅됩니다.

```
The Web server is configured to not list the contents of this directory.
```

이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.

* 앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.
* 배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.
* *web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.

다음 단계를 수행합니다.

1. 호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.
1. Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.
   * *web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.
   * Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.
   * IIS에서 앱을 호스트하는 경우 **IIS 관리자** 의 **기본 설정** 에 표시되는 IIS **실제 경로** 에 앱이 배포되었는지 확인합니다.
1. 호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.

게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요. *web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.

### <a name="500-internal-server-error"></a>500 내부 서버 오류

앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.

이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다. 응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. 애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다. 서버의 관점에서 보면 맞습니다. 앱이 시작되었지만 유효한 응답을 생성할 수 없습니다. 서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.

### <a name="5000-in-process-handler-load-failure"></a>500.0 In-Process 처리기 로드 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 .NET Core CLR을 찾지 못하고 in-process 요청 처리기( *aspnetcorev2_inprocess.dll* )를 찾지 못했습니다. 다음을 확인합니다.

* 앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.
* 앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Out-Of-Process 처리기 로드 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 out-of-process 호스팅 요청 처리기를 찾지 못했습니다. *aspnetcorev2_outofprocess.dll* 이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.

### <a name="5025-process-failure"></a>502.5 프로세스 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다. 프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.

일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다. 대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다. 공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다. 메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다. 자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.

호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

앱의 어셈블리( *.dll* )를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.

게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.

앱 풀의 32비트 설정이 올바른지 확인합니다.

1. IIS 관리자의 **애플리케이션 풀** 에서 앱 풀을 선택합니다.
1. **작업** 패널의 **애플리케이션 풀 편집** 에서 **고급 설정** 을 선택합니다.
1. **32비트 애플리케이션 사용** 을 설정합니다.
   * 32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.
   * 64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.

프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.

### <a name="connection-reset"></a>연결 다시 설정

헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류** 를 전송하는 것은 너무 늦은 것입니다. 응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다. 이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. [애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.

### <a name="default-startup-limits"></a>기본 시작 제한

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit* 가 120초로 구성됩니다. 기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다. 모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.

## <a name="troubleshoot-on-azure-app-service"></a>Azure App Service 문제 해결

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>애플리케이션 이벤트 로그(Azure App Service)

애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.

1. Azure Portal에서 **Azure App Services** 의 앱을 엽니다.
1. **문제 진단 및 해결** 을 선택합니다.
1. **진단 도구** 제목을 선택합니다.
1. **지원 도구** 에서 **애플리케이션 이벤트** 단추를 선택합니다.
1. **원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.

**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.

1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **LogFiles** 폴더를 엽니다.
1. *eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.
1. 로그를 검토합니다. 로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.

### <a name="run-the-app-in-the-kudu-console"></a>Kudu 콘솔에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. [Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.

1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.

#### <a name="test-a-32-bit-x86-app"></a>32비트(x86) 앱 테스트

**현재 릴리스**

1. `cd d:\home\site\wwwroot`
1. 앱을 실행합니다.
   * 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

     ```console
     {ASSEMBLY NAME}.exe
     ```

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**

ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)
1. 앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.

#### <a name="test-a-64-bit-x64-app"></a>64비트(x64) 앱 테스트

**현재 릴리스**

* 앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:
  1. `cd D:\Program Files\dotnet`
  1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:
  1. `cd D:\home\site\wwwroot`
  1. 앱을 실행합니다. `{ASSEMBLY NAME}.exe`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.

**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**

ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)
1. 앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Core 모듈 stdout 로그(Azure App Service)

ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다. stdout 로그를 사용하고 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. **문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.
1. **추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.
1. Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 폴더를 엽니다. 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. Azure Portal로 돌아갑니다. **개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **LogFiles** 폴더를 선택합니다.
1. **수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.
1. 로그 파일이 열리면 오류가 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다. 연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.
1. **stdoutLogEnabled** 를 `false`로 설정합니다.
1. **저장** 을 선택하여 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다. 앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>ASP.NET Core 모듈 디버그 로그(Azure App Service)

ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다. stdout 로그를 사용하고 보려면:

1. 개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.
   * [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다. 앱을 다시 배포합니다.
   * Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.
     1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
     1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
     1. 폴더를 **site** > **wwwroot** 경로로 엽니다. 연필 단추를 선택하여 *web.config* 파일을 편집합니다. [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다. **저장** 단추를 선택합니다.
1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. 폴더를 **site** > **wwwroot** 경로로 엽니다. *aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다. 경로를 제공한 경우 로그 파일의 위치로 이동합니다.
1. 파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.

문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.

향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.

* *web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.
* Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다. 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.

> [!WARNING]
> 디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기에는 제한이 없습니다. 앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="slow-or-hanging-app-azure-app-service"></a>느리거나 중단된 앱(Azure App Service)

요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.

* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>모니터링 블레이드

모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다. 이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.

ASP.NET Core 확장이 설치되어 있는지 확인합니다. 확장이 설치되어 있지 않으면 수동으로 설치합니다.

1. **개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.
1. **ASP.NET Core 확장** 이 목록에 표시됩니다.
1. 확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.
1. 목록에서 **ASP.NET Core 확장** 을 선택합니다.
1. **확인** 을 선택하여 적합한 조건을 적용합니다.
1. **확장 추가** 블레이드에서 **확인** 을 선택합니다.
1. 정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.

stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.

1. Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.

계속해서 진단 로깅을 활성화합니다.

1. Azure Portal에서 **진단 로그** 블레이드를 선택합니다.
1. **애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지** 에 대해 **켜기** 스위치를 선택합니다. 블레이드 위쪽에 있는 **저장** 단추를 선택합니다.
1. FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적** 에 대해 **켜기** 스위치를 선택합니다.
1. 포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.
1. 앱에 대한 요청을 실행합니다.
1. 로그 스트림 데이터 내에 오류의 원인이 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.

실패한 요청 추적 로그(FREB 로그)를 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. 사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그** 를 선택합니다.

자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.

자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

## <a name="troubleshoot-on-iis"></a>IIS에 대한 문제 해결

### <a name="application-event-log-iis"></a>애플리케이션 이벤트 로그(IIS)

애플리케이션 이벤트 로그에 액세스합니다.

1. 시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.
1. **이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.
1. **애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.
1. 실패한 앱과 연결된 오류를 검색합니다. 오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.

### <a name="run-the-app-at-a-command-prompt"></a>명령 프롬프트에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. 호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.

#### <a name="framework-dependent-deployment"></a>프레임워크 종속 배포

앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe* 로 앱의 어셈블리를 실행하여 앱을 실행합니다. `dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

#### <a name="self-contained-deployment"></a>자체 포함 배포

앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다. `<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Core 모듈 stdout 로그(IIS)

stdout 로그를 사용하고 보려면:

1. 호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.
1. *logs* 폴더가 없으면 폴더를 만듭니다. MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.
1. *web.config* 파일을 편집합니다. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`). 경로의 `stdout`은 로그 파일 이름 접두사입니다. 로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다. `stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log* 로 지정됩니다.
1. 애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.
1. 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. *logs* 폴더로 이동합니다. 가장 최근의 stdout 로그를 찾아서 엽니다.
1. 오류에 대한 로그를 검토합니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. *web.config* 파일을 편집합니다.
1. **stdoutLogEnabled** 를 `false`로 설정합니다.
1. 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET Core 모듈 디버그 로그(IIS)

앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용하도록 설정합니다.

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.

### <a name="enable-the-developer-exception-page"></a>개발자 예외 페이지 사용

`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다. 앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다. 문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다. *web.config* 에서 환경 변수를 설정하는 방법에 대한 자세한 내용은 [aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.

### <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다. 자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.

### <a name="slow-or-hanging-app-iis"></a>앱이 느리거나 중단됨(IIS)

*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.

#### <a name="app-crashes-or-encounters-an-exception"></a>앱 충돌 또는 예외 발생

[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.

1. `c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다. 앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.
1. [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:
   * 앱에서 [ 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. 충돌이 발생하는 조건에서 앱을 실행합니다.
1. 충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:
   * 앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:

     ```console
     .\DisableDumps dotnet.exe
     ```

앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다. PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.

> [!WARNING]
> 크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>앱 중단 시작 중에 실패 또는 정상적으로 실행

앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.

#### <a name="analyze-the-dump"></a>덤프 분석

덤프는 여러 방법을 사용하여 분석할 수 있습니다. 자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.

## <a name="clear-package-caches"></a>패키지 캐시 지우기

개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다. 경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다. 이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.

1. *bin* 및 *obj* 폴더를 삭제합니다.
1. 명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.

   [nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다. *nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.

1. 프로젝트를 복원하고 다시 빌드합니다.
1. 앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure 설명서

* [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics)
* [방법: Azure App Service에서 앱 모니터링](/azure/app-service/web-sites-monitor)
* [Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure의 웹앱에 대한 애플리케이션 성능 FAQ](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))
* [Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio 설명서

* [Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio를 사용하여 디버깅하는 자세한 내용](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code 설명서

* [Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.

[앱 시작 오류](#app-startup-errors)  
일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.

[Azure App Service 문제 해결](#troubleshoot-on-azure-app-service)  
Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.

[IIS에 대한 문제 해결](#troubleshoot-on-iis)  
IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다. 이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.

[패키지 캐시 지우기](#clear-package-caches)  
주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.

[추가 리소스](#additional-resources)  
추가 문제 해결 항목을 나열합니다.

## <a name="app-startup-errors"></a>앱 시작 오류

Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다. 로컬에서 디버그할 때 발생하는 *502.5 프로세스 실패* 는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.

### <a name="40314-forbidden"></a>403.14 사용할 수 없음

앱이 시작되지 않았습니다. 다음 오류가 로깅됩니다.

```
The Web server is configured to not list the contents of this directory.
```

이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.

* 앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.
* 배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.
* *web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.

다음 단계를 수행합니다.

1. 호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.
1. Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.
   * *web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.
   * Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.
   * IIS에서 앱을 호스트하는 경우 **IIS 관리자** 의 **기본 설정** 에 표시되는 IIS **실제 경로** 에 앱이 배포되었는지 확인합니다.
1. 호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.

게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요. *web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.

### <a name="500-internal-server-error"></a>500 내부 서버 오류

앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.

이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다. 응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. 애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다. 서버의 관점에서 보면 맞습니다. 앱이 시작되었지만 유효한 응답을 생성할 수 없습니다. 서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.

### <a name="5025-process-failure"></a>502.5 프로세스 실패

작업자 프로세스가 실패합니다. 앱이 시작되지 않습니다.

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다. 프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.

일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다. 대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다. 공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다. 메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다. 자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.

호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

앱의 어셈블리( *.dll* )를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.

게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.

앱 풀의 32비트 설정이 올바른지 확인합니다.

1. IIS 관리자의 **애플리케이션 풀** 에서 앱 풀을 선택합니다.
1. **작업** 패널의 **애플리케이션 풀 편집** 에서 **고급 설정** 을 선택합니다.
1. **32비트 애플리케이션 사용** 을 설정합니다.
   * 32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.
   * 64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.

프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.

### <a name="connection-reset"></a>연결 다시 설정

헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류** 를 전송하는 것은 너무 늦은 것입니다. 응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다. 이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. [애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.

### <a name="default-startup-limits"></a>기본 시작 제한

[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit* 가 120초로 구성됩니다. 기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다. 모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.

## <a name="troubleshoot-on-azure-app-service"></a>Azure App Service 문제 해결

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>애플리케이션 이벤트 로그(Azure App Service)

애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.

1. Azure Portal에서 **Azure App Services** 의 앱을 엽니다.
1. **문제 진단 및 해결** 을 선택합니다.
1. **진단 도구** 제목을 선택합니다.
1. **지원 도구** 에서 **애플리케이션 이벤트** 단추를 선택합니다.
1. **원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.

**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.

1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **LogFiles** 폴더를 엽니다.
1. *eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.
1. 로그를 검토합니다. 로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.

### <a name="run-the-app-in-the-kudu-console"></a>Kudu 콘솔에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. [Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.

1. **개발 도구** 영역에서 **고급 도구** 를 엽니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.

#### <a name="test-a-32-bit-x86-app"></a>32비트(x86) 앱 테스트

**현재 릴리스**

1. `cd d:\home\site\wwwroot`
1. 앱을 실행합니다.
   * 앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

     ```console
     {ASSEMBLY NAME}.exe
     ```

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**

ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)
1. 앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.

#### <a name="test-a-64-bit-x64-app"></a>64비트(x64) 앱 테스트

**현재 릴리스**

* 앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:
  1. `cd D:\Program Files\dotnet`
  1. 앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* 앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:
  1. `cd D:\home\site\wwwroot`
  1. 앱을 실행합니다. `{ASSEMBLY NAME}.exe`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.

**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**

ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)
1. 앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Core 모듈 stdout 로그(Azure App Service)

ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다. stdout 로그를 사용하고 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. **문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.
1. **추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.
1. Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 폴더를 엽니다. 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. Azure Portal로 돌아갑니다. **개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **LogFiles** 폴더를 선택합니다.
1. **수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.
1. 로그 파일이 열리면 오류가 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다. 연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.
1. **stdoutLogEnabled** 를 `false`로 설정합니다.
1. **저장** 을 선택하여 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다. 앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.
>
> 시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="slow-or-hanging-app-azure-app-service"></a>느리거나 중단된 앱(Azure App Service)

요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.

* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>모니터링 블레이드

모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다. 이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.

ASP.NET Core 확장이 설치되어 있는지 확인합니다. 확장이 설치되어 있지 않으면 수동으로 설치합니다.

1. **개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.
1. **ASP.NET Core 확장** 이 목록에 표시됩니다.
1. 확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.
1. 목록에서 **ASP.NET Core 확장** 을 선택합니다.
1. **확인** 을 선택하여 적합한 조건을 적용합니다.
1. **확장 추가** 블레이드에서 **확인** 을 선택합니다.
1. 정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.

stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.

1. Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다. **이동&rarr;** 단추를 선택합니다. 새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.
1. 페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.
1. **site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.
1. *web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.
1. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.
1. **저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.

계속해서 진단 로깅을 활성화합니다.

1. Azure Portal에서 **진단 로그** 블레이드를 선택합니다.
1. **애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지** 에 대해 **켜기** 스위치를 선택합니다. 블레이드 위쪽에 있는 **저장** 단추를 선택합니다.
1. FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적** 에 대해 **켜기** 스위치를 선택합니다.
1. 포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.
1. 앱에 대한 요청을 실행합니다.
1. 로그 스트림 데이터 내에 오류의 원인이 표시됩니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.

실패한 요청 추적 로그(FREB 로그)를 보려면:

1. Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.
1. 사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그** 를 선택합니다.

자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.

자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

## <a name="troubleshoot-on-iis"></a>IIS에 대한 문제 해결

### <a name="application-event-log-iis"></a>애플리케이션 이벤트 로그(IIS)

애플리케이션 이벤트 로그에 액세스합니다.

1. 시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.
1. **이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.
1. **애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.
1. 실패한 앱과 연결된 오류를 검색합니다. 오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.

### <a name="run-the-app-at-a-command-prompt"></a>명령 프롬프트에서 앱 실행

애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다. 호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.

#### <a name="framework-dependent-deployment"></a>프레임워크 종속 배포

앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe* 로 앱의 어셈블리를 실행하여 앱을 실행합니다. `dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

#### <a name="self-contained-deployment"></a>자체 포함 배포

앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:

1. 명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다. `<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.
1. 오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.
1. 앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다. 기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다. 앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Core 모듈 stdout 로그(IIS)

stdout 로그를 사용하고 보려면:

1. 호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.
1. *logs* 폴더가 없으면 폴더를 만듭니다. MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.
1. *web.config* 파일을 편집합니다. **stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`). 경로의 `stdout`은 로그 파일 이름 접두사입니다. 로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다. `stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log* 로 지정됩니다.
1. 애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.
1. 업데이트된 *web.config* 파일을 저장합니다.
1. 앱에 대한 요청을 실행합니다.
1. *logs* 폴더로 이동합니다. 가장 최근의 stdout 로그를 찾아서 엽니다.
1. 오류에 대한 로그를 검토합니다.

문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.

1. *web.config* 파일을 편집합니다.
1. **stdoutLogEnabled** 를 `false`로 설정합니다.
1. 파일을 저장합니다.

자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.

> [!WARNING]
> stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다. 로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.
>
> ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다. 자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.

### <a name="enable-the-developer-exception-page"></a>개발자 예외 페이지 사용

`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다. 앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다. 문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다. *web.config* 에서 환경 변수를 설정하는 방법에 대한 자세한 내용은 [aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.

### <a name="obtain-data-from-an-app"></a>앱에서 데이터 얻기

앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다. 자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.

### <a name="slow-or-hanging-app-iis"></a>앱이 느리거나 중단됨(IIS)

*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.

#### <a name="app-crashes-or-encounters-an-exception"></a>앱 충돌 또는 예외 발생

[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.

1. `c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다. 앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.
1. [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:
   * 앱에서 [ 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. 충돌이 발생하는 조건에서 앱을 실행합니다.
1. 충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:
   * 앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * 앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:

     ```console
     .\DisableDumps dotnet.exe
     ```

앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다. PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.

> [!WARNING]
> 크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>앱 중단 시작 중에 실패 또는 정상적으로 실행

앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.

#### <a name="analyze-the-dump"></a>덤프 분석

덤프는 여러 방법을 사용하여 분석할 수 있습니다. 자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.

## <a name="clear-package-caches"></a>패키지 캐시 지우기

개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다. 경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다. 이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.

1. *bin* 및 *obj* 폴더를 삭제합니다.
1. 명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.

   [nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다. *nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.

1. 프로젝트를 복원하고 다시 빌드합니다.
1. 앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure 설명서

* [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics)
* [방법: Azure App Service에서 앱 모니터링](/azure/app-service/web-sites-monitor)
* [Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service에서 느린 웹앱 성능 문제 해결](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure의 웹앱에 대한 애플리케이션 성능 FAQ](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))
* [Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio 설명서

* [Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio를 사용하여 디버깅하는 자세한 내용](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code 설명서

* [Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)

::: moniker-end
