---
title: ASP.NET Core를 사용하는 Azure App Service 및 IIS에 대한 일반적인 오류 참조
author: rick-anderson
description: Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스팅할 때 일반적인 오류에 대한 문제 해결 조언을 얻습니다.
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: b009cc61a94e618a48d96ecbd770ef6371308f6a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059847"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>ASP.NET Core를 사용하는 Azure App Service 및 IIS에 대한 일반적인 오류 참조

::: moniker range=">= aspnetcore-2.2"

이 토픽에서는 일반적인 오류에 대해 설명하고 Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스트할 때 발생되는 특정 오류에 대한 문제 해결 조언을 제공합니다.

일반적인 문제 해결 지침은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

다음과 같은 정보를 수집합니다.

* 브라우저 동작(상태 코드 및 오류 메시지)
* 애플리케이션 이벤트 로그 항목
  * Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.
  * IIS
    1. **Windows** 메뉴에서 **시작** 을 선택하고, *이벤트 뷰어* 를 입력하고, **Enter** 를 누릅니다.
    1. **이벤트 뷰어** 가 열리면 사이드바에서 **Windows 로그** > **애플리케이션** 을 확장합니다.
* ASP.NET Core 모듈 stdout 및 디버그 로그 항목
  * Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.
  * IIS: ASP.NET Core 모듈 항목의 [로그 생성 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) 및 [향상된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션의 지침을 따릅니다.

오류 정보를 다음 일반 오류와 비교합니다. 일치하는 항목이 발견되면 문제 해결 권장 사항을 따릅니다.

이 항목의 오류 목록은 완전하지 않습니다. 여기에 나열되지 않은 오류가 발생하는 경우 오류를 재현하는 방법에 대한 자세한 지침과 함께 이 항목 하단에 있는 **콘텐츠 피드백** 단추를 사용하여 새 문제를 엽니다.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨

**애플리케이션 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** 을 로드하지 못했습니다. 데이터 오류입니다.

문제 해결:

OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다. OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음, OS를 업그레이드한 후에 32비트 모드에서 앱 풀을 실행하면 이 문제가 발생합니다. OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다. [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요. 설치 관리자가 실행될 때 **복구** 를 선택합니다.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>사이트 확장 누락, 32비트(x86) 및 64비트(x64) 사이트 확장이 설치됨 또는 잘못된 프로세스 비트 수가 설정됨

Azure App Services에서 호스트하는 앱에 적용됩니다.

* **브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패

* **애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다. inprocess 요청 처리기를 찾을 수 없습니다. hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다. ‘/LM/W3SVC/1416782824/ROOT’ 애플리케이션을 시작하지 못했습니다. 오류 코드 ‘0x8000ffff’.

* **ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.

* **ASP.NET Core 모듈 디버그 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다. 이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요. 실패한 HRESULT가 반환되었습니다. 0x8000ffff. inprocess 요청 처리기를 찾을 수 없습니다. 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.

문제 해결:

* 미리 보기 런타임에서 앱을 실행하는 경우 앱의 비트 수 및 앱의 런타임 버전과 일치하는 32비트(x86) **또는** 64비트(x64) 사이트 확장을 설치합니다. **두 확장을 모두 설치하거나 확장의 여러 런타임 버전을 설치하지 않습니다.**

  * ASP.NET Core {RUNTIME VERSION}(x86) 런타임
  * ASP.NET Core {RUNTIME VERSION}(x64) 런타임

  앱을 다시 시작합니다. 앱이 다시 시작될 때까지 몇 초간 기다립니다.

* 미리 보기 런타임에서 앱을 실행 중이며 32비트(x86) 및 64비트(x64) [사이트 확장](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)이 둘 다 설치된 경우 앱의 비트 수와 일치하지 않는 사이트 확장을 제거합니다. 사이트 확장을 제거한 후 앱을 다시 시작합니다. 앱이 다시 시작될 때까지 몇 초간 기다립니다.

* 미리 보기 런타임에서 앱을 실행 중이며 사이트 확장의 비트 수가 앱의 비트 수와 일치하는 경우 미리 보기 사이트 확장의 ‘런타임 버전’이 앱의 런타임 버전과 일치하는지 확인합니다.

* **애플리케이션 설정** 에 있는 앱의 **플랫폼** 이 앱의 비트 수와 일치하는지 확인합니다.

자세한 내용은 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>를 참조하세요.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>x86 앱이 배포되었지만 32비트 앱에 대해 앱 풀이 활성화되어 있지 않습니다.

* **브라우저:** HTTP 오류 500.30 - ANCM In-Process 시작 실패

* **애플리케이션 로그:** 실제 루트 '{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 예기치 않은 관리 예외, 예외 코드 = '0xe0434352'가 적중했습니다. 자세한 내용은 stderr 로그를 확인하세요. 실제 루트'{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 clr 및 관리되는 애플리케이션을 로드하지 못했습니다. CLR 작업자 스레드가 조기에 종료됨

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.

* **ASP.NET Core 모듈 디버그 로그:** 실패한 HRESULT가 반환되었습니다. 0x8007023e

이 시나리오는 자체 포함된 앱을 게시할 때 SDK에 의해 트래핑됩니다. RID가 플랫폼 대상과 일치하지 않으면 SDK에서 오류가 발생합니다(예: 프로젝트 파일에 `<PlatformTarget>x86</PlatformTarget>`이 있는 `win10-x64` RID).

문제 해결:

x86 프레임워크 종속 배포(`<PlatformTarget>x86</PlatformTarget>`)의 경우 32비트 앱용 IIS 앱 풀을 사용하도록 설정합니다. IIS 관리자에서 앱 풀의 **고급 설정** 을 열고 **32비트 앱 사용** 을 **True** 로 설정합니다.

## <a name="platform-conflicts-with-rid"></a>플랫폼이 RID와 충돌함

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : ff.

* **ASP.NET Core 모듈 stdout 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일 또는 어셈블리 '{ASSEMBLY}.dll'을 로드할 수 없습니다. 프로그램을 잘못된 형식으로 로드하려고 했습니다.

문제 해결:

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

* Azure 앱 배포에서 앱을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다. 호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI 엔드포인트가 잘못되었거나 중지된 웹 사이트

* **브라우저:** ERR_CONNECTION_REFUSED **--또는--** 연결할 수 없음

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

* 사용 중인 앱에 대해 올바른 URI 엔드포인트를 확인합니다. 바인딩을 확인합니다.

* IIS 웹 사이트가 ‘중지됨’ 상태가 아닌지 확인합니다.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음

**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.

문제 해결:

적절한 역할 및 기능이 사용 가능한지 확인합니다. [IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.

## <a name="incorrect-website-physical-path-or-app-missing"></a>잘못된 웹 사이트 실제 경로 또는 누락된 앱

* **브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다. **--또는--** 403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

IIS 웹 사이트 **기본 설정** 과 실제 앱 폴더를 확인합니다. 앱이 IIS 웹 사이트 **실제 경로** 의 폴더에 있는지 확인합니다.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>잘못된 역할, 설치되지 않은 ASP.NET Core 모듈 또는 잘못된 권한

* **브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다. **--또는--** 이 페이지를 표시할 수 없습니다

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

* 적절한 역할을 사용할 수 있는지 확인합니다. [IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.

* **프로그램 및 기능** 또는 **앱 및 기능** 을 열고 **Windows Server 호스팅** 이 설치되어 있는지 확인합니다. **Windows Server 호스팅** 이 설치된 프로그램 목록에 없는 경우 .NET Core 호스팅 번들을 다운로드하여 설치합니다.

  [현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

* **애플리케이션 풀** > **프로세스 모델** > **Identity** 가 **ApplicationPoolIdentity** 로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지를 확인합니다.

* ASP.NET Core 호스팅 번들을 제거하고 이전 버전의 호스팅 번들을 설치하는 경우 *applicationHost.config* 파일에는 ASP.NET Core 모듈에 대한 섹션이 포함되지 않습니다. *%windir%/System32/inetsrv/config* 에서 *applicationHost.config* 를 열고 `<configuration><configSections><sectionGroup name="system.webServer">` 섹션 그룹을 찾습니다. ASP.NET Core 모듈에 대한 섹션이 섹션 그룹에서 누락된 경우 섹션 요소를 추가합니다.

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  또는 최신 버전의 ASP.NET Core 호스팅 번들을 설치합니다. 최신 버전은 지원되는 이전 버전의 ASP.NET Core 앱과 호환 가능합니다.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반

* **브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패

* **애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"{...}" 명령줄로 프로세스를 시작하지 못했습니다. ', ErrorCode = '0x80070002 : 0. 애플리케이션 '{PATH}'를 시작할 수 없습니다. '{PATH}'에서 실행 파일을 찾을 수 없습니다. 애플리케이션 '/LM/W3SVC/2/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8007023e'.

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** 이벤트 로그: '애플리케이션 '{PATH}'를 시작할 수 없습니다. '{PATH}'에서 실행 파일을 찾을 수 없습니다. 실패한 HRESULT가 반환되었습니다. 0x8007023e

문제 해결:

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

* *web.config* 의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 `dotnet`인지 또는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에 대한 `.\{ASSEMBLY}.exe`인지 확인합니다.

* FDD의 경우 *dotnet.exe* 에서 PATH 설정을 통해 액세스하지 못할 수 있습니다. 시스템 PATH 설정에 *C:\Program Files\dotnet\\* 이 있는지 확인합니다.

* FDD의 경우 *dotnet.exe* 에서 앱 풀의 사용자 ID에 액세스하지 못할 수 있습니다. 앱 풀 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다. *C:\Program Files\dotnet* 및 앱 디렉터리에 앱 풀 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.

* IIS를 다시 시작하지 않은 상태로 FDD를 배포하고 .NET Core를 설치했을 수 있습니다. 서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.

* 호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다. .NET Core 런타임이 설치되어 있지 않으면 시스템에서 **.NET Core 호스팅 번들 설치 관리자** 를 실행합니다.

  [현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

  특정 런타임이 필요한 경우 [.NET Download Archives](https://dotnet.microsoft.com/download/archives)에서 런타임을 다운로드하여 시스템에 설치합니다. 설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>\<aspNetCore> 요소의 인수가 잘못됨

* **브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패

* **애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다. 이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요. inprocess 요청 처리기를 찾을 수 없습니다. hostfxr 호출에서 캡처된 출력: dotnet SDK 명령을 실행하시겠습니까? 다음 위치에서 dotnet SDK를 설치하세요. https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 애플리케이션 '/LM/W3SVC/3/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8000ffff'.

* **ASP.NET Core 모듈 stdout 로그:** dotnet SDK 명령을 실행하시겠습니까? [https://go.microsoft.com/fwlink/?LinkID=798306&amp;clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 ) 에서 dotnet SDK를 설치하세요.

* **ASP.NET Core 모듈 디버그 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다. 이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요. 실패한 HRESULT가 반환되었습니다. 0x8000ffff inprocess 요청 처리기를 찾을 수 없습니다. hostfxr 호출에서 캡처된 출력: dotnet SDK 명령을 실행하시겠습니까? 다음 위치에서 dotnet SDK를 설치하세요. https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 실패한 HRESULT가 반환되었습니다. 0x8000ffff

문제 해결:

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

* *web.config* 의 `<aspNetCore>` 요소에서 *인수* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 `.\{ASSEMBLY}.dll`인지, 또는 (b) 없는 경우 빈 문자열(`arguments=""`)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 목록인지 확인합니다.

## <a name="missing-net-core-shared-framework"></a>.NET Core 공유 프레임워크 누락

* **브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패

* **애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다. 이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요. inprocess 요청 처리기를 찾을 수 없습니다. hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 'Microsoft.AspNetCore.App', 버전 '{VERSION}'을 찾을 수 없습니다.

애플리케이션 '/LM/W3SVC/5/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8000ffff'.

* **ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 'Microsoft.AspNetCore.App', 버전 '{VERSION}'을 찾을 수 없습니다.

* **ASP.NET Core 모듈 디버그 로그:** 실패한 HRESULT가 반환되었습니다. 0x8000ffff

문제 해결:

FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.

## <a name="stopped-application-pool"></a>중지된 애플리케이션 풀

* **브라우저:** 503 서비스를 사용할 수 없음

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

애플리케이션 풀이 ‘중지됨’ 상태가 아닌지 확인합니다.

## <a name="sub-application-includes-a-handlers-section"></a>하위 애플리케이션에 \<handlers> 섹션이 포함됨

* **브라우저:** HTTP 오류 500.19 - 내부 서버 오류

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다. 하위 앱의 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다. 하위 앱의 로그 파일이 생성되지 않습니다.

문제 해결:

하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않거나 하위 앱이 부모 앱의 처리기를 상속하지 않았는지 확인합니다.

*web.config* 의 부모 앱 `<system.webServer>` 섹션은 `<location>` 요소 내부에 배치되어 있습니다. <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 부모 앱의 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다. 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.

## <a name="stdout-log-path-incorrect"></a>stdout 로그 경로가 올바르지 않음

* **브라우저:** 앱이 정상적으로 응답합니다.

* **애플리케이션 로그:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 시작하지 못했습니다. 예외 메시지: HRESULT 0x80070005가 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84에서 반환되었습니다. C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 중지하지 못했습니다. 예외 메시지: HRESULT 0x80070002가 {PATH}에서 반환되었습니다. {PATH}\aspnetcorev2_inprocess.dll에서 stdout 리디렉션을 시작할 수 없습니다.

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

* **ASP.NET Core 모듈 디버그 로그:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 시작하지 못했습니다. 예외 메시지: HRESULT 0x80070005가 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84에서 반환되었습니다. C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 중지하지 못했습니다. 예외 메시지: HRESULT 0x80070002가 {PATH}에서 반환되었습니다. {PATH}\aspnetcorev2_inprocess.dll에서 stdout 리디렉션을 시작할 수 없습니다.

문제 해결:

* *web.config* 의 `<aspNetCore>` 요소에 지정된 `stdoutLogFile` 경로가 없습니다. 자세한 내용은 [ASP.NET Core 모듈: 로그 만들기 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)을 참조하세요.

* 앱 풀 사용자는 stdout 로그 경로에 대한 쓰기 액세스 권한이 없습니다.

## <a name="application-configuration-general-issue"></a>일반적인 애플리케이션 구성 문제

* **브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패 **--또는--** HTTP 오류 500.30 - ANCM In-Process 시작 실패

* **애플리케이션 로그:** 변수

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일은 생성되지만 비어 있거나 앱이 실패할 때까지 일반 항목으로 생성됩니다.

* **ASP.NET Core 모듈 디버그 로그:** 변수

문제 해결:

앱 구성 또는 프로그래밍 문제로 인해 프로세스를 시작하지 못했습니다.

자세한 내용은 다음 항목을 참조하세요.

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

이 토픽에서는 일반적인 오류에 대해 설명하고 Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스트할 때 발생되는 특정 오류에 대한 문제 해결 조언을 제공합니다.

일반적인 문제 해결 지침은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

다음과 같은 정보를 수집합니다.

* 브라우저 동작(상태 코드 및 오류 메시지)
* 애플리케이션 이벤트 로그 항목
  * Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.
  * IIS
    1. **Windows** 메뉴에서 **시작** 을 선택하고, *이벤트 뷰어* 를 입력하고, **Enter** 를 누릅니다.
    1. **이벤트 뷰어** 가 열리면 사이드바에서 **Windows 로그** > **애플리케이션** 을 확장합니다.
* ASP.NET Core 모듈 stdout 및 디버그 로그 항목
  * Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.
  * IIS: ASP.NET Core 모듈 항목의 [로그 생성 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) 및 [향상된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션의 지침을 따릅니다.

오류 정보를 다음 일반 오류와 비교합니다. 일치하는 항목이 발견되면 문제 해결 권장 사항을 따릅니다.

이 항목의 오류 목록은 완전하지 않습니다. 여기에 나열되지 않은 오류가 발생하는 경우 오류를 재현하는 방법에 대한 자세한 지침과 함께 이 항목 하단에 있는 **콘텐츠 피드백** 단추를 사용하여 새 문제를 엽니다.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨

**애플리케이션 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** 을 로드하지 못했습니다. 데이터 오류입니다.

문제 해결:

OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다. OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음, OS를 업그레이드한 후에 32비트 모드에서 앱 풀을 실행하면 이 문제가 발생합니다. OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다. [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요. 설치 관리자가 실행될 때 **복구** 를 선택합니다.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>사이트 확장 누락, 32비트(x86) 및 64비트(x64) 사이트 확장이 설치됨 또는 잘못된 프로세스 비트 수가 설정됨

Azure App Services에서 호스트하는 앱에 적용됩니다.

* **브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패

* **애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다. inprocess 요청 처리기를 찾을 수 없습니다. hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다. ‘/LM/W3SVC/1416782824/ROOT’ 애플리케이션을 시작하지 못했습니다. 오류 코드 ‘0x8000ffff’.

* **ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다. 지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.

문제 해결:

* 미리 보기 런타임에서 앱을 실행하는 경우 앱의 비트 수 및 앱의 런타임 버전과 일치하는 32비트(x86) **또는** 64비트(x64) 사이트 확장을 설치합니다. **두 확장을 모두 설치하거나 확장의 여러 런타임 버전을 설치하지 않습니다.**

  * ASP.NET Core {RUNTIME VERSION}(x86) 런타임
  * ASP.NET Core {RUNTIME VERSION}(x64) 런타임

  앱을 다시 시작합니다. 앱이 다시 시작될 때까지 몇 초간 기다립니다.

* 미리 보기 런타임에서 앱을 실행 중이며 32비트(x86) 및 64비트(x64) [사이트 확장](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)이 둘 다 설치된 경우 앱의 비트 수와 일치하지 않는 사이트 확장을 제거합니다. 사이트 확장을 제거한 후 앱을 다시 시작합니다. 앱이 다시 시작될 때까지 몇 초간 기다립니다.

* 미리 보기 런타임에서 앱을 실행 중이며 사이트 확장의 비트 수가 앱의 비트 수와 일치하는 경우 미리 보기 사이트 확장의 ‘런타임 버전’이 앱의 런타임 버전과 일치하는지 확인합니다.

* **애플리케이션 설정** 에 있는 앱의 **플랫폼** 이 앱의 비트 수와 일치하는지 확인합니다.

자세한 내용은 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>를 참조하세요.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>x86 앱이 배포되었지만 32비트 앱에 대해 앱 풀이 활성화되어 있지 않습니다.

* **브라우저:** HTTP 오류 500.30 - ANCM In-Process 시작 실패

* **애플리케이션 로그:** 실제 루트 '{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 예기치 않은 관리 예외, 예외 코드 = '0xe0434352'가 적중했습니다. 자세한 내용은 stderr 로그를 확인하세요. 실제 루트'{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 clr 및 관리되는 애플리케이션을 로드하지 못했습니다. CLR 작업자 스레드가 조기에 종료됨

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.

이 시나리오는 자체 포함된 앱을 게시할 때 SDK에 의해 트래핑됩니다. RID가 플랫폼 대상과 일치하지 않으면 SDK에서 오류가 발생합니다(예: 프로젝트 파일에 `<PlatformTarget>x86</PlatformTarget>`이 있는 `win10-x64` RID).

문제 해결:

x86 프레임워크 종속 배포(`<PlatformTarget>x86</PlatformTarget>`)의 경우 32비트 앱용 IIS 앱 풀을 사용하도록 설정합니다. IIS 관리자에서 앱 풀의 **고급 설정** 을 열고 **32비트 앱 사용** 을 **True** 로 설정합니다.

## <a name="platform-conflicts-with-rid"></a>플랫폼이 RID와 충돌함

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : ff.

* **ASP.NET Core 모듈 stdout 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일 또는 어셈블리 '{ASSEMBLY}.dll'을 로드할 수 없습니다. 프로그램을 잘못된 형식으로 로드하려고 했습니다.

문제 해결:

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

* Azure 앱 배포에서 앱을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다. 호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI 엔드포인트가 잘못되었거나 중지된 웹 사이트

* **브라우저:** ERR_CONNECTION_REFUSED **--또는--** 연결할 수 없음

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

* 사용 중인 앱에 대해 올바른 URI 엔드포인트를 확인합니다. 바인딩을 확인합니다.

* IIS 웹 사이트가 ‘중지됨’ 상태가 아닌지 확인합니다.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음

**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.

문제 해결:

적절한 역할 및 기능이 사용 가능한지 확인합니다. [IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.

## <a name="incorrect-website-physical-path-or-app-missing"></a>잘못된 웹 사이트 실제 경로 또는 누락된 앱

* **브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다. **--또는--** 403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

IIS 웹 사이트 **기본 설정** 과 실제 앱 폴더를 확인합니다. 앱이 IIS 웹 사이트 **실제 경로** 의 폴더에 있는지 확인합니다.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>잘못된 역할, 설치되지 않은 ASP.NET Core 모듈 또는 잘못된 권한

* **브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다. **--또는--** 이 페이지를 표시할 수 없습니다

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

* 적절한 역할을 사용할 수 있는지 확인합니다. [IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.

* **프로그램 및 기능** 또는 **앱 및 기능** 을 열고 **Windows Server 호스팅** 이 설치되어 있는지 확인합니다. **Windows Server 호스팅** 이 설치된 프로그램 목록에 없는 경우 .NET Core 호스팅 번들을 다운로드하여 설치합니다.

  [현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

* **애플리케이션 풀** > **프로세스 모델** > **Identity** 가 **ApplicationPoolIdentity** 로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지를 확인합니다.

* ASP.NET Core 호스팅 번들을 제거하고 이전 버전의 호스팅 번들을 설치하는 경우 *applicationHost.config* 파일에는 ASP.NET Core 모듈에 대한 섹션이 포함되지 않습니다. *%windir%/System32/inetsrv/config* 에서 *applicationHost.config* 를 열고 `<configuration><configSections><sectionGroup name="system.webServer">` 섹션 그룹을 찾습니다. ASP.NET Core 모듈에 대한 섹션이 섹션 그룹에서 누락된 경우 섹션 요소를 추가합니다.

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  또는 최신 버전의 ASP.NET Core 호스팅 번들을 설치합니다. 최신 버전은 지원되는 이전 버전의 ASP.NET Core 앱과 호환 가능합니다.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"{...}" 명령줄로 프로세스를 시작하지 못했습니다. ', ErrorCode = '0x80070002 : 0.

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.

문제 해결:

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

* *web.config* 의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 `dotnet`인지 또는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에 대한 `.\{ASSEMBLY}.exe`인지 확인합니다.

* FDD의 경우 *dotnet.exe* 에서 PATH 설정을 통해 액세스하지 못할 수 있습니다. 시스템 PATH 설정에 *C:\Program Files\dotnet\\* 이 있는지 확인합니다.

* FDD의 경우 *dotnet.exe* 에서 앱 풀의 사용자 ID에 액세스하지 못할 수 있습니다. 앱 풀 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다. *C:\Program Files\dotnet* 및 앱 디렉터리에 앱 풀 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.

* IIS를 다시 시작하지 않은 상태로 FDD를 배포하고 .NET Core를 설치했을 수 있습니다. 서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.

* 호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다. .NET Core 런타임이 설치되어 있지 않으면 시스템에서 **.NET Core 호스팅 번들 설치 관리자** 를 실행합니다.

  [현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.

  특정 런타임이 필요한 경우 [.NET Download Archives](https://dotnet.microsoft.com/download/archives)에서 런타임을 다운로드하여 시스템에 설치합니다. 설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>\<aspNetCore> 요소의 인수가 잘못됨

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"dotnet" .\{ASSEMBLY}.dll' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : 80008081.

* **ASP.NET Core 모듈 stdout 로그:** 실행할 애플리케이션이 없습니다. 'PATH\{ASSEMBLY}.dll'

문제 해결:

* 앱이 Kestrel에서 로컬로 실행되는지 확인합니다. 프로세스 오류는 앱 내의 문제 때문일 수 있습니다. 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.

* *web.config* 의 `<aspNetCore>` 요소에서 *인수* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 `.\{ASSEMBLY}.dll`인지, 또는 (b) 없는 경우 빈 문자열(`arguments=""`)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 목록인지 확인합니다.

문제 해결:

FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.

## <a name="stopped-application-pool"></a>중지된 애플리케이션 풀

* **브라우저:** 503 서비스를 사용할 수 없음

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

애플리케이션 풀이 ‘중지됨’ 상태가 아닌지 확인합니다.

## <a name="sub-application-includes-a-handlers-section"></a>하위 애플리케이션에 \<handlers> 섹션이 포함됨

* **브라우저:** HTTP 오류 500.19 - 내부 서버 오류

* **애플리케이션 로그:** 진입 금지

* **ASP.NET Core 모듈 stdout 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다. 하위 앱의 로그 파일이 생성되지 않습니다.

문제 해결:

하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않은지 확인합니다.

## <a name="stdout-log-path-incorrect"></a>stdout 로그 경로가 올바르지 않음

* **브라우저:** 앱이 정상적으로 응답합니다.

* **애플리케이션 로그:** 경고: stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log를 만들지 못했습니다. 오류 코드 = -2147024893.

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.

문제 해결:

* *web.config* 의 `<aspNetCore>` 요소에 지정된 `stdoutLogFile` 경로가 없습니다. 자세한 내용은 [ASP.NET Core 모듈: 로그 만들기 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)을 참조하세요.

* 앱 풀 사용자는 stdout 로그 경로에 대한 쓰기 액세스 권한이 없습니다.

## <a name="application-configuration-general-issue"></a>일반적인 애플리케이션 구성 문제

* **브라우저:** HTTP 오류 502.5 - 프로세스 오류

* **애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 크래시하거나 응답하지 않거나 수신 대기하지 않습니다. 오류 코드 = '{ERROR CODE}'

* **ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.

문제 해결:

앱 구성 또는 프로그래밍 문제로 인해 프로세스를 시작하지 못했습니다.

자세한 내용은 다음 항목을 참조하세요.

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
