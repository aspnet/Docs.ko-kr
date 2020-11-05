---
title: Azure App Service 및 IIS에서 ASP.NET Core 문제 해결
author: rick-anderson
description: ASP.NET Core 앱의 Azure App Service 및 IIS(인터넷 정보 서비스) 배포에 대한 문제 진단 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: d51a4a43f585b0a0b7e3aab2c5de1b2d215de494
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059600"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="141e3-103">Azure App Service 및 IIS에서 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="141e3-104">작성자 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="141e3-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="141e3-105">이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="141e3-106">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="141e3-107">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="141e3-108">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="141e3-109">Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="141e3-110">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="141e3-111">IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="141e3-112">이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="141e3-113">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="141e3-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="141e3-114">주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="141e3-115">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="141e3-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="141e3-116">추가 문제 해결 항목을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="141e3-117">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-117">App startup errors</span></span>

<span data-ttu-id="141e3-118">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="141e3-119">로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패* 는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="141e3-120">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="141e3-120">403.14 Forbidden</span></span>

<span data-ttu-id="141e3-121">앱이 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-121">The app fails to start.</span></span> <span data-ttu-id="141e3-122">다음 오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="141e3-123">이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="141e3-124">앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="141e3-125">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="141e3-126">*web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="141e3-127">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-127">Perform the following steps:</span></span>

1. <span data-ttu-id="141e3-128">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="141e3-129">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="141e3-130">*web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="141e3-131">Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="141e3-132">IIS에서 앱을 호스트하는 경우 **IIS 관리자** 의 **기본 설정** 에 표시되는 IIS **실제 경로** 에 앱이 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager** 's **Basic Settings**.</span></span>
1. <span data-ttu-id="141e3-133">호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="141e3-134">게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="141e3-135">*web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="141e3-136">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-136">500 Internal Server Error</span></span>

<span data-ttu-id="141e3-137">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="141e3-138">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="141e3-139">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="141e3-140">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="141e3-141">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="141e3-142">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="141e3-143">서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="141e3-144">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="141e3-145">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-145">The worker process fails.</span></span> <span data-ttu-id="141e3-146">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-146">The app doesn't start.</span></span>

<span data-ttu-id="141e3-147">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 구성 요소를 로드하는 중 알 수 없는 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="141e3-148">다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-148">Take one of the following actions:</span></span>

