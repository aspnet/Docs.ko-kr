---
title: ASP.NET Core를 사용하는 Azure App Service 및 IIS에 대한 일반적인 오류 참조
author: rick-anderson
description: Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스팅할 때 일반적인 오류에 대한 문제 해결 조언을 얻습니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: b009cc61a94e618a48d96ecbd770ef6371308f6a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059847"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="2818f-103">ASP.NET Core를 사용하는 Azure App Service 및 IIS에 대한 일반적인 오류 참조</span><span class="sxs-lookup"><span data-stu-id="2818f-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2818f-104">이 토픽에서는 일반적인 오류에 대해 설명하고 Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스트할 때 발생되는 특정 오류에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-104">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="2818f-105">일반적인 문제 해결 지침은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-105">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="2818f-106">다음과 같은 정보를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-106">Collect the following information:</span></span>

* <span data-ttu-id="2818f-107">브라우저 동작(상태 코드 및 오류 메시지)</span><span class="sxs-lookup"><span data-stu-id="2818f-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="2818f-108">애플리케이션 이벤트 로그 항목</span><span class="sxs-lookup"><span data-stu-id="2818f-108">Application Event Log entries</span></span>
  * <span data-ttu-id="2818f-109">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-109">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="2818f-110">IIS</span><span class="sxs-lookup"><span data-stu-id="2818f-110">IIS</span></span>
    1. <span data-ttu-id="2818f-111">**Windows** 메뉴에서 **시작** 을 선택하고, *이벤트 뷰어* 를 입력하고, **Enter** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-111">Select **Start** on the **Windows** menu, type *Event Viewer* , and press **Enter**.</span></span>
    1. <span data-ttu-id="2818f-112">**이벤트 뷰어** 가 열리면 사이드바에서 **Windows 로그** > **애플리케이션** 을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="2818f-113">ASP.NET Core 모듈 stdout 및 디버그 로그 항목</span><span class="sxs-lookup"><span data-stu-id="2818f-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="2818f-114">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-114">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="2818f-115">IIS: ASP.NET Core 모듈 항목의 [로그 생성 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) 및 [향상된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-115">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="2818f-116">오류 정보를 다음 일반 오류와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="2818f-117">일치하는 항목이 발견되면 문제 해결 권장 사항을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="2818f-118">이 항목의 오류 목록은 완전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="2818f-119">여기에 나열되지 않은 오류가 발생하는 경우 오류를 재현하는 방법에 대한 자세한 지침과 함께 이 항목 하단에 있는 **콘텐츠 피드백** 단추를 사용하여 새 문제를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="2818f-120">OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨</span><span class="sxs-lookup"><span data-stu-id="2818f-120">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="2818f-121">**애플리케이션 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** 을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-121">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="2818f-122">데이터 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-122">The data is the error.</span></span>

<span data-ttu-id="2818f-123">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-123">Troubleshooting:</span></span>

<span data-ttu-id="2818f-124">OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-124">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="2818f-125">OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음, OS를 업그레이드한 후에 32비트 모드에서 앱 풀을 실행하면 이 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-125">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="2818f-126">OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-126">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="2818f-127">[.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-127">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="2818f-128">설치 관리자가 실행될 때 **복구** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-128">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="2818f-129">사이트 확장 누락, 32비트(x86) 및 64비트(x64) 사이트 확장이 설치됨 또는 잘못된 프로세스 비트 수가 설정됨</span><span class="sxs-lookup"><span data-stu-id="2818f-129">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="2818f-130">Azure App Services에서 호스트하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-130">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="2818f-131">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-131">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="2818f-132">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-132">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="2818f-133">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-133">Could not find inprocess request handler.</span></span> <span data-ttu-id="2818f-134">hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-134">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-135">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-135">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="2818f-136">‘/LM/W3SVC/1416782824/ROOT’ 애플리케이션을 시작하지 못했습니다. 오류 코드 ‘0x8000ffff’.</span><span class="sxs-lookup"><span data-stu-id="2818f-136">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="2818f-137">**ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-137">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-138">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-138">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

* <span data-ttu-id="2818f-139">**ASP.NET Core 모듈 디버그 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-139">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="2818f-140">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-140">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="2818f-141">실패한 HRESULT가 반환되었습니다. 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="2818f-141">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="2818f-142">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-142">Could not find inprocess request handler.</span></span> <span data-ttu-id="2818f-143">호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-143">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-144">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-144">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="2818f-145">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-145">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-146">미리 보기 런타임에서 앱을 실행하는 경우 앱의 비트 수 및 앱의 런타임 버전과 일치하는 32비트(x86) **또는** 64비트(x64) 사이트 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-146">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="2818f-147">**두 확장을 모두 설치하거나 확장의 여러 런타임 버전을 설치하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="2818f-147">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="2818f-148">ASP.NET Core {RUNTIME VERSION}(x86) 런타임</span><span class="sxs-lookup"><span data-stu-id="2818f-148">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="2818f-149">ASP.NET Core {RUNTIME VERSION}(x64) 런타임</span><span class="sxs-lookup"><span data-stu-id="2818f-149">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="2818f-150">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-150">Restart the app.</span></span> <span data-ttu-id="2818f-151">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-151">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="2818f-152">미리 보기 런타임에서 앱을 실행 중이며 32비트(x86) 및 64비트(x64) [사이트 확장](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)이 둘 다 설치된 경우 앱의 비트 수와 일치하지 않는 사이트 확장을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-152">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="2818f-153">사이트 확장을 제거한 후 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-153">After removing the site extension, restart the app.</span></span> <span data-ttu-id="2818f-154">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-154">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="2818f-155">미리 보기 런타임에서 앱을 실행 중이며 사이트 확장의 비트 수가 앱의 비트 수와 일치하는 경우 미리 보기 사이트 확장의 ‘런타임 버전’이 앱의 런타임 버전과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-155">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="2818f-156">**애플리케이션 설정** 에 있는 앱의 **플랫폼** 이 앱의 비트 수와 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-156">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="2818f-157">자세한 내용은 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-157">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="2818f-158">x86 앱이 배포되었지만 32비트 앱에 대해 앱 풀이 활성화되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-158">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="2818f-159">**브라우저:** HTTP 오류 500.30 - ANCM In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-159">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="2818f-160">**애플리케이션 로그:** 실제 루트 '{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 예기치 않은 관리 예외, 예외 코드 = '0xe0434352'가 적중했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-160">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="2818f-161">자세한 내용은 stderr 로그를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-161">Please check the stderr logs for more information.</span></span> <span data-ttu-id="2818f-162">실제 루트'{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 clr 및 관리되는 애플리케이션을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-162">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="2818f-163">CLR 작업자 스레드가 조기에 종료됨</span><span class="sxs-lookup"><span data-stu-id="2818f-163">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="2818f-164">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-164">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

* <span data-ttu-id="2818f-165">**ASP.NET Core 모듈 디버그 로그:** 실패한 HRESULT가 반환되었습니다. 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="2818f-165">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="2818f-166">이 시나리오는 자체 포함된 앱을 게시할 때 SDK에 의해 트래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-166">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="2818f-167">RID가 플랫폼 대상과 일치하지 않으면 SDK에서 오류가 발생합니다(예: 프로젝트 파일에 `<PlatformTarget>x86</PlatformTarget>`이 있는 `win10-x64` RID).</span><span class="sxs-lookup"><span data-stu-id="2818f-167">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="2818f-168">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-168">Troubleshooting:</span></span>

<span data-ttu-id="2818f-169">x86 프레임워크 종속 배포(`<PlatformTarget>x86</PlatformTarget>`)의 경우 32비트 앱용 IIS 앱 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-169">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="2818f-170">IIS 관리자에서 앱 풀의 **고급 설정** 을 열고 **32비트 앱 사용** 을 **True** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-170">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="2818f-171">플랫폼이 RID와 충돌함</span><span class="sxs-lookup"><span data-stu-id="2818f-171">Platform conflicts with RID</span></span>

* <span data-ttu-id="2818f-172">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-172">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="2818f-173">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="2818f-173">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="2818f-174">**ASP.NET Core 모듈 stdout 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일 또는 어셈블리 '{ASSEMBLY}.dll'을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-174">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="2818f-175">프로그램을 잘못된 형식으로 로드하려고 했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-175">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="2818f-176">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-176">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-177">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-177">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="2818f-178">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-178">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="2818f-179">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-179">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="2818f-180">Azure 앱 배포에서 앱을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-180">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="2818f-181">호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-181">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="2818f-182">URI 엔드포인트가 잘못되었거나 중지된 웹 사이트</span><span class="sxs-lookup"><span data-stu-id="2818f-182">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="2818f-183">**브라우저:** ERR_CONNECTION_REFUSED **--또는--** 연결할 수 없음</span><span class="sxs-lookup"><span data-stu-id="2818f-183">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="2818f-184">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-184">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-185">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-185">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="2818f-186">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-186">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-187">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-187">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-188">사용 중인 앱에 대해 올바른 URI 엔드포인트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-188">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="2818f-189">바인딩을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-189">Check the bindings.</span></span>

* <span data-ttu-id="2818f-190">IIS 웹 사이트가 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-190">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="2818f-191">CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음</span><span class="sxs-lookup"><span data-stu-id="2818f-191">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="2818f-192">**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-192">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="2818f-193">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-193">Troubleshooting:</span></span>

<span data-ttu-id="2818f-194">적절한 역할 및 기능이 사용 가능한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-194">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="2818f-195">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-195">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="2818f-196">잘못된 웹 사이트 실제 경로 또는 누락된 앱</span><span class="sxs-lookup"><span data-stu-id="2818f-196">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="2818f-197">**브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다. **--또는--** 403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-197">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="2818f-198">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-198">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-199">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-199">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="2818f-200">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-200">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-201">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-201">Troubleshooting:</span></span>

<span data-ttu-id="2818f-202">IIS 웹 사이트 **기본 설정** 과 실제 앱 폴더를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-202">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="2818f-203">앱이 IIS 웹 사이트 **실제 경로** 의 폴더에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-203">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="2818f-204">잘못된 역할, 설치되지 않은 ASP.NET Core 모듈 또는 잘못된 권한</span><span class="sxs-lookup"><span data-stu-id="2818f-204">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="2818f-205">**브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-205">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="2818f-206">**--또는--** 이 페이지를 표시할 수 없습니다</span><span class="sxs-lookup"><span data-stu-id="2818f-206">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="2818f-207">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-207">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-208">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-208">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="2818f-209">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-209">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-210">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-210">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-211">적절한 역할을 사용할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-211">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="2818f-212">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-212">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="2818f-213">**프로그램 및 기능** 또는 **앱 및 기능** 을 열고 **Windows Server 호스팅** 이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-213">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="2818f-214">**Windows Server 호스팅** 이 설치된 프로그램 목록에 없는 경우 .NET Core 호스팅 번들을 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-214">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="2818f-215">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="2818f-215">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="2818f-216">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-216">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="2818f-217">**애플리케이션 풀** > **프로세스 모델** > **Identity** 가 **ApplicationPoolIdentity** 로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-217">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="2818f-218">ASP.NET Core 호스팅 번들을 제거하고 이전 버전의 호스팅 번들을 설치하는 경우 *applicationHost.config* 파일에는 ASP.NET Core 모듈에 대한 섹션이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-218">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="2818f-219">*%windir%/System32/inetsrv/config* 에서 *applicationHost.config* 를 열고 `<configuration><configSections><sectionGroup name="system.webServer">` 섹션 그룹을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-219">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="2818f-220">ASP.NET Core 모듈에 대한 섹션이 섹션 그룹에서 누락된 경우 섹션 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-220">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="2818f-221">또는 최신 버전의 ASP.NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-221">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="2818f-222">최신 버전은 지원되는 이전 버전의 ASP.NET Core 앱과 호환 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-222">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="2818f-223">잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반</span><span class="sxs-lookup"><span data-stu-id="2818f-223">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="2818f-224">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-224">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="2818f-225">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"{...}" 명령줄로 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-225">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="2818f-226">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="2818f-226">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="2818f-227">애플리케이션 '{PATH}'를 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-227">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="2818f-228">'{PATH}'에서 실행 파일을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-228">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="2818f-229">애플리케이션 '/LM/W3SVC/2/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8007023e'.</span><span class="sxs-lookup"><span data-stu-id="2818f-229">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="2818f-230">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-230">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="2818f-231">**ASP.NET Core 모듈 디버그 로그:** 이벤트 로그: '애플리케이션 '{PATH}'를 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-231">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="2818f-232">'{PATH}'에서 실행 파일을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-232">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="2818f-233">실패한 HRESULT가 반환되었습니다. 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="2818f-233">Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="2818f-234">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-234">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-235">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-235">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="2818f-236">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-236">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="2818f-237">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="2818f-238">*web.config* 의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 `dotnet`인지 또는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에 대한 `.\{ASSEMBLY}.exe`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-238">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="2818f-239">FDD의 경우 *dotnet.exe* 에서 PATH 설정을 통해 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-239">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="2818f-240">시스템 PATH 설정에 *C:\Program Files\dotnet\\* 이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-240">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="2818f-241">FDD의 경우 *dotnet.exe* 에서 앱 풀의 사용자 ID에 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-241">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="2818f-242">앱 풀 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-242">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="2818f-243">*C:\Program Files\dotnet* 및 앱 디렉터리에 앱 풀 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-243">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="2818f-244">IIS를 다시 시작하지 않은 상태로 FDD를 배포하고 .NET Core를 설치했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-244">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="2818f-245">서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-245">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="2818f-246">호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-246">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="2818f-247">.NET Core 런타임이 설치되어 있지 않으면 시스템에서 **.NET Core 호스팅 번들 설치 관리자** 를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-247">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="2818f-248">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="2818f-248">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="2818f-249">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-249">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="2818f-250">특정 런타임이 필요한 경우 [.NET Download Archives](https://dotnet.microsoft.com/download/archives)에서 런타임을 다운로드하여 시스템에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-250">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="2818f-251">설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-251">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="2818f-252">\<aspNetCore> 요소의 인수가 잘못됨</span><span class="sxs-lookup"><span data-stu-id="2818f-252">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="2818f-253">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-253">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="2818f-254">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-254">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="2818f-255">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-255">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="2818f-256">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-256">Could not find inprocess request handler.</span></span> <span data-ttu-id="2818f-257">hostfxr 호출에서 캡처된 출력: dotnet SDK 명령을 실행하시겠습니까?</span><span class="sxs-lookup"><span data-stu-id="2818f-257">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="2818f-258">다음 위치에서 dotnet SDK를 설치하세요. https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 애플리케이션 '/LM/W3SVC/3/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="2818f-258">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="2818f-259">**ASP.NET Core 모듈 stdout 로그:** dotnet SDK 명령을 실행하시겠습니까?</span><span class="sxs-lookup"><span data-stu-id="2818f-259">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="2818f-260">[https://go.microsoft.com/fwlink/?LinkID=798306&amp;clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 ) 에서 dotnet SDK를 설치하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-260">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="2818f-261">**ASP.NET Core 모듈 디버그 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-261">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="2818f-262">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-262">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="2818f-263">실패한 HRESULT가 반환되었습니다. 0x8000ffff inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-263">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="2818f-264">hostfxr 호출에서 캡처된 출력: dotnet SDK 명령을 실행하시겠습니까?</span><span class="sxs-lookup"><span data-stu-id="2818f-264">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="2818f-265">다음 위치에서 dotnet SDK를 설치하세요. https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 실패한 HRESULT가 반환되었습니다. 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="2818f-265">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="2818f-266">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-266">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-267">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-267">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="2818f-268">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-268">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="2818f-269">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-269">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="2818f-270">*web.config* 의 `<aspNetCore>` 요소에서 *인수* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 `.\{ASSEMBLY}.dll`인지, 또는 (b) 없는 경우 빈 문자열(`arguments=""`)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 목록인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-270">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="2818f-271">.NET Core 공유 프레임워크 누락</span><span class="sxs-lookup"><span data-stu-id="2818f-271">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="2818f-272">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-272">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="2818f-273">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-273">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="2818f-274">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-274">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="2818f-275">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-275">Could not find inprocess request handler.</span></span> <span data-ttu-id="2818f-276">hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-276">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-277">지정된 프레임워크 'Microsoft.AspNetCore.App', 버전 '{VERSION}'을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-277">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="2818f-278">애플리케이션 '/LM/W3SVC/5/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="2818f-278">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="2818f-279">**ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-279">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-280">지정된 프레임워크 'Microsoft.AspNetCore.App', 버전 '{VERSION}'을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-280">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="2818f-281">**ASP.NET Core 모듈 디버그 로그:** 실패한 HRESULT가 반환되었습니다. 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="2818f-281">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="2818f-282">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-282">Troubleshooting:</span></span>

<span data-ttu-id="2818f-283">FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-283">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="2818f-284">중지된 애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="2818f-284">Stopped Application Pool</span></span>

* <span data-ttu-id="2818f-285">**브라우저:** 503 서비스를 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="2818f-285">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="2818f-286">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-286">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-287">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-287">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="2818f-288">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-288">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-289">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-289">Troubleshooting:</span></span>

<span data-ttu-id="2818f-290">애플리케이션 풀이 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-290">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="2818f-291">하위 애플리케이션에 \<handlers> 섹션이 포함됨</span><span class="sxs-lookup"><span data-stu-id="2818f-291">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="2818f-292">**브라우저:** HTTP 오류 500.19 - 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-292">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="2818f-293">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-293">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-294">**ASP.NET Core 모듈 stdout 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-294">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="2818f-295">하위 앱의 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-295">The sub-app's log file isn't created.</span></span>

* <span data-ttu-id="2818f-296">**ASP.NET Core 모듈 디버그 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-296">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="2818f-297">하위 앱의 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-297">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="2818f-298">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-298">Troubleshooting:</span></span>

<span data-ttu-id="2818f-299">하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않거나 하위 앱이 부모 앱의 처리기를 상속하지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-299">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="2818f-300">*web.config* 의 부모 앱 `<system.webServer>` 섹션은 `<location>` 요소 내부에 배치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-300">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="2818f-301"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 부모 앱의 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-301">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="2818f-302">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-302">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="2818f-303">stdout 로그 경로가 올바르지 않음</span><span class="sxs-lookup"><span data-stu-id="2818f-303">stdout log path incorrect</span></span>

* <span data-ttu-id="2818f-304">**브라우저:** 앱이 정상적으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-304">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="2818f-305">**애플리케이션 로그:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-305">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="2818f-306">예외 메시지: HRESULT 0x80070005가 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-306">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="2818f-307">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 중지하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-307">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="2818f-308">예외 메시지: HRESULT 0x80070002가 {PATH}에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-308">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="2818f-309">{PATH}\aspnetcorev2_inprocess.dll에서 stdout 리디렉션을 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-309">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="2818f-310">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-310">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="2818f-311">**ASP.NET Core 모듈 디버그 로그:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-311">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="2818f-312">예외 메시지: HRESULT 0x80070005가 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-312">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="2818f-313">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 중지하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-313">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="2818f-314">예외 메시지: HRESULT 0x80070002가 {PATH}에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-314">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="2818f-315">{PATH}\aspnetcorev2_inprocess.dll에서 stdout 리디렉션을 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-315">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

<span data-ttu-id="2818f-316">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-316">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-317">*web.config* 의 `<aspNetCore>` 요소에 지정된 `stdoutLogFile` 경로가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-317">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="2818f-318">자세한 내용은 [ASP.NET Core 모듈: 로그 만들기 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-318">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="2818f-319">앱 풀 사용자는 stdout 로그 경로에 대한 쓰기 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-319">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="2818f-320">일반적인 애플리케이션 구성 문제</span><span class="sxs-lookup"><span data-stu-id="2818f-320">Application configuration general issue</span></span>

* <span data-ttu-id="2818f-321">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패 **--또는--** HTTP 오류 500.30 - ANCM In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-321">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="2818f-322">**애플리케이션 로그:** 변수</span><span class="sxs-lookup"><span data-stu-id="2818f-322">**Application Log:** Variable</span></span>

* <span data-ttu-id="2818f-323">**ASP.NET Core 모듈 stdout 로그:** 로그 파일은 생성되지만 비어 있거나 앱이 실패할 때까지 일반 항목으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-323">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="2818f-324">**ASP.NET Core 모듈 디버그 로그:** 변수</span><span class="sxs-lookup"><span data-stu-id="2818f-324">**ASP.NET Core Module Debug Log:** Variable</span></span>

<span data-ttu-id="2818f-325">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-325">Troubleshooting:</span></span>

<span data-ttu-id="2818f-326">앱 구성 또는 프로그래밍 문제로 인해 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-326">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="2818f-327">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-327">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2818f-328">이 토픽에서는 일반적인 오류에 대해 설명하고 Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스트할 때 발생되는 특정 오류에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-328">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="2818f-329">일반적인 문제 해결 지침은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-329">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="2818f-330">다음과 같은 정보를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-330">Collect the following information:</span></span>

* <span data-ttu-id="2818f-331">브라우저 동작(상태 코드 및 오류 메시지)</span><span class="sxs-lookup"><span data-stu-id="2818f-331">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="2818f-332">애플리케이션 이벤트 로그 항목</span><span class="sxs-lookup"><span data-stu-id="2818f-332">Application Event Log entries</span></span>
  * <span data-ttu-id="2818f-333">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-333">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="2818f-334">IIS</span><span class="sxs-lookup"><span data-stu-id="2818f-334">IIS</span></span>
    1. <span data-ttu-id="2818f-335">**Windows** 메뉴에서 **시작** 을 선택하고, *이벤트 뷰어* 를 입력하고, **Enter** 를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-335">Select **Start** on the **Windows** menu, type *Event Viewer* , and press **Enter**.</span></span>
    1. <span data-ttu-id="2818f-336">**이벤트 뷰어** 가 열리면 사이드바에서 **Windows 로그** > **애플리케이션** 을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-336">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="2818f-337">ASP.NET Core 모듈 stdout 및 디버그 로그 항목</span><span class="sxs-lookup"><span data-stu-id="2818f-337">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="2818f-338">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-338">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="2818f-339">IIS: ASP.NET Core 모듈 항목의 [로그 생성 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) 및 [향상된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-339">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="2818f-340">오류 정보를 다음 일반 오류와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-340">Compare error information to the following common errors.</span></span> <span data-ttu-id="2818f-341">일치하는 항목이 발견되면 문제 해결 권장 사항을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-341">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="2818f-342">이 항목의 오류 목록은 완전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-342">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="2818f-343">여기에 나열되지 않은 오류가 발생하는 경우 오류를 재현하는 방법에 대한 자세한 지침과 함께 이 항목 하단에 있는 **콘텐츠 피드백** 단추를 사용하여 새 문제를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-343">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="2818f-344">OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨</span><span class="sxs-lookup"><span data-stu-id="2818f-344">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="2818f-345">**애플리케이션 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** 을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-345">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="2818f-346">데이터 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-346">The data is the error.</span></span>

<span data-ttu-id="2818f-347">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-347">Troubleshooting:</span></span>

<span data-ttu-id="2818f-348">OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-348">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="2818f-349">OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음, OS를 업그레이드한 후에 32비트 모드에서 앱 풀을 실행하면 이 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-349">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="2818f-350">OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-350">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="2818f-351">[.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-351">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="2818f-352">설치 관리자가 실행될 때 **복구** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-352">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="2818f-353">사이트 확장 누락, 32비트(x86) 및 64비트(x64) 사이트 확장이 설치됨 또는 잘못된 프로세스 비트 수가 설정됨</span><span class="sxs-lookup"><span data-stu-id="2818f-353">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="2818f-354">Azure App Services에서 호스트하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-354">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="2818f-355">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-355">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="2818f-356">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-356">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="2818f-357">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-357">Could not find inprocess request handler.</span></span> <span data-ttu-id="2818f-358">hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-358">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-359">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-359">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="2818f-360">‘/LM/W3SVC/1416782824/ROOT’ 애플리케이션을 시작하지 못했습니다. 오류 코드 ‘0x8000ffff’.</span><span class="sxs-lookup"><span data-stu-id="2818f-360">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="2818f-361">**ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-361">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="2818f-362">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-362">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="2818f-363">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-363">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-364">미리 보기 런타임에서 앱을 실행하는 경우 앱의 비트 수 및 앱의 런타임 버전과 일치하는 32비트(x86) **또는** 64비트(x64) 사이트 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-364">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="2818f-365">**두 확장을 모두 설치하거나 확장의 여러 런타임 버전을 설치하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="2818f-365">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="2818f-366">ASP.NET Core {RUNTIME VERSION}(x86) 런타임</span><span class="sxs-lookup"><span data-stu-id="2818f-366">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="2818f-367">ASP.NET Core {RUNTIME VERSION}(x64) 런타임</span><span class="sxs-lookup"><span data-stu-id="2818f-367">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="2818f-368">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-368">Restart the app.</span></span> <span data-ttu-id="2818f-369">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-369">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="2818f-370">미리 보기 런타임에서 앱을 실행 중이며 32비트(x86) 및 64비트(x64) [사이트 확장](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)이 둘 다 설치된 경우 앱의 비트 수와 일치하지 않는 사이트 확장을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-370">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="2818f-371">사이트 확장을 제거한 후 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-371">After removing the site extension, restart the app.</span></span> <span data-ttu-id="2818f-372">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-372">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="2818f-373">미리 보기 런타임에서 앱을 실행 중이며 사이트 확장의 비트 수가 앱의 비트 수와 일치하는 경우 미리 보기 사이트 확장의 ‘런타임 버전’이 앱의 런타임 버전과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-373">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="2818f-374">**애플리케이션 설정** 에 있는 앱의 **플랫폼** 이 앱의 비트 수와 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-374">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="2818f-375">자세한 내용은 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-375">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="2818f-376">x86 앱이 배포되었지만 32비트 앱에 대해 앱 풀이 활성화되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-376">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="2818f-377">**브라우저:** HTTP 오류 500.30 - ANCM In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="2818f-377">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="2818f-378">**애플리케이션 로그:** 실제 루트 '{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 예기치 않은 관리 예외, 예외 코드 = '0xe0434352'가 적중했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-378">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="2818f-379">자세한 내용은 stderr 로그를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-379">Please check the stderr logs for more information.</span></span> <span data-ttu-id="2818f-380">실제 루트'{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 clr 및 관리되는 애플리케이션을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-380">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="2818f-381">CLR 작업자 스레드가 조기에 종료됨</span><span class="sxs-lookup"><span data-stu-id="2818f-381">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="2818f-382">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-382">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="2818f-383">이 시나리오는 자체 포함된 앱을 게시할 때 SDK에 의해 트래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-383">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="2818f-384">RID가 플랫폼 대상과 일치하지 않으면 SDK에서 오류가 발생합니다(예: 프로젝트 파일에 `<PlatformTarget>x86</PlatformTarget>`이 있는 `win10-x64` RID).</span><span class="sxs-lookup"><span data-stu-id="2818f-384">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="2818f-385">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-385">Troubleshooting:</span></span>

<span data-ttu-id="2818f-386">x86 프레임워크 종속 배포(`<PlatformTarget>x86</PlatformTarget>`)의 경우 32비트 앱용 IIS 앱 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-386">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="2818f-387">IIS 관리자에서 앱 풀의 **고급 설정** 을 열고 **32비트 앱 사용** 을 **True** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-387">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="2818f-388">플랫폼이 RID와 충돌함</span><span class="sxs-lookup"><span data-stu-id="2818f-388">Platform conflicts with RID</span></span>

* <span data-ttu-id="2818f-389">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-389">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="2818f-390">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="2818f-390">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="2818f-391">**ASP.NET Core 모듈 stdout 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일 또는 어셈블리 '{ASSEMBLY}.dll'을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-391">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="2818f-392">프로그램을 잘못된 형식으로 로드하려고 했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-392">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="2818f-393">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-393">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-394">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-394">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="2818f-395">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-395">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="2818f-396">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-396">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="2818f-397">Azure 앱 배포에서 앱을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-397">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="2818f-398">호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-398">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="2818f-399">URI 엔드포인트가 잘못되었거나 중지된 웹 사이트</span><span class="sxs-lookup"><span data-stu-id="2818f-399">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="2818f-400">**브라우저:** ERR_CONNECTION_REFUSED **--또는--** 연결할 수 없음</span><span class="sxs-lookup"><span data-stu-id="2818f-400">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="2818f-401">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-401">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-402">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-402">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-403">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-403">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-404">사용 중인 앱에 대해 올바른 URI 엔드포인트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-404">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="2818f-405">바인딩을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-405">Check the bindings.</span></span>

* <span data-ttu-id="2818f-406">IIS 웹 사이트가 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-406">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="2818f-407">CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음</span><span class="sxs-lookup"><span data-stu-id="2818f-407">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="2818f-408">**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-408">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="2818f-409">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-409">Troubleshooting:</span></span>

<span data-ttu-id="2818f-410">적절한 역할 및 기능이 사용 가능한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-410">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="2818f-411">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-411">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="2818f-412">잘못된 웹 사이트 실제 경로 또는 누락된 앱</span><span class="sxs-lookup"><span data-stu-id="2818f-412">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="2818f-413">**브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다. **--또는--** 403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-413">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="2818f-414">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-414">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-415">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-415">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-416">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-416">Troubleshooting:</span></span>

<span data-ttu-id="2818f-417">IIS 웹 사이트 **기본 설정** 과 실제 앱 폴더를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-417">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="2818f-418">앱이 IIS 웹 사이트 **실제 경로** 의 폴더에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-418">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="2818f-419">잘못된 역할, 설치되지 않은 ASP.NET Core 모듈 또는 잘못된 권한</span><span class="sxs-lookup"><span data-stu-id="2818f-419">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="2818f-420">**브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-420">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="2818f-421">**--또는--** 이 페이지를 표시할 수 없습니다</span><span class="sxs-lookup"><span data-stu-id="2818f-421">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="2818f-422">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-422">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-423">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-423">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-424">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-424">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-425">적절한 역할을 사용할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-425">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="2818f-426">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-426">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="2818f-427">**프로그램 및 기능** 또는 **앱 및 기능** 을 열고 **Windows Server 호스팅** 이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-427">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="2818f-428">**Windows Server 호스팅** 이 설치된 프로그램 목록에 없는 경우 .NET Core 호스팅 번들을 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-428">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="2818f-429">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="2818f-429">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="2818f-430">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-430">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="2818f-431">**애플리케이션 풀** > **프로세스 모델** > **Identity** 가 **ApplicationPoolIdentity** 로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-431">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="2818f-432">ASP.NET Core 호스팅 번들을 제거하고 이전 버전의 호스팅 번들을 설치하는 경우 *applicationHost.config* 파일에는 ASP.NET Core 모듈에 대한 섹션이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-432">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="2818f-433">*%windir%/System32/inetsrv/config* 에서 *applicationHost.config* 를 열고 `<configuration><configSections><sectionGroup name="system.webServer">` 섹션 그룹을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-433">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="2818f-434">ASP.NET Core 모듈에 대한 섹션이 섹션 그룹에서 누락된 경우 섹션 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-434">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="2818f-435">또는 최신 버전의 ASP.NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-435">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="2818f-436">최신 버전은 지원되는 이전 버전의 ASP.NET Core 앱과 호환 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-436">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="2818f-437">잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반</span><span class="sxs-lookup"><span data-stu-id="2818f-437">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="2818f-438">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-438">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="2818f-439">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"{...}" 명령줄로 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-439">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="2818f-440">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="2818f-440">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="2818f-441">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-441">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="2818f-442">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-442">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-443">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-443">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="2818f-444">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-444">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="2818f-445">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-445">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="2818f-446">*web.config* 의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 `dotnet`인지 또는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에 대한 `.\{ASSEMBLY}.exe`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-446">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="2818f-447">FDD의 경우 *dotnet.exe* 에서 PATH 설정을 통해 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-447">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="2818f-448">시스템 PATH 설정에 *C:\Program Files\dotnet\\* 이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-448">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="2818f-449">FDD의 경우 *dotnet.exe* 에서 앱 풀의 사용자 ID에 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-449">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="2818f-450">앱 풀 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-450">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="2818f-451">*C:\Program Files\dotnet* 및 앱 디렉터리에 앱 풀 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-451">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="2818f-452">IIS를 다시 시작하지 않은 상태로 FDD를 배포하고 .NET Core를 설치했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-452">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="2818f-453">서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-453">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="2818f-454">호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-454">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="2818f-455">.NET Core 런타임이 설치되어 있지 않으면 시스템에서 **.NET Core 호스팅 번들 설치 관리자** 를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-455">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="2818f-456">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="2818f-456">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="2818f-457">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-457">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="2818f-458">특정 런타임이 필요한 경우 [.NET Download Archives](https://dotnet.microsoft.com/download/archives)에서 런타임을 다운로드하여 시스템에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-458">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="2818f-459">설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y** 에 이어 **net start w3svc** 를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-459">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="2818f-460">\<aspNetCore> 요소의 인수가 잘못됨</span><span class="sxs-lookup"><span data-stu-id="2818f-460">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="2818f-461">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-461">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="2818f-462">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"dotnet" .\{ASSEMBLY}.dll' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : 80008081.</span><span class="sxs-lookup"><span data-stu-id="2818f-462">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="2818f-463">**ASP.NET Core 모듈 stdout 로그:** 실행할 애플리케이션이 없습니다. 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="2818f-463">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

<span data-ttu-id="2818f-464">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-464">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-465">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-465">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="2818f-466">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-466">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="2818f-467">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-467">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="2818f-468">*web.config* 의 `<aspNetCore>` 요소에서 *인수* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 `.\{ASSEMBLY}.dll`인지, 또는 (b) 없는 경우 빈 문자열(`arguments=""`)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 목록인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-468">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

<span data-ttu-id="2818f-469">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-469">Troubleshooting:</span></span>

<span data-ttu-id="2818f-470">FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-470">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="2818f-471">중지된 애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="2818f-471">Stopped Application Pool</span></span>

* <span data-ttu-id="2818f-472">**브라우저:** 503 서비스를 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="2818f-472">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="2818f-473">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-473">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-474">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-474">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-475">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-475">Troubleshooting:</span></span>

<span data-ttu-id="2818f-476">애플리케이션 풀이 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-476">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="2818f-477">하위 애플리케이션에 \<handlers> 섹션이 포함됨</span><span class="sxs-lookup"><span data-stu-id="2818f-477">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="2818f-478">**브라우저:** HTTP 오류 500.19 - 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-478">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="2818f-479">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="2818f-479">**Application Log:** No entry</span></span>

* <span data-ttu-id="2818f-480">**ASP.NET Core 모듈 stdout 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-480">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="2818f-481">하위 앱의 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-481">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="2818f-482">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-482">Troubleshooting:</span></span>

<span data-ttu-id="2818f-483">하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-483">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="2818f-484">stdout 로그 경로가 올바르지 않음</span><span class="sxs-lookup"><span data-stu-id="2818f-484">stdout log path incorrect</span></span>

* <span data-ttu-id="2818f-485">**브라우저:** 앱이 정상적으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-485">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="2818f-486">**애플리케이션 로그:** 경고: stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log를 만들지 못했습니다. 오류 코드 = -2147024893.</span><span class="sxs-lookup"><span data-stu-id="2818f-486">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="2818f-487">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-487">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="2818f-488">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-488">Troubleshooting:</span></span>

* <span data-ttu-id="2818f-489">*web.config* 의 `<aspNetCore>` 요소에 지정된 `stdoutLogFile` 경로가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-489">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="2818f-490">자세한 내용은 [ASP.NET Core 모듈: 로그 만들기 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-490">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="2818f-491">앱 풀 사용자는 stdout 로그 경로에 대한 쓰기 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-491">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="2818f-492">일반적인 애플리케이션 구성 문제</span><span class="sxs-lookup"><span data-stu-id="2818f-492">Application configuration general issue</span></span>

* <span data-ttu-id="2818f-493">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="2818f-493">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="2818f-494">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 크래시하거나 응답하지 않거나 수신 대기하지 않습니다. 오류 코드 = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="2818f-494">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="2818f-495">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-495">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="2818f-496">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="2818f-496">Troubleshooting:</span></span>

<span data-ttu-id="2818f-497">앱 구성 또는 프로그래밍 문제로 인해 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2818f-497">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="2818f-498">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2818f-498">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
