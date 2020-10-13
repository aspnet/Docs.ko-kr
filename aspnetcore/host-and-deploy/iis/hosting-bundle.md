---
title: 호스팅 번들
author: rick-anderson
description: .NET Core 호스팅 번들을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755176"
---
# <a name="the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들

.NET Core 호스팅 번들은 .NET Core 런타임 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)용 설치 관리자입니다. 번들을 통해 ASP.NET Core 앱을 IIS에서 실행할 수 있습니다.

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core 호스팅 번들 설치

> [!IMPORTANT]
> IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다. IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.
>
> .NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)). 이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.

### <a name="direct-download-current-version"></a>직접 다운로드(현재 버전)

다음 링크를 사용하여 설치 관리자를 다운로드합니다.

[현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>이전 버전의 설치 관리자

이전 버전의 설치 관리자를 가져오려면:

1. [.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.
1. 원하는 .NET Core 버전을 선택합니다.
1. **앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.
1. **호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.

> [!WARNING]
> 일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다. 자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.

### <a name="install-the-hosting-bundle"></a>호스팅 번들 설치

1. 서버에서 설치 관리자를 실행합니다. 관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.

   * `OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.
   * `OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다. 서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.
   * `OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다. 서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.
   * `OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다. 32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다. 향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(`applicationHost.config`)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다. *ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.* 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.
1. 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.

ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택하지 않습니다. 새 호스팅 번들을 설치하여 공유 프레임워크를 업그레이드한 후, 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>모듈 버전 및 호스팅 번들 설치 관리자 로그

설치된 ASP.NET Core 모듈의 버전을 확인하려면:

1. 호스팅 시스템에서 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`로 이동합니다.
1. `aspnetcorev2.dll` 파일을 찾습니다.
1. 파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성**을 선택합니다.
1. **세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전**은 설치된 모듈 버전을 나타냅니다.

모듈의 호스팅 번들 설치 관리자 로그는 `C:\Users\%UserName%\AppData\Local\Temp`에 있습니다. 파일 이름은 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`입니다. 여기서 자리 표시자 `{TIMESTAMP}`는 파일의 타임스탬프입니다.