* <span data-ttu-id="141e3-149">[Microsoft 지원](https://support.microsoft.com/oas/default.aspx?prid=15832)에 문의하세요( **개발자 도구** 를 선택한 다음, **ASP.NET Core** 선택).</span><span class="sxs-lookup"><span data-stu-id="141e3-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core** ).</span></span>
* <span data-ttu-id="141e3-150">Stack Overflow에 대해 질문하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="141e3-151">[GitHub 리포지토리](https://github.com/dotnet/AspNetCore)에 문제를 제기하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="141e3-152">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="141e3-153">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-153">The worker process fails.</span></span> <span data-ttu-id="141e3-154">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-154">The app doesn't start.</span></span>

<span data-ttu-id="141e3-155">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 .NET Core CLR in-process를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="141e3-156">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="141e3-157">일반적인 오류 조건:</span><span class="sxs-lookup"><span data-stu-id="141e3-157">Common failure conditions:</span></span>

* <span data-ttu-id="141e3-158">존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="141e3-159">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="141e3-160">Azure Key Vault를 사용할 경우 Key Vault에 대한 사용 권한이 부족합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="141e3-161">대상 Key Vault의 액세스 정책을 확인하여 올바른 사용 권한이 부여되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="141e3-162">500.31 ANCM 네이티브 종속성을 찾지 못함</span><span class="sxs-lookup"><span data-stu-id="141e3-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="141e3-163">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-163">The worker process fails.</span></span> <span data-ttu-id="141e3-164">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-164">The app doesn't start.</span></span>

<span data-ttu-id="141e3-165">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="141e3-166">이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="141e3-167">앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="141e3-168">예제 오류 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="141e3-169">오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="141e3-170">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-170">To fix this error, either:</span></span>

* <span data-ttu-id="141e3-171">머신에 적절한 버전의 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="141e3-172">머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="141e3-173">[자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="141e3-174">개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="141e3-175">프로세스 시작 실패의 원인은 애플리케이션 이벤트 로그에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="141e3-176">500.32 ANCM dll을 로드하지 못함</span><span class="sxs-lookup"><span data-stu-id="141e3-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="141e3-177">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-177">The worker process fails.</span></span> <span data-ttu-id="141e3-178">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-178">The app doesn't start.</span></span>

<span data-ttu-id="141e3-179">이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="141e3-180">작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="141e3-181">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-181">To fix this error, either:</span></span>

* <span data-ttu-id="141e3-182">작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="141e3-183">앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="141e3-184">500.33 ANCM 요청 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="141e3-185">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-185">The worker process fails.</span></span> <span data-ttu-id="141e3-186">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-186">The app doesn't start.</span></span>

<span data-ttu-id="141e3-187">앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="141e3-188">`Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는 앱만 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)에서 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="141e3-189">이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="141e3-190">`.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="141e3-191">500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음</span><span class="sxs-lookup"><span data-stu-id="141e3-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="141e3-192">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="141e3-193">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="141e3-194">500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="141e3-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="141e3-195">작업자 프로세스는 동일한 프로세스에서 여러 in-process 앱을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="141e3-196">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="141e3-197">500.36 ANCM Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="141e3-198">Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll* 이 *aspnetcorev2.dll* 파일 옆에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll* , isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="141e3-199">이는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 손상된 설치를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="141e3-200">이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="141e3-201">500.37 ANCM 시작 시간 제한 내에 시작하지 못함</span><span class="sxs-lookup"><span data-stu-id="141e3-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="141e3-202">제공된 시작 시간 제한 내에 ANCM을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-202">ANCM failed to start within the provided startup time limit.</span></span> <span data-ttu-id="141e3-203">기본적으로 제한 시간은 120초입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="141e3-204">이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="141e3-205">시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="141e3-206">여러 앱의 시작 프로세스를 분산해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="141e3-207">500.38 ANCM 애플리케이션 DLL을 찾을 수 없음</span><span class="sxs-lookup"><span data-stu-id="141e3-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="141e3-208">ANCM에서 실행 파일 옆에 있어야 하는 애플리케이션 DLL을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="141e3-209">이 오류는 [단일 파일 실행 파일](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)로 패키지된 앱을 In-process 호스팅 모델을 사용하여 호스트할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="141e3-210">In-process 모델을 사용하려면 ANCM에서 .NET Core 앱을 기존 IIS 프로세스로 로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="141e3-211">단일 파일 배포 모델에서는 이 시나리오가 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="141e3-212">앱의 프로젝트 파일에서 다음 방법 중 **하나** 를 사용하여 이 오류를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="141e3-213">`PublishSingleFile` MSBuild 속성을 `false`로 설정하여 단일 파일 게시를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="141e3-214">`AspNetCoreHostingModel` MSBuild 속성을 `OutOfProcess`로 설정하여 out-of-process 호스팅 모델로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="141e3-215">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-215">502.5 Process Failure</span></span>

<span data-ttu-id="141e3-216">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-216">The worker process fails.</span></span> <span data-ttu-id="141e3-217">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-217">The app doesn't start.</span></span>

<span data-ttu-id="141e3-218">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="141e3-219">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="141e3-220">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="141e3-221">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="141e3-222">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-222">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="141e3-223">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="141e3-224">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="141e3-225">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="141e3-226">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="141e3-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="141e3-227">앱의 어셈블리( *.dll* )를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-227">The app failed to start because the app's assembly ( *.dll* ) couldn't be loaded.</span></span>

<span data-ttu-id="141e3-228">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="141e3-229">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="141e3-230">IIS 관리자의 **애플리케이션 풀** 에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="141e3-231">**작업** 패널의 **애플리케이션 풀 편집** 에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="141e3-232">**32비트 애플리케이션 사용** 을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-232">Set **Enable 32-Bit Applications** :</span></span>
   * <span data-ttu-id="141e3-233">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="141e3-234">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="141e3-235">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="141e3-236">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="141e3-236">Connection reset</span></span>

<span data-ttu-id="141e3-237">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류** 를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="141e3-238">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="141e3-239">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="141e3-240">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="141e3-241">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="141e3-241">Default startup limits</span></span>

<span data-ttu-id="141e3-242">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit* 가 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="141e3-243">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="141e3-244">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="141e3-245">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="141e3-246">애플리케이션 이벤트 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="141e3-247">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="141e3-248">Azure Portal에서 **Azure App Services** 의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="141e3-249">**문제 진단 및 해결** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="141e3-250">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="141e3-251">**지원 도구** 에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-251">Under **Support Tools** , select the **Application Events** button.</span></span>
1. <span data-ttu-id="141e3-252">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="141e3-253">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="141e3-254">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-255">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-256">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-257">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-258">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="141e3-259">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="141e3-260">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-260">Examine the log.</span></span> <span data-ttu-id="141e3-261">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="141e3-262">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="141e3-263">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="141e3-264">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="141e3-265">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-266">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-267">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-268">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="141e3-269">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="141e3-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="141e3-270">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="141e3-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="141e3-271">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-271">Run the app:</span></span>
   * <span data-ttu-id="141e3-272">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="141e3-273">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="141e3-274">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="141e3-275">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="141e3-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="141e3-276">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="141e3-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="141e3-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="141e3-278">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="141e3-279">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="141e3-280">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="141e3-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="141e3-281">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="141e3-281">**Current release**</span></span>

* <span data-ttu-id="141e3-282">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="141e3-283">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="141e3-284">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="141e3-285">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="141e3-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="141e3-286">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="141e3-287">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="141e3-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="141e3-288">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="141e3-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="141e3-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="141e3-290">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="141e3-291">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="141e3-292">ASP.NET Core 모듈 stdout 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-293">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-294">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="141e3-295">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="141e3-296">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-297">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="141e3-298">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="141e3-299">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-300">Azure Portal에서 웹앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="141e3-301">**App Service** 블레이드에서 검색 상자에 **kudu** 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="141e3-302">**고급 도구** > **이동** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="141e3-303">**디버그 콘솔 > CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="141e3-304">*site/wwwroot* 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="141e3-305">연필 아이콘을 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-306">`<aspNetCore />` 요소에서 `stdoutLogEnabled="true"`를 설정하고 **저장** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="141e3-307">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 `stdoutLogEnabled="false"`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="141e3-308">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="141e3-309">ASP.NET Core 모듈 디버그 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="141e3-310">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="141e3-311">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-312">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="141e3-313">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="141e3-314">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-314">Redeploy the app.</span></span>
   * <span data-ttu-id="141e3-315">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="141e3-316">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-317">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-318">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="141e3-319">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="141e3-320">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="141e3-321">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="141e3-322">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="141e3-323">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="141e3-324">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-325">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-326">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-327">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-328">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="141e3-329">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="141e3-330">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="141e3-331">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="141e3-332">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="141e3-333">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="141e3-334">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="141e3-335">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="141e3-336">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-336">Save the file.</span></span>

<span data-ttu-id="141e3-337">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-338">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-339">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-339">There's no limit on log file size.</span></span> <span data-ttu-id="141e3-340">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="141e3-341">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-342">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="141e3-343">느리거나 중단된 앱(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="141e3-344">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="141e3-345">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="141e3-346">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="141e3-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="141e3-347">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="141e3-347">Monitoring blades</span></span>

<span data-ttu-id="141e3-348">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="141e3-349">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="141e3-350">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="141e3-351">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="141e3-352">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="141e3-353">**ASP.NET Core 확장** 이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="141e3-354">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="141e3-355">목록에서 **ASP.NET Core 확장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="141e3-356">**확인** 을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="141e3-357">**확장 추가** 블레이드에서 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="141e3-358">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="141e3-359">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="141e3-360">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="141e3-361">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-362">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-363">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-364">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="141e3-365">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-366">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="141e3-367">**저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="141e3-368">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="141e3-369">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="141e3-370">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지** 에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="141e3-371">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="141e3-372">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적** 에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="141e3-373">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="141e3-374">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-374">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-375">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="141e3-376">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="141e3-377">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="141e3-378">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="141e3-379">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="141e3-380">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="141e3-381">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-382">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-383">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="141e3-384">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-385">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="141e3-386">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="141e3-387">애플리케이션 이벤트 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="141e3-388">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="141e3-389">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-389">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="141e3-390">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-390">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="141e3-391">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="141e3-392">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="141e3-393">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="141e3-394">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-394">Run the app at a command prompt</span></span>

<span data-ttu-id="141e3-395">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="141e3-396">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="141e3-397">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="141e3-397">Framework-dependent deployment</span></span>

<span data-ttu-id="141e3-398">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="141e3-399">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe* 로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="141e3-400">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="141e3-401">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="141e3-402">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="141e3-403">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="141e3-404">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="141e3-405">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="141e3-405">Self-contained deployment</span></span>

<span data-ttu-id="141e3-406">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="141e3-407">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="141e3-408">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="141e3-409">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="141e3-410">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="141e3-411">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="141e3-412">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="141e3-413">ASP.NET Core 모듈 stdout 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="141e3-414">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-415">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="141e3-416">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="141e3-417">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="141e3-418">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-418">Edit the *web.config* file.</span></span> <span data-ttu-id="141e3-419">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="141e3-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="141e3-420">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="141e3-421">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="141e3-422">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log* 로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="141e3-423">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="141e3-424">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="141e3-425">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-425">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-426">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="141e3-427">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="141e3-428">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-428">Study the log for errors.</span></span>

<span data-ttu-id="141e3-429">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="141e3-430">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-431">**stdoutLogEnabled** 를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="141e3-432">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-432">Save the file.</span></span>

<span data-ttu-id="141e3-433">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-434">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-435">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="141e3-436">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-437">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="141e3-438">ASP.NET Core 모듈 디버그 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="141e3-439">앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="141e3-440">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="141e3-441">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="141e3-442">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="141e3-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="141e3-443">`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="141e3-444">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="141e3-445">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="141e3-446">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="141e3-447">*web.config* 에서 환경 변수를 설정하는 방법에 대한 자세한 내용은 [aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-447">For information on setting environment variables in *web.config* , see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="141e3-448">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="141e3-448">Obtain data from an app</span></span>

<span data-ttu-id="141e3-449">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="141e3-450">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="141e3-451">앱이 느리거나 중단됨(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="141e3-452">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="141e3-453">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="141e3-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="141e3-454">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="141e3-455">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="141e3-456">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="141e3-457">[EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="141e3-458">앱에서 [ 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="141e3-459">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="141e3-460">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="141e3-461">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="141e3-462">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="141e3-463">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="141e3-464">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="141e3-465">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-466">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="141e3-467">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="141e3-468">앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="141e3-469">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="141e3-469">Analyze the dump</span></span>

<span data-ttu-id="141e3-470">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="141e3-471">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="141e3-472">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="141e3-472">Clear package caches</span></span>

<span data-ttu-id="141e3-473">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="141e3-474">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="141e3-475">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="141e3-476">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="141e3-477">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="141e3-478">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="141e3-479">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="141e3-480">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="141e3-481">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="141e3-482">추가 자료</span><span class="sxs-lookup"><span data-stu-id="141e3-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="141e3-483">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-483">Azure documentation</span></span>

* [<span data-ttu-id="141e3-484">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="141e3-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="141e3-485">Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션</span><span class="sxs-lookup"><span data-stu-id="141e3-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="141e3-486">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="141e3-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="141e3-487">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="141e3-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="141e3-488">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="141e3-489">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="141e3-490">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="141e3-491">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="141e3-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="141e3-492">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="141e3-492">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="141e3-493">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="141e3-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="141e3-494">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="141e3-495">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141e3-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="141e3-496">Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141e3-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="141e3-497">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="141e3-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="141e3-498">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-498">Visual Studio Code documentation</span></span>

* <span data-ttu-id="141e3-499">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="141e3-499">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="141e3-500">이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="141e3-501">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="141e3-502">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="141e3-503">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="141e3-504">Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="141e3-505">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="141e3-506">IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="141e3-507">이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="141e3-508">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="141e3-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="141e3-509">주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="141e3-510">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="141e3-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="141e3-511">추가 문제 해결 항목을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="141e3-512">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-512">App startup errors</span></span>

<span data-ttu-id="141e3-513">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="141e3-514">로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패* 는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="141e3-515">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="141e3-515">403.14 Forbidden</span></span>

<span data-ttu-id="141e3-516">앱이 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-516">The app fails to start.</span></span> <span data-ttu-id="141e3-517">다음 오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="141e3-518">이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="141e3-519">앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="141e3-520">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="141e3-521">*web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="141e3-522">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-522">Perform the following steps:</span></span>

1. <span data-ttu-id="141e3-523">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="141e3-524">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="141e3-525">*web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="141e3-526">Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="141e3-527">IIS에서 앱을 호스트하는 경우 **IIS 관리자** 의 **기본 설정** 에 표시되는 IIS **실제 경로** 에 앱이 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager** 's **Basic Settings**.</span></span>
1. <span data-ttu-id="141e3-528">호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="141e3-529">게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="141e3-530">*web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="141e3-531">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-531">500 Internal Server Error</span></span>

<span data-ttu-id="141e3-532">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="141e3-533">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="141e3-534">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="141e3-535">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="141e3-536">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="141e3-537">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="141e3-538">서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="141e3-539">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="141e3-540">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-540">The worker process fails.</span></span> <span data-ttu-id="141e3-541">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-541">The app doesn't start.</span></span>

<span data-ttu-id="141e3-542">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 .NET Core CLR을 찾지 못하고 in-process 요청 처리기( *aspnetcorev2_inprocess.dll* )를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler ( *aspnetcorev2_inprocess.dll* ).</span></span> <span data-ttu-id="141e3-543">다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-543">Check that:</span></span>

* <span data-ttu-id="141e3-544">앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="141e3-545">앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="141e3-546">500.0 Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="141e3-547">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-547">The worker process fails.</span></span> <span data-ttu-id="141e3-548">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-548">The app doesn't start.</span></span>

<span data-ttu-id="141e3-549">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 out-of-process 호스팅 요청 처리기를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="141e3-550">*aspnetcorev2_outofprocess.dll* 이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="141e3-551">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-551">502.5 Process Failure</span></span>

<span data-ttu-id="141e3-552">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-552">The worker process fails.</span></span> <span data-ttu-id="141e3-553">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-553">The app doesn't start.</span></span>

<span data-ttu-id="141e3-554">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="141e3-555">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="141e3-556">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="141e3-557">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="141e3-558">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-558">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="141e3-559">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="141e3-560">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="141e3-561">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="141e3-562">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="141e3-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="141e3-563">앱의 어셈블리( *.dll* )를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-563">The app failed to start because the app's assembly ( *.dll* ) couldn't be loaded.</span></span>

<span data-ttu-id="141e3-564">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="141e3-565">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="141e3-566">IIS 관리자의 **애플리케이션 풀** 에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="141e3-567">**작업** 패널의 **애플리케이션 풀 편집** 에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="141e3-568">**32비트 애플리케이션 사용** 을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-568">Set **Enable 32-Bit Applications** :</span></span>
   * <span data-ttu-id="141e3-569">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="141e3-570">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="141e3-571">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="141e3-572">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="141e3-572">Connection reset</span></span>

<span data-ttu-id="141e3-573">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류** 를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="141e3-574">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="141e3-575">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="141e3-576">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="141e3-577">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="141e3-577">Default startup limits</span></span>

<span data-ttu-id="141e3-578">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit* 가 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="141e3-579">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="141e3-580">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="141e3-581">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="141e3-582">애플리케이션 이벤트 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="141e3-583">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="141e3-584">Azure Portal에서 **Azure App Services** 의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="141e3-585">**문제 진단 및 해결** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="141e3-586">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="141e3-587">**지원 도구** 에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-587">Under **Support Tools** , select the **Application Events** button.</span></span>
1. <span data-ttu-id="141e3-588">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="141e3-589">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="141e3-590">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-591">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-592">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-593">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-594">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="141e3-595">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="141e3-596">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-596">Examine the log.</span></span> <span data-ttu-id="141e3-597">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="141e3-598">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="141e3-599">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="141e3-600">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="141e3-601">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-602">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-603">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-604">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="141e3-605">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="141e3-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="141e3-606">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="141e3-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="141e3-607">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-607">Run the app:</span></span>
   * <span data-ttu-id="141e3-608">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="141e3-609">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="141e3-610">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="141e3-611">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="141e3-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="141e3-612">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="141e3-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="141e3-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="141e3-614">앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="141e3-615">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="141e3-616">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="141e3-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="141e3-617">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="141e3-617">**Current release**</span></span>

* <span data-ttu-id="141e3-618">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="141e3-619">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="141e3-620">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="141e3-621">앱을 실행합니다. `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="141e3-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="141e3-622">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="141e3-623">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="141e3-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="141e3-624">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="141e3-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="141e3-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="141e3-626">앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="141e3-627">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="141e3-628">ASP.NET Core 모듈 stdout 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="141e3-629">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="141e3-630">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-631">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="141e3-632">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-632">Under **SELECT PROBLEM CATEGORY** , select the **Web App Down** button.</span></span>
1. <span data-ttu-id="141e3-633">**추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection** , select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="141e3-634">Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-634">In the Kudu **Diagnostic Console** , open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="141e3-635">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="141e3-636">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-637">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="141e3-638">**저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="141e3-639">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-639">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-640">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-640">Return to the Azure portal.</span></span> <span data-ttu-id="141e3-641">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="141e3-642">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-643">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-644">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-645">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="141e3-646">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="141e3-647">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="141e3-648">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="141e3-649">Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-649">In the Kudu **Diagnostic Console** , return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="141e3-650">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="141e3-651">**stdoutLogEnabled** 를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="141e3-652">**저장** 을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="141e3-653">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-654">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-655">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="141e3-656">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="141e3-657">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-658">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="141e3-659">ASP.NET Core 모듈 디버그 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="141e3-660">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="141e3-661">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-662">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="141e3-663">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="141e3-664">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-664">Redeploy the app.</span></span>
   * <span data-ttu-id="141e3-665">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="141e3-666">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-667">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-668">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="141e3-669">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="141e3-670">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="141e3-671">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="141e3-672">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="141e3-673">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="141e3-674">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-675">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-676">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-677">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-678">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="141e3-679">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="141e3-680">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="141e3-681">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="141e3-682">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="141e3-683">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="141e3-684">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="141e3-685">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="141e3-686">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-686">Save the file.</span></span>

<span data-ttu-id="141e3-687">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-688">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-689">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-689">There's no limit on log file size.</span></span> <span data-ttu-id="141e3-690">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="141e3-691">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-692">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="141e3-693">느리거나 중단된 앱(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="141e3-694">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="141e3-695">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="141e3-696">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="141e3-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="141e3-697">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="141e3-697">Monitoring blades</span></span>

<span data-ttu-id="141e3-698">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="141e3-699">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="141e3-700">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="141e3-701">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="141e3-702">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="141e3-703">**ASP.NET Core 확장** 이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="141e3-704">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="141e3-705">목록에서 **ASP.NET Core 확장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="141e3-706">**확인** 을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="141e3-707">**확장 추가** 블레이드에서 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="141e3-708">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="141e3-709">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="141e3-710">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="141e3-711">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-712">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-713">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-714">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="141e3-715">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-716">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="141e3-717">**저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="141e3-718">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="141e3-719">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="141e3-720">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지** 에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="141e3-721">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="141e3-722">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적** 에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="141e3-723">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="141e3-724">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-724">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-725">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="141e3-726">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="141e3-727">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="141e3-728">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="141e3-729">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="141e3-730">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="141e3-731">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-732">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-733">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="141e3-734">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-735">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="141e3-736">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="141e3-737">애플리케이션 이벤트 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="141e3-738">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="141e3-739">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-739">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="141e3-740">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-740">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="141e3-741">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="141e3-742">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="141e3-743">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="141e3-744">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-744">Run the app at a command prompt</span></span>

<span data-ttu-id="141e3-745">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="141e3-746">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="141e3-747">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="141e3-747">Framework-dependent deployment</span></span>

<span data-ttu-id="141e3-748">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="141e3-749">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe* 로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="141e3-750">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="141e3-751">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="141e3-752">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="141e3-753">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="141e3-754">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="141e3-755">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="141e3-755">Self-contained deployment</span></span>

<span data-ttu-id="141e3-756">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="141e3-757">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="141e3-758">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="141e3-759">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="141e3-760">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="141e3-761">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="141e3-762">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="141e3-763">ASP.NET Core 모듈 stdout 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="141e3-764">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-765">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="141e3-766">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="141e3-767">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="141e3-768">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-768">Edit the *web.config* file.</span></span> <span data-ttu-id="141e3-769">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="141e3-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="141e3-770">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="141e3-771">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="141e3-772">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log* 로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="141e3-773">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="141e3-774">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="141e3-775">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-775">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-776">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="141e3-777">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="141e3-778">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-778">Study the log for errors.</span></span>

<span data-ttu-id="141e3-779">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="141e3-780">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-781">**stdoutLogEnabled** 를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="141e3-782">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-782">Save the file.</span></span>

<span data-ttu-id="141e3-783">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-784">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-785">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="141e3-786">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-787">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="141e3-788">ASP.NET Core 모듈 디버그 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="141e3-789">앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="141e3-790">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="141e3-791">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="141e3-792">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="141e3-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="141e3-793">`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="141e3-794">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="141e3-795">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="141e3-796">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="141e3-797">*web.config* 에서 환경 변수를 설정하는 방법에 대한 자세한 내용은 [aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-797">For information on setting environment variables in *web.config* , see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="141e3-798">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="141e3-798">Obtain data from an app</span></span>

<span data-ttu-id="141e3-799">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="141e3-800">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="141e3-801">앱이 느리거나 중단됨(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="141e3-802">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="141e3-803">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="141e3-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="141e3-804">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="141e3-805">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="141e3-806">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="141e3-807">[EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="141e3-808">앱에서 [ 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="141e3-809">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="141e3-810">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="141e3-811">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="141e3-812">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="141e3-813">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="141e3-814">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="141e3-815">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-816">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="141e3-817">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="141e3-818">앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="141e3-819">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="141e3-819">Analyze the dump</span></span>

<span data-ttu-id="141e3-820">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="141e3-821">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="141e3-822">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="141e3-822">Clear package caches</span></span>

<span data-ttu-id="141e3-823">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="141e3-824">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="141e3-825">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="141e3-826">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="141e3-827">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="141e3-828">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="141e3-829">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="141e3-830">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="141e3-831">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="141e3-832">추가 자료</span><span class="sxs-lookup"><span data-stu-id="141e3-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="141e3-833">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-833">Azure documentation</span></span>

* [<span data-ttu-id="141e3-834">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="141e3-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="141e3-835">Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션</span><span class="sxs-lookup"><span data-stu-id="141e3-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="141e3-836">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="141e3-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="141e3-837">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="141e3-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="141e3-838">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="141e3-839">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="141e3-840">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="141e3-841">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="141e3-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="141e3-842">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="141e3-842">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="141e3-843">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="141e3-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="141e3-844">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="141e3-845">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141e3-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="141e3-846">Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141e3-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="141e3-847">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="141e3-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="141e3-848">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-848">Visual Studio Code documentation</span></span>

* <span data-ttu-id="141e3-849">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="141e3-849">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="141e3-850">이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="141e3-851">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="141e3-852">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="141e3-853">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="141e3-854">Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="141e3-855">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="141e3-856">IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="141e3-857">이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="141e3-858">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="141e3-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="141e3-859">주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="141e3-860">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="141e3-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="141e3-861">추가 문제 해결 항목을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="141e3-862">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-862">App startup errors</span></span>

<span data-ttu-id="141e3-863">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="141e3-864">로컬에서 디버그할 때 발생하는 *502.5 프로세스 실패* 는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="141e3-865">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="141e3-865">403.14 Forbidden</span></span>

<span data-ttu-id="141e3-866">앱이 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-866">The app fails to start.</span></span> <span data-ttu-id="141e3-867">다음 오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="141e3-868">이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="141e3-869">앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="141e3-870">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="141e3-871">*web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="141e3-872">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-872">Perform the following steps:</span></span>

1. <span data-ttu-id="141e3-873">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="141e3-874">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="141e3-875">*web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="141e3-876">Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="141e3-877">IIS에서 앱을 호스트하는 경우 **IIS 관리자** 의 **기본 설정** 에 표시되는 IIS **실제 경로** 에 앱이 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager** 's **Basic Settings**.</span></span>
1. <span data-ttu-id="141e3-878">호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="141e3-879">게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="141e3-880">*web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="141e3-881">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="141e3-881">500 Internal Server Error</span></span>

<span data-ttu-id="141e3-882">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="141e3-883">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="141e3-884">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="141e3-885">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="141e3-886">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="141e3-887">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="141e3-888">서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="141e3-889">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="141e3-889">502.5 Process Failure</span></span>

<span data-ttu-id="141e3-890">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-890">The worker process fails.</span></span> <span data-ttu-id="141e3-891">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-891">The app doesn't start.</span></span>

<span data-ttu-id="141e3-892">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="141e3-893">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="141e3-894">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="141e3-895">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="141e3-896">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-896">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="141e3-897">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="141e3-898">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="141e3-899">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="141e3-900">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="141e3-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="141e3-901">앱의 어셈블리( *.dll* )를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-901">The app failed to start because the app's assembly ( *.dll* ) couldn't be loaded.</span></span>

<span data-ttu-id="141e3-902">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="141e3-903">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="141e3-904">IIS 관리자의 **애플리케이션 풀** 에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="141e3-905">**작업** 패널의 **애플리케이션 풀 편집** 에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="141e3-906">**32비트 애플리케이션 사용** 을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-906">Set **Enable 32-Bit Applications** :</span></span>
   * <span data-ttu-id="141e3-907">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="141e3-908">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="141e3-909">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="141e3-910">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="141e3-910">Connection reset</span></span>

<span data-ttu-id="141e3-911">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류** 를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="141e3-912">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="141e3-913">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="141e3-914">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="141e3-915">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="141e3-915">Default startup limits</span></span>

<span data-ttu-id="141e3-916">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit* 가 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="141e3-917">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="141e3-918">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="141e3-919">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="141e3-920">애플리케이션 이벤트 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="141e3-921">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="141e3-922">Azure Portal에서 **Azure App Services** 의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="141e3-923">**문제 진단 및 해결** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="141e3-924">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="141e3-925">**지원 도구** 에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-925">Under **Support Tools** , select the **Application Events** button.</span></span>
1. <span data-ttu-id="141e3-926">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="141e3-927">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="141e3-928">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-929">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-930">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-931">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-932">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="141e3-933">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="141e3-934">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-934">Examine the log.</span></span> <span data-ttu-id="141e3-935">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="141e3-936">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="141e3-937">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="141e3-938">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="141e3-939">**개발 도구** 영역에서 **고급 도구** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="141e3-940">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-941">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-942">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="141e3-943">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="141e3-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="141e3-944">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="141e3-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="141e3-945">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-945">Run the app:</span></span>
   * <span data-ttu-id="141e3-946">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="141e3-947">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="141e3-948">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="141e3-949">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="141e3-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="141e3-950">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="141e3-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="141e3-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="141e3-952">앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="141e3-953">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="141e3-954">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="141e3-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="141e3-955">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="141e3-955">**Current release**</span></span>

* <span data-ttu-id="141e3-956">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="141e3-957">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="141e3-958">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="141e3-959">앱을 실행합니다. `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="141e3-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="141e3-960">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔로 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="141e3-961">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="141e3-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="141e3-962">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="141e3-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="141e3-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="141e3-964">앱을 실행합니다. `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="141e3-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="141e3-965">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="141e3-966">ASP.NET Core 모듈 stdout 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="141e3-967">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="141e3-968">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-969">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="141e3-970">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-970">Under **SELECT PROBLEM CATEGORY** , select the **Web App Down** button.</span></span>
1. <span data-ttu-id="141e3-971">**추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection** , select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="141e3-972">Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-972">In the Kudu **Diagnostic Console** , open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="141e3-973">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="141e3-974">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-975">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="141e3-976">**저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="141e3-977">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-977">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-978">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-978">Return to the Azure portal.</span></span> <span data-ttu-id="141e3-979">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="141e3-980">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-981">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-982">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-983">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="141e3-984">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="141e3-985">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="141e3-986">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="141e3-987">Kudu **진단 콘솔** 에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-987">In the Kudu **Diagnostic Console** , return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="141e3-988">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="141e3-989">**stdoutLogEnabled** 를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="141e3-990">**저장** 을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="141e3-991">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-992">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-993">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="141e3-994">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="141e3-995">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-996">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="141e3-997">느리거나 중단된 앱(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="141e3-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="141e3-998">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="141e3-999">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="141e3-1000">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="141e3-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="141e3-1001">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="141e3-1001">Monitoring blades</span></span>

<span data-ttu-id="141e3-1002">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="141e3-1003">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="141e3-1004">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="141e3-1005">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="141e3-1006">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="141e3-1007">**ASP.NET Core 확장** 이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="141e3-1008">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="141e3-1009">목록에서 **ASP.NET Core 확장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="141e3-1010">**확인** 을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="141e3-1011">**확장 추가** 블레이드에서 **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="141e3-1012">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="141e3-1013">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="141e3-1014">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="141e3-1015">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="141e3-1016">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="141e3-1017">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔** 을 열고 **CMD** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="141e3-1018">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="141e3-1019">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-1020">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="141e3-1021">**저장** 을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="141e3-1022">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="141e3-1023">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="141e3-1024">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지** 에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="141e3-1025">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="141e3-1026">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적** 에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="141e3-1027">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="141e3-1028">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-1029">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="141e3-1030">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="141e3-1031">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="141e3-1032">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="141e3-1033">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="141e3-1034">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="141e3-1035">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-1036">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-1037">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="141e3-1038">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-1039">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="141e3-1040">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="141e3-1041">애플리케이션 이벤트 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="141e3-1042">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="141e3-1043">시작 메뉴를 열고 *이벤트 뷰어* 를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1043">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="141e3-1044">**이벤트 뷰어** 에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1044">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="141e3-1045">**애플리케이션** 을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="141e3-1046">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="141e3-1047">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="141e3-1048">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="141e3-1049">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="141e3-1050">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="141e3-1051">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="141e3-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="141e3-1052">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="141e3-1053">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe* 로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="141e3-1054">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="141e3-1055">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="141e3-1056">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="141e3-1057">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="141e3-1058">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="141e3-1059">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="141e3-1059">Self-contained deployment</span></span>

<span data-ttu-id="141e3-1060">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="141e3-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="141e3-1061">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="141e3-1062">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="141e3-1063">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="141e3-1064">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="141e3-1065">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="141e3-1066">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="141e3-1067">ASP.NET Core 모듈 stdout 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="141e3-1068">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="141e3-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="141e3-1069">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="141e3-1070">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="141e3-1071">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="141e3-1072">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="141e3-1073">**stdoutLogEnabled** 를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="141e3-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="141e3-1074">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="141e3-1075">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="141e3-1076">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log* 로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="141e3-1077">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="141e3-1078">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="141e3-1079">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="141e3-1080">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="141e3-1081">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="141e3-1082">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1082">Study the log for errors.</span></span>

<span data-ttu-id="141e3-1083">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="141e3-1084">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="141e3-1085">**stdoutLogEnabled** 를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="141e3-1086">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1086">Save the file.</span></span>

<span data-ttu-id="141e3-1087">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-1088">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="141e3-1089">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="141e3-1090">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="141e3-1091">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="141e3-1092">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="141e3-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="141e3-1093">`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="141e3-1094">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="141e3-1095">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="141e3-1096">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="141e3-1097">*web.config* 에서 환경 변수를 설정하는 방법에 대한 자세한 내용은 [aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1097">For information on setting environment variables in *web.config* , see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="141e3-1098">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="141e3-1098">Obtain data from an app</span></span>

<span data-ttu-id="141e3-1099">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="141e3-1100">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="141e3-1101">앱이 느리거나 중단됨(IIS)</span><span class="sxs-lookup"><span data-stu-id="141e3-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="141e3-1102">*크래시 덤프* 는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="141e3-1103">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="141e3-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="141e3-1104">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="141e3-1105">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="141e3-1106">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="141e3-1107">[EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="141e3-1108">앱에서 [ 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="141e3-1109">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="141e3-1110">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="141e3-1111">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="141e3-1112">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe* :</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="141e3-1113">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe* 에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="141e3-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe* :</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="141e3-1114">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="141e3-1115">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="141e3-1116">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="141e3-1117">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="141e3-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="141e3-1118">앱이 *중단* (응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="141e3-1119">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="141e3-1119">Analyze the dump</span></span>

<span data-ttu-id="141e3-1120">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="141e3-1121">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="141e3-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="141e3-1122">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="141e3-1122">Clear package caches</span></span>

<span data-ttu-id="141e3-1123">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="141e3-1124">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="141e3-1125">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="141e3-1126">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="141e3-1127">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="141e3-1128">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="141e3-1129">*nuget.exe* 는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="141e3-1130">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="141e3-1131">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="141e3-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="141e3-1132">추가 자료</span><span class="sxs-lookup"><span data-stu-id="141e3-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="141e3-1133">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-1133">Azure documentation</span></span>

* [<span data-ttu-id="141e3-1134">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="141e3-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="141e3-1135">Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션</span><span class="sxs-lookup"><span data-stu-id="141e3-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="141e3-1136">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="141e3-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="141e3-1137">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="141e3-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="141e3-1138">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="141e3-1139">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="141e3-1140">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="141e3-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="141e3-1141">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="141e3-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="141e3-1142">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="141e3-1142">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="141e3-1143">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="141e3-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="141e3-1144">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="141e3-1145">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141e3-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="141e3-1146">Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141e3-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="141e3-1147">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="141e3-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="141e3-1148">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="141e3-1148">Visual Studio Code documentation</span></span>

* <span data-ttu-id="141e3-1149">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="141e3-1149">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end
