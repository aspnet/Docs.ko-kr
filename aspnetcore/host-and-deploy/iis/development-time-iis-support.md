---
title: ASP.NET Core용 Visual Studio의 개발 시간 IIS 지원
author: rick-anderson
description: Windows Server에서 IIS를 통해 실행될 경우 ASP.NET Core 앱 디버그에 대한 지원을 확인해 보세요.
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ab892b2cdfa61378ac7328c0380c8a6cffc6d376
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058456"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="84831-103">ASP.NET Core용 Visual Studio의 개발 시간 IIS 지원</span><span class="sxs-lookup"><span data-stu-id="84831-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="84831-104">작성자: [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="84831-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84831-105">이 문서에서는 Windows Server에서 IIS를 통해 실행되는 ASP.NET Core 앱을 디버그하기 위한 [Visual Studio](https://visualstudio.microsoft.com) 지원에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="84831-106">이 항목에서는 이 시나리오를 사용하도록 설정하고 프로젝트를 설정하는 방법을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84831-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="84831-107">Prerequisites</span></span>

* [<span data-ttu-id="84831-108">Windows용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84831-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="84831-109">**ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="84831-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="84831-110">**.NET Core 플랫폼 간 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="84831-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="84831-111">X.509 보안 인증서(HTTPS 지원용)</span><span class="sxs-lookup"><span data-stu-id="84831-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="84831-112">IIS 활성화</span><span class="sxs-lookup"><span data-stu-id="84831-112">Enable IIS</span></span>

1. <span data-ttu-id="84831-113">Windows에서 **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="84831-114">**인터넷 정보 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="84831-115">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-115">Select **OK**.</span></span>

<span data-ttu-id="84831-116">IIS를 설치하려면 시스템을 다시 시작해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="84831-117">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="84831-117">Configure IIS</span></span>

<span data-ttu-id="84831-118">IIS에서 웹 사이트는 다음과 같이 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="84831-119">**호스트 이름**: 일반적으로 **기본 웹 사이트** 는 `localhost`의 **호스트 이름** 과 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="84831-120">그러나 고유한 호스트 이름이 있는 모든 유효한 IIS 웹 사이트가 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="84831-121">**사이트 바인딩**</span><span class="sxs-lookup"><span data-stu-id="84831-121">**Site Binding**</span></span>
  * <span data-ttu-id="84831-122">HTTPS가 필요한 앱의 경우 인증서를 사용하여 포트 443에 대한 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84831-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="84831-123">일반적으로 **IIS Express 개발 인증서** 가 사용되지만 모든 유효한 인증서가 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="84831-124">HTTP를 사용하는 앱의 경우 포트 80에 대한 바인딩이 있는지 확인하거나 새 사이트에 대해 포트 80에 대한 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84831-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="84831-125">HTTP 또는 HTTPS에 단일 바인딩을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="84831-126">**HTTP 및 HTTPS 포트에 대한 바인딩은 동시에 지원되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="84831-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="84831-127">Visual Studio에서 개발 시간 IIS 지원 사용</span><span class="sxs-lookup"><span data-stu-id="84831-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="84831-128">Visual Studio 설치 관리자를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="84831-129">IIS 개발 시 지원에 사용할 Visual Studio 설치에 대해 **수정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="84831-130">**ASP.NET 및 웹 개발** 워크로드의 경우 **개발 시 IIS 지원** 구성 요소를 찾고 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="84831-131">구성 요소는 워크로드 오른쪽 **설치 세부 정보** 패널의 **개발 시 IIS 지원** 아래 **선택 사항** 섹션에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="84831-132">이 구성 요소는 IIS를 사용하여 ASP.NET Core 앱을 실행하는 데 필요한 네이티브 IIS 모듈인 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="84831-133">프로젝트 구성</span><span class="sxs-lookup"><span data-stu-id="84831-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="84831-134">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="84831-134">HTTPS redirection</span></span>

<span data-ttu-id="84831-135">HTTPS가 필요한 새 프로젝트의 경우 **새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **HTTPS에 대한 구성** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="84831-136">확인란을 선택하면 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 생성될 때 앱에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="84831-137">HTTPS가 필요한 기존 프로젝트의 경우 `Startup.Configure`에서 HTTPS 리디렉션 및 HSTS Middleware를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="84831-138">자세한 내용은 <xref:security/enforcing-ssl>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84831-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="84831-139">HTTP를 사용하는 프로젝트의 경우 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 앱에 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="84831-140">앱 구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="84831-141">IIS 시작 프로필</span><span class="sxs-lookup"><span data-stu-id="84831-141">IIS launch profile</span></span>

<span data-ttu-id="84831-142">새 시작 프로필을 만들어 개발 시간 IIS 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="84831-143">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="84831-144">**속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-144">Select **Properties**.</span></span> <span data-ttu-id="84831-145">**디버그** 탭을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="84831-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="84831-146">**프로필** 의 경우 **새로 만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="84831-147">팝업 창에서 프로필 이름을 “IIS”로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="84831-148">**확인** 을 선택하여 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84831-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="84831-149">**시작** 설정의 경우 목록에서 **IIS** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="84831-150">**브라우저 시작** 확인란을 선택하고 엔드포인트 URL을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="84831-151">앱에 HTTPS가 필요한 경우 HTTPS 엔드포인트(`https://`)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="84831-152">HTTP의 경우 HTTP(`http://`) 엔드포인트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="84831-153">[이전에 지정된 IIS 구성에서 사용하는](#configure-iis) 동일한 호스트 이름 및 포트를 제공합니다(일반적으로 `localhost`).</span><span class="sxs-lookup"><span data-stu-id="84831-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="84831-154">URL의 끝에 있는 앱 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="84831-155">예를 들어 `https://localhost/WebApplication1`(HTTPS) 또는 `http://localhost/WebApplication1`(HTTP)은 유효한 엔드포인트 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="84831-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="84831-156">**환경 변수** 섹션에서 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="84831-157">**이름**`ASPNETCORE_ENVIRONMENT` 및 **값**`Development`를 사용하여 환경 변수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="84831-158">**웹 서버 설정** 영역에서 **앱 URL** 을 **브라우저 시작** 엔드포인트 URL에 사용된 동일한 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="84831-159">Visual Studio 2019 이상에 있는 **호스팅 모델** 설정의 경우 **기본값** 을 선택하여 프로젝트에서 사용되는 호스팅 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="84831-160">프로젝트가 프로젝트 파일에서 `<AspNetCoreHostingModel>` 속성을 설정하면 해당 속성 값(`InProcess` 또는 `OutOfProcess`)이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="84831-161">속성이 없으면 앱의 기본 호스팅 모델인 in-process가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="84831-162">앱의 일반 호스팅 모델과 다른 명시적 호스팅 모델 설정이 앱에 필요한 경우에는 필요에 따라 **호스팅 모델** 을 `In Process` 또는 `Out Of Process`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="84831-163">프로필을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-163">Save the profile.</span></span>

<span data-ttu-id="84831-164">Visual Studio를 사용하지 않는 경우에는 *속성* 폴더의 [launchSettings.json](https://json.schemastore.org/launchsettings) 파일에 시작 프로필을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="84831-165">다음 예제에서는 HTTPS 프로토콜을 사용하도록 프로필을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="84831-166">`applicationUrl` 및 `launchUrl` 엔드포인트가 일치하고 IIS 바인딩 구성과 동일한 프로토콜(HTTP 또는 HTTPS)을 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="84831-167">프로젝트 실행</span><span class="sxs-lookup"><span data-stu-id="84831-167">Run the project</span></span>

<span data-ttu-id="84831-168">관리자 권한으로 Visual Studio를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="84831-169">빌드 구성 드롭다운 목록이 **디버그** 로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="84831-170">[디버깅 시작 단추](/visualstudio/debugger/debugger-feature-tour)를 **IIS** 프로필로 설정하고 단추를 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="84831-171">관리자로 실행하고 있지 않으면 Visual Studio에서 다시 시작하라는 메시지가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="84831-172">메시지가 표시되면 Visual Studio를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="84831-173">신뢰할 수 없는 개발 인증서를 사용하는 경우 브라우저에서 신뢰할 수 없는 인증서에 대한 예외를 만들어야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="84831-174">[내 코드만](/visualstudio/debugger/just-my-code) 및 컴파일러 최적화를 사용하여 릴리스 빌드 구성을 디버그하면 성능이 저하됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="84831-175">예를 들어 중단점이 적중되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84831-176">추가 자료</span><span class="sxs-lookup"><span data-stu-id="84831-176">Additional resources</span></span>

* [<span data-ttu-id="84831-177">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="84831-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84831-178">이 문서에서는 Windows Server에서 IIS를 통해 실행되는 ASP.NET Core 앱을 디버그하기 위한 [Visual Studio](https://visualstudio.microsoft.com) 지원에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="84831-179">이 항목에서는 이 시나리오를 사용하도록 설정하고 프로젝트를 설정하는 방법을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84831-180">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="84831-180">Prerequisites</span></span>

* [<span data-ttu-id="84831-181">Windows용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84831-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="84831-182">**ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="84831-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="84831-183">**.NET Core 플랫폼 간 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="84831-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="84831-184">X.509 보안 인증서(HTTPS 지원용)</span><span class="sxs-lookup"><span data-stu-id="84831-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="84831-185">IIS 활성화</span><span class="sxs-lookup"><span data-stu-id="84831-185">Enable IIS</span></span>

1. <span data-ttu-id="84831-186">Windows에서 **제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="84831-187">**인터넷 정보 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="84831-188">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-188">Select **OK**.</span></span>

<span data-ttu-id="84831-189">IIS를 설치하려면 시스템을 다시 시작해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="84831-190">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="84831-190">Configure IIS</span></span>

<span data-ttu-id="84831-191">IIS에서 웹 사이트는 다음과 같이 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="84831-192">**호스트 이름**: 일반적으로 **기본 웹 사이트** 는 `localhost`의 **호스트 이름** 과 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="84831-193">그러나 고유한 호스트 이름이 있는 모든 유효한 IIS 웹 사이트가 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="84831-194">**사이트 바인딩**</span><span class="sxs-lookup"><span data-stu-id="84831-194">**Site Binding**</span></span>
  * <span data-ttu-id="84831-195">HTTPS가 필요한 앱의 경우 인증서를 사용하여 포트 443에 대한 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84831-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="84831-196">일반적으로 **IIS Express 개발 인증서** 가 사용되지만 모든 유효한 인증서가 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="84831-197">HTTP를 사용하는 앱의 경우 포트 80에 대한 바인딩이 있는지 확인하거나 새 사이트에 대해 포트 80에 대한 바인딩을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84831-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="84831-198">HTTP 또는 HTTPS에 단일 바인딩을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="84831-199">**HTTP 및 HTTPS 포트에 대한 바인딩은 동시에 지원되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="84831-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="84831-200">Visual Studio에서 개발 시간 IIS 지원 사용</span><span class="sxs-lookup"><span data-stu-id="84831-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="84831-201">Visual Studio 설치 관리자를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="84831-202">IIS 개발 시 지원에 사용할 Visual Studio 설치에 대해 **수정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="84831-203">**ASP.NET 및 웹 개발** 워크로드의 경우 **개발 시 IIS 지원** 구성 요소를 찾고 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="84831-204">구성 요소는 워크로드 오른쪽 **설치 세부 정보** 패널의 **개발 시 IIS 지원** 아래 **선택 사항** 섹션에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="84831-205">이 구성 요소는 IIS를 사용하여 ASP.NET Core 앱을 실행하는 데 필요한 네이티브 IIS 모듈인 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="84831-206">프로젝트 구성</span><span class="sxs-lookup"><span data-stu-id="84831-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="84831-207">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="84831-207">HTTPS redirection</span></span>

<span data-ttu-id="84831-208">HTTPS가 필요한 새 프로젝트의 경우 **새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **HTTPS에 대한 구성** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="84831-209">확인란을 선택하면 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 생성될 때 앱에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="84831-210">HTTPS가 필요한 기존 프로젝트의 경우 `Startup.Configure`에서 HTTPS 리디렉션 및 HSTS Middleware를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="84831-211">자세한 내용은 <xref:security/enforcing-ssl>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84831-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="84831-212">HTTP를 사용하는 프로젝트의 경우 [HTTPS 리디렉션 및 HSTS 미들웨어](xref:security/enforcing-ssl)가 앱에 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="84831-213">앱 구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="84831-214">IIS 시작 프로필</span><span class="sxs-lookup"><span data-stu-id="84831-214">IIS launch profile</span></span>

<span data-ttu-id="84831-215">새 시작 프로필을 만들어 개발 시간 IIS 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="84831-216">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="84831-217">**속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-217">Select **Properties**.</span></span> <span data-ttu-id="84831-218">**디버그** 탭을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="84831-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="84831-219">**프로필** 의 경우 **새로 만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="84831-220">팝업 창에서 프로필 이름을 “IIS”로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="84831-221">**확인** 을 선택하여 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84831-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="84831-222">**시작** 설정의 경우 목록에서 **IIS** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="84831-223">**브라우저 시작** 확인란을 선택하고 엔드포인트 URL을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="84831-224">앱에 HTTPS가 필요한 경우 HTTPS 엔드포인트(`https://`)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="84831-225">HTTP의 경우 HTTP(`http://`) 엔드포인트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="84831-226">[이전에 지정된 IIS 구성에서 사용하는](#configure-iis) 동일한 호스트 이름 및 포트를 제공합니다(일반적으로 `localhost`).</span><span class="sxs-lookup"><span data-stu-id="84831-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="84831-227">URL의 끝에 있는 앱 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="84831-228">예를 들어 `https://localhost/WebApplication1`(HTTPS) 또는 `http://localhost/WebApplication1`(HTTP)은 유효한 엔드포인트 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="84831-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="84831-229">**환경 변수** 섹션에서 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="84831-230">**이름**`ASPNETCORE_ENVIRONMENT` 및 **값**`Development`를 사용하여 환경 변수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="84831-231">**웹 서버 설정** 영역에서 **앱 URL** 을 **브라우저 시작** 엔드포인트 URL에 사용된 동일한 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="84831-232">Visual Studio 2019 이상에 있는 **호스팅 모델** 설정의 경우 **기본값** 을 선택하여 프로젝트에서 사용되는 호스팅 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="84831-233">프로젝트가 프로젝트 파일에서 `<AspNetCoreHostingModel>` 속성을 설정하면 해당 속성 값(`InProcess` 또는 `OutOfProcess`)이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="84831-234">속성이 없으면 앱의 기본 호스팅 모델인 out-of-process가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="84831-235">앱의 일반 호스팅 모델과 다른 명시적 호스팅 모델 설정이 앱에 필요한 경우에는 필요에 따라 **호스팅 모델** 을 `In Process` 또는 `Out Of Process`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="84831-236">프로필을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-236">Save the profile.</span></span>

<span data-ttu-id="84831-237">Visual Studio를 사용하지 않는 경우에는 *속성* 폴더의 [launchSettings.json](https://json.schemastore.org/launchsettings) 파일에 시작 프로필을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="84831-238">다음 예제에서는 HTTPS 프로토콜을 사용하도록 프로필을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="84831-239">`applicationUrl` 및 `launchUrl` 엔드포인트가 일치하고 IIS 바인딩 구성과 동일한 프로토콜(HTTP 또는 HTTPS)을 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="84831-240">프로젝트 실행</span><span class="sxs-lookup"><span data-stu-id="84831-240">Run the project</span></span>

<span data-ttu-id="84831-241">관리자 권한으로 Visual Studio를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="84831-242">빌드 구성 드롭다운 목록이 **디버그** 로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="84831-243">[디버깅 시작 단추](/visualstudio/debugger/debugger-feature-tour)를 **IIS** 프로필로 설정하고 단추를 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="84831-244">관리자로 실행하고 있지 않으면 Visual Studio에서 다시 시작하라는 메시지가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="84831-245">메시지가 표시되면 Visual Studio를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="84831-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="84831-246">신뢰할 수 없는 개발 인증서를 사용하는 경우 브라우저에서 신뢰할 수 없는 인증서에 대한 예외를 만들어야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="84831-247">[내 코드만](/visualstudio/debugger/just-my-code) 및 컴파일러 최적화를 사용하여 릴리스 빌드 구성을 디버그하면 성능이 저하됩니다.</span><span class="sxs-lookup"><span data-stu-id="84831-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="84831-248">예를 들어 중단점이 적중되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="84831-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84831-249">추가 자료</span><span class="sxs-lookup"><span data-stu-id="84831-249">Additional resources</span></span>

* [<span data-ttu-id="84831-250">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="84831-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
