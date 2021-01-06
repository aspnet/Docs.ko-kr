---
title: 호스팅 번들
author: rick-anderson
description: .NET Core 호스팅 번들을 구성하는 방법을 알아봅니다.
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93343639"
---
# <a name="the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들

.NET Core 호스팅 번들은 .NET Core 런타임 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)용 설치 관리자입니다. 번들을 통해 ASP.NET Core 앱을 IIS에서 실행할 수 있습니다.

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들 설치

> [!IMPORTANT]
> IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다. IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.
>
> .NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)). 이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.

## <a name="direct-download-current-version"></a>직접 다운로드(현재 버전)

다음 링크를 사용하여 설치 관리자를 다운로드합니다.

[현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a>Visual C++ 재배포 가능 패키지 요구 사항

이전 버전의 Windows(예: Windows Server 2012 R2)에는 Visual Studio C++ 2015, 2017, 2019 재배포 가능 패키지를 설치하세요. 그렇지 않으면 Windows 이벤트 로그의 혼란스러운 오류 메시지가 다음을 보고합니다. `The data is the error.`

[현재 x64 VS C++ 재배포 가능 패키지](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[현재 x86 VS C++ 재배포 가능 패키지](https://aka.ms/vs/16/release/vc_redist.x86.exe)

## <a name="earlier-versions-of-the-installer"></a>이전 버전의 설치 관리자

이전 버전의 설치 관리자를 가져오려면:

1. [.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.
1. 원하는 .NET Core 버전을 선택합니다.
1. **앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.
1. **호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.

> [!WARNING]
> 일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다. 자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.

## <a name="options"></a>옵션

1. 관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.

   * `OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.
   * `OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다. 서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.
   * `OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다. 서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.
   * `OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다. 32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다. 향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(`applicationHost.config`)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다. *ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.* 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.

> [!NOTE]
> IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.

## <a name="restart-iis"></a>IIS 다시 시작

호스팅 번들을 설치한 후 수동 IIS 다시 시작이 필요할 수 있습니다. 예를 들어 `dotnet` CLI 도구(명령)가 IIS 작업자 프로세스 실행 경로에 없을 수 있습니다.

IIS를 수동으로 중지하고 시작하려면 관리자 권한 명령 셸에서 다음 명령을 실행합니다.

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a>모듈 버전 및 호스팅 번들 설치 관리자 로그

설치된 ASP.NET Core 모듈의 버전을 확인하려면:

1. 호스팅 시스템에서 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`로 이동합니다.
1. `aspnetcorev2.dll` 파일을 찾습니다.
1. 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.
1. **세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.

모듈의 호스팅 번들 설치 관리자 로그는 `C:\Users\%UserName%\AppData\Local\Temp`에 있습니다. 파일 이름은 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`입니다. 여기서 자리 표시자 `{TIMESTAMP}`는 파일의 타임스탬프입니다.
