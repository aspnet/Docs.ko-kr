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
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="91ed8-103">.NET Core 호스팅 번들</span><span class="sxs-lookup"><span data-stu-id="91ed8-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="91ed8-104">.NET Core 호스팅 번들은 .NET Core 런타임 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)용 설치 관리자입니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="91ed8-105">번들을 통해 ASP.NET Core 앱을 IIS에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="91ed8-106">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="91ed8-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="91ed8-107">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="91ed8-108">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="91ed8-109">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="91ed8-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="91ed8-110">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="91ed8-111">직접 다운로드(현재 버전)</span><span class="sxs-lookup"><span data-stu-id="91ed8-111">Direct download (current version)</span></span>

<span data-ttu-id="91ed8-112">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="91ed8-113">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="91ed8-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="91ed8-114">Visual C++ 재배포 가능 패키지 요구 사항</span><span class="sxs-lookup"><span data-stu-id="91ed8-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="91ed8-115">이전 버전의 Windows(예: Windows Server 2012 R2)에는 Visual Studio C++ 2015, 2017, 2019 재배포 가능 패키지를 설치하세요.</span><span class="sxs-lookup"><span data-stu-id="91ed8-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="91ed8-116">그렇지 않으면 Windows 이벤트 로그의 혼란스러운 오류 메시지가 다음을 보고합니다. `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="91ed8-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="91ed8-117">[현재 x64 VS C++ 재배포 가능 패키지](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[현재 x86 VS C++ 재배포 가능 패키지](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="91ed8-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="91ed8-118">이전 버전의 설치 관리자</span><span class="sxs-lookup"><span data-stu-id="91ed8-118">Earlier versions of the installer</span></span>

<span data-ttu-id="91ed8-119">이전 버전의 설치 관리자를 가져오려면:</span><span class="sxs-lookup"><span data-stu-id="91ed8-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="91ed8-120">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="91ed8-121">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="91ed8-122">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="91ed8-123">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="91ed8-124">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="91ed8-125">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91ed8-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="91ed8-126">옵션</span><span class="sxs-lookup"><span data-stu-id="91ed8-126">Options</span></span>

1. <span data-ttu-id="91ed8-127">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="91ed8-128">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="91ed8-129">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="91ed8-130">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="91ed8-131">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="91ed8-132">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="91ed8-133">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="91ed8-134">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="91ed8-135">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="91ed8-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(`applicationHost.config`)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="91ed8-137">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="91ed8-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="91ed8-138">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91ed8-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="91ed8-139">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91ed8-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="91ed8-140">IIS 다시 시작</span><span class="sxs-lookup"><span data-stu-id="91ed8-140">Restart IIS</span></span>

<span data-ttu-id="91ed8-141">호스팅 번들을 설치한 후 수동 IIS 다시 시작이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="91ed8-142">예를 들어 `dotnet` CLI 도구(명령)가 IIS 작업자 프로세스 실행 경로에 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="91ed8-143">IIS를 수동으로 중지하고 시작하려면 관리자 권한 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="91ed8-144">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="91ed8-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="91ed8-145">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="91ed8-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="91ed8-146">호스팅 시스템에서 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="91ed8-147">`aspnetcorev2.dll` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="91ed8-148">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="91ed8-149">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전** 은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="91ed8-150">모듈의 호스팅 번들 설치 관리자 로그는 `C:\Users\%UserName%\AppData\Local\Temp`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="91ed8-151">파일 이름은 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`입니다. 여기서 자리 표시자 `{TIMESTAMP}`는 파일의 타임스탬프입니다.</span><span class="sxs-lookup"><span data-stu-id="91ed8-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
