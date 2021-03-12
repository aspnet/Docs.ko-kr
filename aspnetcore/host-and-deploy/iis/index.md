---
title: IIS가 있는 Windows에서 ASP.NET Core 호스팅
author: rick-anderson
description: Windows Server IIS(인터넷 정보 서비스)에서 ASP.NET Core 앱을 호스팅하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: 13c4c2e2759355b10a4648b313f4dbed4b3aa482
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588960"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="e5457-103">IIS가 있는 Windows에서 ASP.NET Core 호스팅</span><span class="sxs-lookup"><span data-stu-id="e5457-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e5457-104">IIS(인터넷 정보 서비스)는 ASP.NET Core를 비롯한 웹앱을 호스팅하기 위한 유연하고 안전하며 관리하기 쉬운 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-104">Internet Information Services (IIS) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e5457-105">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="e5457-105">Supported platforms</span></span>

<span data-ttu-id="e5457-106">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-106">The following operating systems are supported:</span></span>

* <span data-ttu-id="e5457-107">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-107">Windows 7 or later</span></span>
* <span data-ttu-id="e5457-108">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-108">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="e5457-109">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-109">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e5457-110">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-110">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e5457-111">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-111">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e5457-112">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-112">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e5457-113">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-113">Has 64-bit native dependencies.</span></span>

## <a name="install-the-aspnet-core-modulehosting-bundle"></a><span data-ttu-id="e5457-114">ASP.NET Core 모듈/호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-114">Install the ASP.NET Core Module/Hosting Bundle</span></span>

<span data-ttu-id="e5457-115">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-115">Download the installer using the following link:</span></span>

[<span data-ttu-id="e5457-116">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="e5457-116">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="e5457-117">ASP.NET Core 모듈을 설치하거나 다른 버전을 설치하는 방법에 대한 자세한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-117">For more details instructions on how to install the ASP.NET Core Module, or installing different versions, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

## <a name="get-started"></a><span data-ttu-id="e5457-118">시작하기</span><span class="sxs-lookup"><span data-stu-id="e5457-118">Get started</span></span>

<span data-ttu-id="e5457-119">IIS에서 웹 사이트 호스팅을 시작하려면 [시작 가이드](xref:tutorials/publish-to-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-119">For getting started with hosting a website on IIS, see our [getting started guide](xref:tutorials/publish-to-iis).</span></span>

<span data-ttu-id="e5457-120">Azure App Services에서 웹 사이트 호스팅을 시작하려면 [Azure App Service에 배포 가이드](xref:host-and-deploy/azure-apps/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e5457-121">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="e5457-121">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e5457-122">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="e5457-122">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e5457-123">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="e5457-123">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e5457-124">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-124">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="overlapped-recycle"></a><span data-ttu-id="e5457-125">겹친 재순환</span><span class="sxs-lookup"><span data-stu-id="e5457-125">Overlapped recycle</span></span>

<span data-ttu-id="e5457-126">일반적으로 가동 중지 시간이 0인 배포에는 [블루-그린 배포](https://www.martinfowler.com/bliki/BlueGreenDeployment.html)와 같은 패턴을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-126">In general, we recommend using a pattern like [blue-green deployments](https://www.martinfowler.com/bliki/BlueGreenDeployment.html) for zero-downtime deployments.</span></span> <span data-ttu-id="e5457-127">겹친 재순환과 같은 기능이 도움이 되지만, 가동 중지 시간이 0인 배포를 수행할 수 있음을 보장하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-127">Features like Overlapped Recycle help, but don't guarantee that you can do a zero-downtime deployment.</span></span> <span data-ttu-id="e5457-128">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/aspnetcore/issues/10117)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-128">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/10117).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5457-129">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e5457-129">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e5457-130">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="e5457-130">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e5457-131">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e5457-131">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e5457-132">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e5457-132">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e5457-133">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-133">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="e5457-134">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-134">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="e5457-135">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="e5457-135">Supported operating systems</span></span>

<span data-ttu-id="e5457-136">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-136">The following operating systems are supported:</span></span>

* <span data-ttu-id="e5457-137">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-137">Windows 7 or later</span></span>
* <span data-ttu-id="e5457-138">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-138">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="e5457-139">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-139">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="e5457-140">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-140">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e5457-141">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-141">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="e5457-142">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e5457-142">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e5457-143">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="e5457-143">Supported platforms</span></span>

<span data-ttu-id="e5457-144">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-144">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e5457-145">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-145">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e5457-146">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-146">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e5457-147">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-147">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e5457-148">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-148">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="e5457-149">32비트(x86)용으로 게시된 앱은 해당 IIS 애플리케이션 풀에 대해 32비트를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-149">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="e5457-150">자세한 내용은 [IIS 사이트 만들기](#create-the-iis-site) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-150">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="e5457-151">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-151">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="e5457-152">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-152">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="e5457-153">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e5457-153">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="e5457-154">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e5457-154">In-process hosting model</span></span>

<span data-ttu-id="e5457-155">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-155">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="e5457-156">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-156">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="e5457-157">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-157">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e5457-158">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="e5457-158">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="e5457-159">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-159">Performs app initialization.</span></span>
  * <span data-ttu-id="e5457-160">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-160">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="e5457-161">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="e5457-161">Calls `Program.Main`.</span></span>
* <span data-ttu-id="e5457-162">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-162">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="e5457-163">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-163">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="e5457-165">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-165">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="e5457-166">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-166">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="e5457-167">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-167">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="e5457-168">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-168">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="e5457-169">IIS HTTP 서버에서 요청을 처리한 후:</span><span class="sxs-lookup"><span data-stu-id="e5457-169">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="e5457-170">해당 요청은 ASP.NET Core 미들웨어 파이프라인으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-170">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="e5457-171">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="e5457-172">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-172">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="e5457-173">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-173">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="e5457-174">In Process 호스팅은 기존 앱에 대해 옵트인(opt in)됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-174">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="e5457-175">ASP.NET Core 웹 템플릿은 In Process 호스팅 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-175">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="e5457-176">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(`w3wp.exe` 또는 `iisexpress.exe`) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-176">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="e5457-177">성능 테스트의 결과 .NET Core 앱 in-process를 호스팅하는 것이 앱 out-of-process 및 [Kestrel](xref:fundamentals/servers/kestrel)에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-177">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e5457-178">단일 실행 파일로 게시된 앱은 In Process 호스팅 모델을 통해 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-178">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="e5457-179">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e5457-179">Out-of-process hosting model</span></span>

<span data-ttu-id="e5457-180">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-180">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="e5457-181">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-181">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e5457-182">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-182">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e5457-183">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-183">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="e5457-185">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-185">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="e5457-186">드라이버는 웹 사이트의 구성된 포트에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-186">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="e5457-187">구성된 포트는 일반적으로 80(HTTP) 또는 443(HTTPS)입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-187">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="e5457-188">모듈은 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-188">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="e5457-189">임의 포트는 80 또는 443이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-189">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="e5457-190">ASP.NET Core 모듈은 시작 시 환경 변수를 통해 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-190">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="e5457-191"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 확장은 `http://localhost:{PORT}`에서 수신하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-191">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="e5457-192">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-192">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e5457-193">이 모듈은 HTTPS 전달을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-193">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="e5457-194">HTTPS를 통해 IIS에서 수신하는 경우에도 요청은 HTTP를 통해 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-194">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e5457-195">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-195">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e5457-196">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-196">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e5457-197">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-197">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e5457-198">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-198">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e5457-199">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-199">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e5457-200">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-200">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e5457-201">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-201">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e5457-202">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="e5457-202">Enable the IISIntegration components</span></span>

<span data-ttu-id="e5457-203">`CreateHostBuilder`(`Program.cs`)에서 호스트를 빌드하는 경우 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-203">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e5457-204">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#default-builder-settings>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-204">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="e5457-205">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-205">IIS options</span></span>

<span data-ttu-id="e5457-206">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="e5457-206">**In-process hosting model**</span></span>

<span data-ttu-id="e5457-207">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-207">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="e5457-208">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-208">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="e5457-209">옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-209">Option</span></span>                         | <span data-ttu-id="e5457-210">기본값</span><span class="sxs-lookup"><span data-stu-id="e5457-210">Default</span></span> | <span data-ttu-id="e5457-211">설정</span><span class="sxs-lookup"><span data-stu-id="e5457-211">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e5457-212">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-212">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e5457-213">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-213">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e5457-214">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-214">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e5457-215">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-215">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e5457-216">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-216">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="e5457-217">`HttpContext.Request` 및 `HttpContext.Response`에 대해 동기 I/O가 허용되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-217">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="e5457-218">`HttpRequest`의 최대 요청 본문 크기를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-218">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="e5457-219">IIS 자체에는 `IISServerOptions`에 설정된 `MaxRequestBodySize` 앞에 처리되는 `maxAllowedContentLength` 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-219">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="e5457-220">`MaxRequestBodySize`를 변경해도 `maxAllowedContentLength`에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-220">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="e5457-221">`maxAllowedContentLength`를 늘리려면 `web.config`에 항목을 추가하여 `maxAllowedContentLength`를 더 높은 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-221">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="e5457-222">자세한 내용은 [구성](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-222">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="e5457-223">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="e5457-223">**Out-of-process hosting model**</span></span>

<span data-ttu-id="e5457-224">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-224">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="e5457-225">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-225">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e5457-226">옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-226">Option</span></span>                         | <span data-ttu-id="e5457-227">기본값</span><span class="sxs-lookup"><span data-stu-id="e5457-227">Default</span></span> | <span data-ttu-id="e5457-228">설정</span><span class="sxs-lookup"><span data-stu-id="e5457-228">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e5457-229">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-229">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e5457-230">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-230">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e5457-231">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-231">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e5457-232">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-232">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e5457-233">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-233">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e5457-234">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-234">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e5457-235">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="e5457-235">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e5457-236">[IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 다음을 전달하도록 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-236">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="e5457-237">체계(HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e5457-237">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="e5457-238">요청이 시작된 원격 IP 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-238">Remote IP address where the request originated.</span></span>

<span data-ttu-id="e5457-239">[IIS 통합 미들웨어](#enable-the-iisintegration-components)는 전달된 헤더 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-239">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="e5457-240">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-240">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e5457-241">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-241">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="e5457-242">`web.config` 파일:</span><span class="sxs-lookup"><span data-stu-id="e5457-242">`web.config` file</span></span>

<span data-ttu-id="e5457-243">`web.config` 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-243">The `web.config` file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e5457-244">`web.config` 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-244">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="e5457-245">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-245">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e5457-246">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-246">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e5457-247">프로젝트에 `web.config` 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments` 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-247">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="e5457-248">프로젝트에 `web.config` 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments`로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-248">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="e5457-249">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-249">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="e5457-250">`web.config` 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-250">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="e5457-251">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-251">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="e5457-252">웹 SDK가 `web.config` 파일을 변환하지 못하도록 하려면 프로젝트 파일의 `<IsTransformWebConfigDisabled>` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-252">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="e5457-253">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 `processPath` 및 `arguments`를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-253">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="e5457-254">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-254">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="e5457-255">`web.config` 파일 위치</span><span class="sxs-lookup"><span data-stu-id="e5457-255">`web.config` file location</span></span>

<span data-ttu-id="e5457-256">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 `web.config` 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-256">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="e5457-257">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-257">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="e5457-258">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 `web.config` 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-258">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="e5457-259">중요한 파일은 `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml`(XML 문서 주석) 및 `{ASSEMBLY}.deps.json`과 같은 앱의 실제 경로에 있습니다. 여기서 자리 표시자 `{ASSEMBLY}`는 어셈플리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-259">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="e5457-260">`web.config` 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-260">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="e5457-261">`web.config` 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-261">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="e5457-262">**`web.config` 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 `web.config` 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="e5457-262">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="e5457-263">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="e5457-263">Transform web.config</span></span>

<span data-ttu-id="e5457-264">게시할 때 `web.config`를 변환해야 하는 경우 <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-264">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="e5457-265">구성, 프로필 또는 환경을 기반으로 하는 환경 변수를 설정하려면 게시할 때 `web.config`를 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-265">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e5457-266">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-266">IIS configuration</span></span>

<span data-ttu-id="e5457-267">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="e5457-267">**Windows Server operating systems**</span></span>

<span data-ttu-id="e5457-268">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-268">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e5457-269">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-269">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e5457-270">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-270">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e5457-272">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-272">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e5457-273">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-273">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e5457-275">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-275">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e5457-276">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-276">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e5457-277">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-277">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e5457-278">자세한 내용은 [Windows 인증 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-278">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e5457-279">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-279">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e5457-280">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-280">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e5457-281">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-281">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e5457-282">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-282">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e5457-283">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-283">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e5457-284">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-284">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e5457-285">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-285">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e5457-286">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="e5457-286">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e5457-287">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-287">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e5457-288">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-288">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e5457-289">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-289">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e5457-290">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-290">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e5457-291">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-291">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e5457-292">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-292">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e5457-293">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-293">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e5457-294">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-294">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e5457-295">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-295">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e5457-296">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-296">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e5457-297">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-297">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e5457-298">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-298">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e5457-299">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-299">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e5457-300">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-300">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e5457-301">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-301">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e5457-302">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-302">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e5457-303">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-303">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e5457-305">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-305">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e5457-306">호스팅 시스템에 *.NET Core 호스팅 번들* 을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-306">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="e5457-307">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-307">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e5457-308">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-308">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e5457-309">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-309">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e5457-310">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-310">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e5457-311">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="e5457-311">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e5457-312">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-312">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="e5457-313">직접 다운로드(현재 버전)</span><span class="sxs-lookup"><span data-stu-id="e5457-313">Direct download (current version)</span></span>

<span data-ttu-id="e5457-314">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-314">Download the installer using the following link:</span></span>

[<span data-ttu-id="e5457-315">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="e5457-315">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="e5457-316">이전 버전의 설치 관리자</span><span class="sxs-lookup"><span data-stu-id="e5457-316">Earlier versions of the installer</span></span>

<span data-ttu-id="e5457-317">이전 버전의 설치 관리자를 가져오려면:</span><span class="sxs-lookup"><span data-stu-id="e5457-317">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="e5457-318">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-318">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e5457-319">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-319">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e5457-320">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-320">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e5457-321">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-321">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e5457-322">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-322">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e5457-323">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-323">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e5457-324">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-324">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e5457-325">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-325">Run the installer on the server.</span></span> <span data-ttu-id="e5457-326">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-326">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e5457-327">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-327">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e5457-328">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-328">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e5457-329">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-329">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e5457-330">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-330">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e5457-331">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-331">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e5457-332">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-332">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="e5457-333">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-333">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e5457-334">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-334">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e5457-335">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(`applicationHost.config`)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-335">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e5457-336">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e5457-336">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e5457-337">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-337">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e5457-338">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-338">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e5457-339">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-339">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e5457-340">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-340">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e5457-341">새 호스팅 번들을 설치하여 공유 프레임워크를 업그레이드한 후, 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-341">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="e5457-342">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-342">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="e5457-343">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-343">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="e5457-344">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-344">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="e5457-345">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-345">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="e5457-346">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-346">The preferred method is to use WebPI.</span></span> <span data-ttu-id="e5457-347">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-347">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e5457-348">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="e5457-348">Create the IIS site</span></span>

1. <span data-ttu-id="e5457-349">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-349">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e5457-350">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-350">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e5457-351">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-351">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e5457-352">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-352">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e5457-353">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-353">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e5457-354">상황에 맞는 메뉴에서 **웹 사이트 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-354">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e5457-355">**사이트 이름** 을 제공하고 **실제 경로** 를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-355">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="e5457-356">**바인딩** 구성을 제공하고 **확인** 을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-356">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="e5457-358">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-358">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e5457-359">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-359">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e5457-360">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-360">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e5457-361">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-361">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e5457-362">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="e5457-362">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e5457-363">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-363">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e5457-364">서버 노드에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-364">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="e5457-365">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-365">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="e5457-366">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-366">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="e5457-368">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-368">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="e5457-369">ASP.NET Core에서는 데스크톱 CLR(.NET CLR)을 로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-369">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="e5457-370">.NET Core에 대한 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-370">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="e5457-371">**.NET CLR 버전** 을 **관리 코드 없음** 으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-371">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="e5457-372">*ASP.NET Core 2.2 이상*:</span><span class="sxs-lookup"><span data-stu-id="e5457-372">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="e5457-373">[In-Process 호스팅 모델](#in-process-hosting-model)을 사용하는 32비트 SDK로 게시된 32비트(x86) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트의 애플리케이션 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-373">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="e5457-374">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-374">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="e5457-375">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-375">Select the app's Application Pool.</span></span> <span data-ttu-id="e5457-376">**작업** 사이드바에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-376">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="e5457-377">**32비트 애플리케이션 사용** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-377">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="e5457-378">[In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-378">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="e5457-379">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-379">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="e5457-380">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-380">Select the app's Application Pool.</span></span> <span data-ttu-id="e5457-381">**작업** 사이드바에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-381">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="e5457-382">**32비트 애플리케이션 사용** 을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-382">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="e5457-383">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-383">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e5457-384">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-384">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e5457-385">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-385">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="e5457-386">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-386">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="e5457-387">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-387">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="e5457-388">앱 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-388">Deploy the app</span></span>

<span data-ttu-id="e5457-389">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-389">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="e5457-390">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 `publish` 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-390">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's `publish` folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="e5457-391">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-391">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="e5457-392">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-392">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="e5457-393">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-393">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="e5457-395">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-395">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="e5457-396">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-396">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="e5457-397">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-397">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="e5457-398">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="e5457-398">Alternatives to Web Deploy</span></span>

<span data-ttu-id="e5457-399">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-399">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="e5457-400">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-400">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="e5457-401">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="e5457-401">Browse the website</span></span>

<span data-ttu-id="e5457-402">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-402">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="e5457-403">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름** 에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-403">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="e5457-404">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-404">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="e5457-406">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="e5457-406">Locked deployment files</span></span>

<span data-ttu-id="e5457-407">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-407">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="e5457-408">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-408">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="e5457-409">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나** 를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-409">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="e5457-410">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-410">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="e5457-411">`app_offline.htm` 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-411">An `app_offline.htm` file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e5457-412">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 `app_offline.htm` 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-412">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the `app_offline.htm` file during the deployment.</span></span> <span data-ttu-id="e5457-413">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-413">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="e5457-414">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-414">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="e5457-415">PowerShell을 사용하여 `app_offline.htm`을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="e5457-415">Use PowerShell to drop `app_offline.htm` (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a><span data-ttu-id="e5457-416">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="e5457-416">Data protection</span></span>

<span data-ttu-id="e5457-417">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-417">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e5457-418">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-418">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e5457-419">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-419">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e5457-420">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-420">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e5457-421">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-421">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e5457-422">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-422">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e5457-423">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-423">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e5457-424">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-424">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e5457-425">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-425">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e5457-426">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="e5457-426">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="e5457-427">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-427">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e5457-428">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-428">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="e5457-429">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-429">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e5457-430">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-430">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e5457-431">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-431">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e5457-432">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-432">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="e5457-433">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-433">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="e5457-434">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-434">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="e5457-435">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-435">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e5457-436">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-436">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e5457-437">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-437">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="e5457-438">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="e5457-438">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e5457-439">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-439">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e5457-440">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-440">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e5457-441">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-441">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e5457-442">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-442">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="e5457-443">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-443">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e5457-444">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-444">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e5457-445">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-445">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e5457-446">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-446">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e5457-447">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-447">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="e5457-448">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-448">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="e5457-449">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-449">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e5457-450">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-450">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e5457-451">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="e5457-451">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e5457-452">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-452">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e5457-453">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-453">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e5457-454">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-454">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e5457-455">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-455">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e5457-456">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-456">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e5457-457">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-457">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e5457-458">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-458">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e5457-459">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="e5457-459">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="e5457-460">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-460">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e5457-461">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-461">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="e5457-462">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="e5457-462">Virtual Directories</span></span>

<span data-ttu-id="e5457-463">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-463">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e5457-464">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-464">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e5457-465">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="e5457-465">Sub-applications</span></span>

<span data-ttu-id="e5457-466">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-466">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e5457-467">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-467">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e5457-468">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-468">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e5457-469">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-469">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e5457-470">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-470">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e5457-471">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-471">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e5457-472">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-472">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e5457-473">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-473">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e5457-474">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-474">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e5457-475">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-475">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e5457-476">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-476">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e5457-477">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-477">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e5457-478">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-478">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e5457-479">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-479">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e5457-480">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-480">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e5457-481">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-481">Select **OK**.</span></span>

<span data-ttu-id="e5457-482">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-482">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e5457-483">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-483">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="e5457-484">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-484">Configuration of IIS with web.config</span></span>

<span data-ttu-id="e5457-485">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config* 에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-485">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="e5457-486">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-486">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="e5457-487">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-487">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="e5457-488">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-488">For more information, see the following topics:</span></span>

* [<span data-ttu-id="e5457-489">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="e5457-489">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="e5457-490">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-490">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="e5457-491">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="e5457-491">Configuration sections of web.config</span></span>

<span data-ttu-id="e5457-492">*web.config* 에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-492">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="e5457-493">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-493">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="e5457-494">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-494">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="e5457-495">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="e5457-495">Application Pools</span></span>

<span data-ttu-id="e5457-496">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-496">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="e5457-497">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-497">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="e5457-498">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-498">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="e5457-499">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-499">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="e5457-500">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-500">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e5457-501">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-501">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="e5457-502">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="e5457-502">Application Pool Identity</span></span>

<span data-ttu-id="e5457-503">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-503">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e5457-504">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-504">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e5457-505">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-505">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="e5457-507">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-507">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e5457-508">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-508">Resources can be secured using this identity.</span></span> <span data-ttu-id="e5457-509">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-509">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e5457-510">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-510">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e5457-511">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-511">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e5457-512">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-512">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e5457-513">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-513">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e5457-514">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-514">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e5457-515">**선택할 개체 이름 입력** 영역에 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-515">Enter `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="e5457-516">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-516">Select the **Check Names** button.</span></span> <span data-ttu-id="e5457-517">*DefaultAppPool* 의 경우 `IIS AppPool\DefaultAppPool`을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-517">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="e5457-518">**이름 확인** 단추를 선택하면 개체 이름 영역에 `DefaultAppPool` 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-518">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="e5457-519">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-519">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e5457-520">개체 이름을 확인할 때 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-520">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e5457-522">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-522">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e5457-524">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-524">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e5457-525">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-525">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e5457-526">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-526">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e5457-527">`DefaultAppPool`을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-527">Using the `DefaultAppPool` as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e5457-528">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-528">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e5457-529">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="e5457-529">HTTP/2 support</span></span>

<span data-ttu-id="e5457-530">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-530">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="e5457-531">In-Process</span><span class="sxs-lookup"><span data-stu-id="e5457-531">In-process</span></span>
  * <span data-ttu-id="e5457-532">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-532">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e5457-533">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="e5457-533">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="e5457-534">Out of Process</span><span class="sxs-lookup"><span data-stu-id="e5457-534">Out-of-process</span></span>
  * <span data-ttu-id="e5457-535">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-535">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e5457-536">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-536">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e5457-537">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="e5457-537">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e5457-538">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-538">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="e5457-539">Out-of-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-539">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e5457-540">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-540">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e5457-541">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-541">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e5457-542">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-542">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e5457-543">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-543">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e5457-544">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="e5457-544">CORS preflight requests</span></span>

<span data-ttu-id="e5457-545">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-545">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e5457-546">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-546">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e5457-547">OPTIONS 요청을 전달하도록 `web.config`에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-547">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="e5457-548">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="e5457-548">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="e5457-549">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="e5457-549">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="e5457-550">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-550">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="e5457-551">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-551">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="e5457-552">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="e5457-552">Application Initialization Module</span></span>

<span data-ttu-id="e5457-553">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="e5457-553">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="e5457-554">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-554">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="e5457-555">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-555">The request triggers the app to start.</span></span> <span data-ttu-id="e5457-556">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="e5457-556">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="e5457-557">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-557">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="e5457-558">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="e5457-558">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="e5457-559">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-559">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e5457-560">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-560">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e5457-561">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-561">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e5457-562">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-562">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="e5457-563">**역할 및 기능 추가 마법사** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-563">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="e5457-564">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-564">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="e5457-565">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-565">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e5457-566">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-566">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="e5457-567">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="e5457-567">Using IIS Manager:</span></span>

  1. <span data-ttu-id="e5457-568">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-568">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e5457-569">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-569">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e5457-570">기본 **시작 모드** 는 **OnDemand** 입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-570">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="e5457-571">**시작 모드** 를 **AlwaysRunning** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-571">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="e5457-572">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-572">Select **OK**.</span></span>
  1. <span data-ttu-id="e5457-573">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-573">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e5457-574">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-574">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e5457-575">기본 **미리 로드 사용** 설정은 **False** 입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-575">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="e5457-576">**미리 로드 사용** 을 **True** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-576">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="e5457-577">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-577">Select **OK**.</span></span>

* <span data-ttu-id="e5457-578">`web.config`를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 web.config` 파일에 있는 `<system.webServer>\` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-578">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's web.config\` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="e5457-579">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="e5457-579">Idle Timeout</span></span>

<span data-ttu-id="e5457-580">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="e5457-580">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="e5457-581">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-581">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="e5457-582">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-582">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="e5457-583">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-583">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="e5457-584">기본 **유휴 시간 제한(분)** 은 **20** 분입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-584">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="e5457-585">**유휴 시간 제한(분)** 을 **0** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-585">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="e5457-586">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-586">Select **OK**.</span></span>
1. <span data-ttu-id="e5457-587">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-587">Recycle the worker process.</span></span>

<span data-ttu-id="e5457-588">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-588">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="e5457-589">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-589">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="e5457-590">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-590">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="e5457-591">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e5457-591">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="e5457-592">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="e5457-592">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="e5457-593">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="e5457-593">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="e5457-594">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="e5457-594">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e5457-595">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="e5457-595">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e5457-596">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="e5457-596">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e5457-597">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="e5457-597">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e5457-598">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-598">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="e5457-599">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e5457-599">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e5457-600">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="e5457-600">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e5457-601">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e5457-601">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e5457-602">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e5457-602">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e5457-603">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-603">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="e5457-604">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-604">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="e5457-605">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="e5457-605">Supported operating systems</span></span>

<span data-ttu-id="e5457-606">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-606">The following operating systems are supported:</span></span>

* <span data-ttu-id="e5457-607">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-607">Windows 7 or later</span></span>
* <span data-ttu-id="e5457-608">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-608">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e5457-609">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-609">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="e5457-610">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-610">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e5457-611">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-611">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="e5457-612">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e5457-612">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e5457-613">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="e5457-613">Supported platforms</span></span>

<span data-ttu-id="e5457-614">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-614">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e5457-615">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-615">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e5457-616">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-616">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e5457-617">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-617">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e5457-618">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-618">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="e5457-619">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-619">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="e5457-620">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-620">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="e5457-621">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e5457-621">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="e5457-622">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e5457-622">In-process hosting model</span></span>

<span data-ttu-id="e5457-623">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-623">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="e5457-624">In Process 호스팅은 다음과 같은 이유로 Out of Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-624">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="e5457-625">요청은 루프백 어댑터를 통해 프록시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-625">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="e5457-626">루프백 어댑터는 나가는 네트워크 트래픽을 동일한 컴퓨터로 다시 반환하는 네트워크 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-626">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="e5457-627">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-627">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e5457-628">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="e5457-628">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="e5457-629">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-629">Performs app initialization.</span></span>
  * <span data-ttu-id="e5457-630">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-630">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="e5457-631">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="e5457-631">Calls `Program.Main`.</span></span>
* <span data-ttu-id="e5457-632">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-632">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="e5457-633">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-633">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="e5457-634">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-634">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

<span data-ttu-id="e5457-636">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-636">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e5457-637">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-637">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e5457-638">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-638">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="e5457-639">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-639">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="e5457-640">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-640">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e5457-641">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-641">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e5457-642">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-642">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="e5457-643">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-643">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="e5457-644">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-Process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-644">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="e5457-645">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-645">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="e5457-646">성능 테스트의 결과 .NET Core 앱을 in-process로 호스트하는 것이 앱을 out-of-process로 호스트하고 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-646">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="e5457-647">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="e5457-647">Out-of-process hosting model</span></span>

<span data-ttu-id="e5457-648">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-648">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="e5457-649">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-649">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e5457-650">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-650">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e5457-651">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-651">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="e5457-653">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-653">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e5457-654">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-654">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e5457-655">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-655">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e5457-656">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-656">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="e5457-657">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-657">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e5457-658">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-658">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e5457-659">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-659">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e5457-660">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-660">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e5457-661">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-661">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e5457-662">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-662">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e5457-663">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-663">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e5457-664">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-664">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e5457-665">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-665">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e5457-666">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="e5457-666">Enable the IISIntegration components</span></span>

<span data-ttu-id="e5457-667">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-667">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e5457-668">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-668">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="e5457-669">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-669">IIS options</span></span>

<span data-ttu-id="e5457-670">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="e5457-670">**In-process hosting model**</span></span>

<span data-ttu-id="e5457-671">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-671">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="e5457-672">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-672">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="e5457-673">옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-673">Option</span></span>                         | <span data-ttu-id="e5457-674">기본값</span><span class="sxs-lookup"><span data-stu-id="e5457-674">Default</span></span> | <span data-ttu-id="e5457-675">설정</span><span class="sxs-lookup"><span data-stu-id="e5457-675">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e5457-676">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-676">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e5457-677">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-677">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e5457-678">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-678">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e5457-679">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-679">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e5457-680">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-680">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="e5457-681">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="e5457-681">**Out-of-process hosting model**</span></span>

<span data-ttu-id="e5457-682">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-682">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e5457-683">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-683">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e5457-684">옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-684">Option</span></span>                         | <span data-ttu-id="e5457-685">기본값</span><span class="sxs-lookup"><span data-stu-id="e5457-685">Default</span></span> | <span data-ttu-id="e5457-686">설정</span><span class="sxs-lookup"><span data-stu-id="e5457-686">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e5457-687">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-687">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e5457-688">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-688">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e5457-689">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-689">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e5457-690">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-690">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e5457-691">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-691">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e5457-692">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-692">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e5457-693">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="e5457-693">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e5457-694">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-694">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e5457-695">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-695">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e5457-696">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-696">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="e5457-697">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="e5457-697">web.config file</span></span>

<span data-ttu-id="e5457-698">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-698">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e5457-699">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-699">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="e5457-700">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-700">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e5457-701">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-701">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e5457-702">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-702">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="e5457-703">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수* 로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-703">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="e5457-704">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-704">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="e5457-705">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-705">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="e5457-706">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-706">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="e5457-707">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-707">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="e5457-708">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수* 를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-708">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="e5457-709">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-709">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="e5457-710">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="e5457-710">web.config file location</span></span>

<span data-ttu-id="e5457-711">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-711">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="e5457-712">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-712">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="e5457-713">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-713">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="e5457-714">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json* 과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-714">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="e5457-715">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-715">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="e5457-716">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-716">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="e5457-717">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="e5457-717">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="e5457-718">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="e5457-718">Transform web.config</span></span>

<span data-ttu-id="e5457-719">게시할 때 *web.config* 를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-719">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e5457-720">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-720">IIS configuration</span></span>

<span data-ttu-id="e5457-721">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="e5457-721">**Windows Server operating systems**</span></span>

<span data-ttu-id="e5457-722">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-722">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e5457-723">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-723">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e5457-724">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-724">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e5457-726">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-726">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e5457-727">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-727">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e5457-729">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-729">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e5457-730">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-730">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e5457-731">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-731">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e5457-732">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-732">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e5457-733">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-733">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e5457-734">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-734">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e5457-735">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-735">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e5457-736">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-736">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e5457-737">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-737">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e5457-738">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-738">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e5457-739">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-739">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e5457-740">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="e5457-740">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e5457-741">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-741">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e5457-742">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-742">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e5457-743">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-743">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e5457-744">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-744">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e5457-745">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-745">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e5457-746">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-746">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e5457-747">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-747">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e5457-748">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-748">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e5457-749">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-749">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e5457-750">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-750">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e5457-751">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-751">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e5457-752">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-752">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e5457-753">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-753">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e5457-754">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-754">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e5457-755">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-755">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e5457-756">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-756">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e5457-757">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-757">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e5457-759">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-759">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e5457-760">호스팅 시스템에 *.NET Core 호스팅 번들* 을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-760">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="e5457-761">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-761">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e5457-762">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-762">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e5457-763">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-763">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e5457-764">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-764">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e5457-765">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="e5457-765">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e5457-766">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-766">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="e5457-767">다운로드</span><span class="sxs-lookup"><span data-stu-id="e5457-767">Download</span></span>

1. <span data-ttu-id="e5457-768">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-768">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e5457-769">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-769">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e5457-770">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-770">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e5457-771">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-771">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e5457-772">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-772">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e5457-773">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-773">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e5457-774">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-774">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e5457-775">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-775">Run the installer on the server.</span></span> <span data-ttu-id="e5457-776">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-776">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e5457-777">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-777">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e5457-778">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-778">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e5457-779">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-779">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e5457-780">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-780">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e5457-781">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-781">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e5457-782">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-782">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="e5457-783">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-783">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e5457-784">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-784">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e5457-785">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-785">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e5457-786">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e5457-786">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e5457-787">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-787">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e5457-788">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-788">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e5457-789">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-789">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e5457-790">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-790">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="e5457-791">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-791">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="e5457-792">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-792">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="e5457-793">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-793">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e5457-794">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-794">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="e5457-795">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-795">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="e5457-796">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-796">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="e5457-797">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-797">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="e5457-798">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-798">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="e5457-799">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-799">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="e5457-800">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-800">The preferred method is to use WebPI.</span></span> <span data-ttu-id="e5457-801">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-801">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e5457-802">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="e5457-802">Create the IIS site</span></span>

1. <span data-ttu-id="e5457-803">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-803">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e5457-804">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-804">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e5457-805">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-805">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e5457-806">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-806">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e5457-807">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-807">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e5457-808">상황에 맞는 메뉴에서 **웹 사이트 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-808">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e5457-809">**사이트 이름** 을 제공하고 **실제 경로** 를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-809">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="e5457-810">**바인딩** 구성을 제공하고 **확인** 을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-810">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="e5457-812">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-812">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e5457-813">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-813">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e5457-814">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-814">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e5457-815">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-815">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e5457-816">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="e5457-816">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e5457-817">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-817">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e5457-818">서버 노드에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-818">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="e5457-819">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-819">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="e5457-820">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-820">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="e5457-822">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-822">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="e5457-823">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-823">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="e5457-824">**.NET CLR 버전** 을 **관리 코드 없음** 으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-824">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="e5457-825">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-825">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="e5457-826">IIS 관리자의 **애플리케이션 풀** 에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-826">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="e5457-827">**32비트 애플리케이션 사용** 을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-827">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="e5457-828">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-828">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="e5457-829">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-829">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e5457-830">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-830">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e5457-831">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-831">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="e5457-832">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-832">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="e5457-833">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-833">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="e5457-834">앱 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-834">Deploy the app</span></span>

<span data-ttu-id="e5457-835">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-835">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="e5457-836">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-836">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="e5457-837">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-837">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="e5457-838">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-838">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="e5457-839">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-839">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="e5457-841">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-841">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="e5457-842">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-842">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="e5457-843">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-843">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="e5457-844">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="e5457-844">Alternatives to Web Deploy</span></span>

<span data-ttu-id="e5457-845">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-845">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="e5457-846">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-846">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="e5457-847">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="e5457-847">Browse the website</span></span>

<span data-ttu-id="e5457-848">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-848">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="e5457-849">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름** 에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-849">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="e5457-850">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-850">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="e5457-852">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="e5457-852">Locked deployment files</span></span>

<span data-ttu-id="e5457-853">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-853">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="e5457-854">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-854">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="e5457-855">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나** 를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-855">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="e5457-856">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-856">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="e5457-857">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-857">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e5457-858">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-858">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="e5457-859">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-859">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="e5457-860">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-860">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="e5457-861">PowerShell을 사용하여 *app_offline.htm* 을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="e5457-861">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="e5457-862">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="e5457-862">Data protection</span></span>

<span data-ttu-id="e5457-863">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-863">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e5457-864">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-864">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e5457-865">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-865">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e5457-866">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-866">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e5457-867">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-867">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e5457-868">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-868">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e5457-869">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-869">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e5457-870">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-870">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e5457-871">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-871">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e5457-872">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="e5457-872">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="e5457-873">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-873">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e5457-874">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-874">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="e5457-875">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-875">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e5457-876">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-876">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e5457-877">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-877">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e5457-878">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-878">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="e5457-879">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-879">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="e5457-880">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-880">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="e5457-881">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-881">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e5457-882">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-882">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e5457-883">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-883">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="e5457-884">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="e5457-884">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e5457-885">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-885">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e5457-886">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-886">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e5457-887">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-887">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e5457-888">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-888">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="e5457-889">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-889">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e5457-890">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-890">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e5457-891">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-891">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e5457-892">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-892">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e5457-893">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-893">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="e5457-894">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-894">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="e5457-895">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-895">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e5457-896">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-896">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e5457-897">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="e5457-897">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e5457-898">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-898">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e5457-899">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-899">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e5457-900">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-900">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e5457-901">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-901">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e5457-902">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-902">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e5457-903">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-903">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e5457-904">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-904">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e5457-905">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="e5457-905">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="e5457-906">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-906">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e5457-907">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-907">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="e5457-908">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="e5457-908">Virtual Directories</span></span>

<span data-ttu-id="e5457-909">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-909">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e5457-910">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-910">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e5457-911">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="e5457-911">Sub-applications</span></span>

<span data-ttu-id="e5457-912">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-912">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e5457-913">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-913">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e5457-914">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-914">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e5457-915">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-915">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e5457-916">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-916">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e5457-917">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-917">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e5457-918">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-918">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e5457-919">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-919">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e5457-920">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-920">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e5457-921">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-921">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e5457-922">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-922">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e5457-923">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-923">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e5457-924">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-924">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e5457-925">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-925">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e5457-926">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-926">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e5457-927">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-927">Select **OK**.</span></span>

<span data-ttu-id="e5457-928">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-928">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e5457-929">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-929">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="e5457-930">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-930">Configuration of IIS with web.config</span></span>

<span data-ttu-id="e5457-931">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config* 에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-931">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="e5457-932">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-932">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="e5457-933">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-933">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="e5457-934">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-934">For more information, see the following topics:</span></span>

* [<span data-ttu-id="e5457-935">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="e5457-935">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="e5457-936">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-936">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="e5457-937">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="e5457-937">Configuration sections of web.config</span></span>

<span data-ttu-id="e5457-938">*web.config* 에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-938">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="e5457-939">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-939">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="e5457-940">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-940">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="e5457-941">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="e5457-941">Application Pools</span></span>

<span data-ttu-id="e5457-942">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-942">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="e5457-943">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-943">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="e5457-944">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-944">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="e5457-945">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-945">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="e5457-946">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-946">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e5457-947">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-947">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="e5457-948">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="e5457-948">Application Pool Identity</span></span>

<span data-ttu-id="e5457-949">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-949">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e5457-950">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-950">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e5457-951">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-951">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="e5457-953">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-953">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e5457-954">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-954">Resources can be secured using this identity.</span></span> <span data-ttu-id="e5457-955">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-955">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e5457-956">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-956">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e5457-957">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-957">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e5457-958">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-958">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e5457-959">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-959">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e5457-960">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-960">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e5457-961">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-961">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="e5457-962">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-962">Select the **Check Names** button.</span></span> <span data-ttu-id="e5457-963">*DefaultAppPool* 의 경우 **IIS AppPool\DefaultAppPool** 을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-963">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="e5457-964">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-964">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="e5457-965">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-965">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e5457-966">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-966">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e5457-968">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-968">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e5457-970">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-970">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e5457-971">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-971">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e5457-972">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-972">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e5457-973">*DefaultAppPool* 을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-973">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e5457-974">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-974">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e5457-975">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="e5457-975">HTTP/2 support</span></span>

<span data-ttu-id="e5457-976">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-976">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="e5457-977">In-Process</span><span class="sxs-lookup"><span data-stu-id="e5457-977">In-process</span></span>
  * <span data-ttu-id="e5457-978">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-978">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e5457-979">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="e5457-979">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="e5457-980">Out of Process</span><span class="sxs-lookup"><span data-stu-id="e5457-980">Out-of-process</span></span>
  * <span data-ttu-id="e5457-981">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-981">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e5457-982">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-982">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e5457-983">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="e5457-983">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e5457-984">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-984">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="e5457-985">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-985">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e5457-986">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-986">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e5457-987">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-987">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e5457-988">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-988">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e5457-989">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-989">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e5457-990">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="e5457-990">CORS preflight requests</span></span>

<span data-ttu-id="e5457-991">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-991">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e5457-992">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-992">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e5457-993">OPTIONS 요청을 전달하도록 *web.config* 에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-993">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="e5457-994">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="e5457-994">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="e5457-995">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="e5457-995">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="e5457-996">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-996">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="e5457-997">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-997">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="e5457-998">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="e5457-998">Application Initialization Module</span></span>

<span data-ttu-id="e5457-999">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="e5457-999">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="e5457-1000">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1000">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="e5457-1001">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1001">The request triggers the app to start.</span></span> <span data-ttu-id="e5457-1002">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="e5457-1002">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="e5457-1003">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1003">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="e5457-1004">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="e5457-1004">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="e5457-1005">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1005">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e5457-1006">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1006">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e5457-1007">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1007">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e5457-1008">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-1008">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="e5457-1009">**역할 및 기능 추가 마법사** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1009">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="e5457-1010">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1010">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="e5457-1011">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1011">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e5457-1012">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1012">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="e5457-1013">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="e5457-1013">Using IIS Manager:</span></span>

  1. <span data-ttu-id="e5457-1014">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1014">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e5457-1015">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1015">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e5457-1016">기본 **시작 모드** 는 **OnDemand** 입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1016">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="e5457-1017">**시작 모드** 를 **AlwaysRunning** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1017">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="e5457-1018">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1018">Select **OK**.</span></span>
  1. <span data-ttu-id="e5457-1019">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1019">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e5457-1020">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1020">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e5457-1021">기본 **미리 로드 사용** 설정은 **False** 입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1021">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="e5457-1022">**미리 로드 사용** 을 **True** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1022">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="e5457-1023">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1023">Select **OK**.</span></span>

* <span data-ttu-id="e5457-1024">*web.config* 를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1024">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="e5457-1025">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="e5457-1025">Idle Timeout</span></span>

<span data-ttu-id="e5457-1026">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="e5457-1026">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="e5457-1027">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1027">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="e5457-1028">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1028">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="e5457-1029">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1029">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="e5457-1030">기본 **유휴 시간 제한(분)** 은 **20** 분입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1030">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="e5457-1031">**유휴 시간 제한(분)** 을 **0** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1031">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="e5457-1032">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1032">Select **OK**.</span></span>
1. <span data-ttu-id="e5457-1033">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1033">Recycle the worker process.</span></span>

<span data-ttu-id="e5457-1034">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1034">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="e5457-1035">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1035">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="e5457-1036">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1036">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="e5457-1037">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e5457-1037">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="e5457-1038">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="e5457-1038">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="e5457-1039">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="e5457-1039">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="e5457-1040">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="e5457-1040">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e5457-1041">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="e5457-1041">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e5457-1042">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="e5457-1042">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e5457-1043">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="e5457-1043">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e5457-1044">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-1044">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="e5457-1045">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e5457-1045">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e5457-1046">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="e5457-1046">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e5457-1047">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e5457-1047">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e5457-1048">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e5457-1048">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e5457-1049">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1049">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="e5457-1050">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-1050">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="e5457-1051">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="e5457-1051">Supported operating systems</span></span>

<span data-ttu-id="e5457-1052">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1052">The following operating systems are supported:</span></span>

* <span data-ttu-id="e5457-1053">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-1053">Windows 7 or later</span></span>
* <span data-ttu-id="e5457-1054">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-1054">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e5457-1055">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1055">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="e5457-1056">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1056">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e5457-1057">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1057">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="e5457-1058">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e5457-1058">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e5457-1059">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="e5457-1059">Supported platforms</span></span>

<span data-ttu-id="e5457-1060">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1060">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e5457-1061">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1061">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e5457-1062">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1062">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e5457-1063">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1063">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e5457-1064">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1064">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="e5457-1065">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1065">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="e5457-1066">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1066">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="e5457-1067">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1067">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="e5457-1068">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](xref:fundamentals/servers/index#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서(*Out-of-Process*) 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1068">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="e5457-1069">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1069">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="e5457-1070">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1070">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e5457-1071">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1071">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e5457-1072">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1072">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="e5457-1074">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1074">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e5457-1075">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1075">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e5457-1076">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1076">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e5457-1077">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1077">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="e5457-1078">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1078">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e5457-1079">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1079">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e5457-1080">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1080">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e5457-1081">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1081">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e5457-1082">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1082">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e5457-1083">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1083">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e5457-1084">`CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1084">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e5457-1085">ASP.NET Core 모듈은 동적 포트를 생성하여 백 엔드 프로세스에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1085">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="e5457-1086">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1086">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> method.</span></span> <span data-ttu-id="e5457-1087">`UseIISIntegration`은 localhost IP 주소(`127.0.0.1`)의 동적 포트에서 수신 대기하도록 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1087">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="e5457-1088">동적 포트가 1234인 경우 Kestrel은 `127.0.0.1:1234`에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1088">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="e5457-1089">이 구성은 다음에서 제공된 다른 URL 구성을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1089">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="e5457-1090">Kestrel의 수신 대기 API</span><span class="sxs-lookup"><span data-stu-id="e5457-1090">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="e5457-1091">[구성](xref:fundamentals/configuration/index)(또는 [명령줄 --urls 옵션](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="e5457-1091">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="e5457-1092">모듈을 사용하는 경우 `UseUrls` 호출 또는 Kestrel의 `Listen` API가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1092">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="e5457-1093">`UseUrls` 또는 `Listen`을 호출하는 경우 Kestrel은 IIS 없이 앱을 실행할 때만 지정된 포트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1093">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="e5457-1094">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1094">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e5457-1095">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1095">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e5457-1096">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-1096">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e5457-1097">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="e5457-1097">Enable the IISIntegration components</span></span>

<span data-ttu-id="e5457-1098">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1098">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e5457-1099">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1099">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="e5457-1100">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-1100">IIS options</span></span>

| <span data-ttu-id="e5457-1101">옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-1101">Option</span></span>                         | <span data-ttu-id="e5457-1102">기본값</span><span class="sxs-lookup"><span data-stu-id="e5457-1102">Default</span></span> | <span data-ttu-id="e5457-1103">설정</span><span class="sxs-lookup"><span data-stu-id="e5457-1103">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e5457-1104">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1104">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e5457-1105">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1105">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e5457-1106">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1106">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e5457-1107">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1107">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e5457-1108">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1108">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="e5457-1109">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1109">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e5457-1110">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1110">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e5457-1111">옵션</span><span class="sxs-lookup"><span data-stu-id="e5457-1111">Option</span></span>                         | <span data-ttu-id="e5457-1112">기본값</span><span class="sxs-lookup"><span data-stu-id="e5457-1112">Default</span></span> | <span data-ttu-id="e5457-1113">설정</span><span class="sxs-lookup"><span data-stu-id="e5457-1113">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e5457-1114">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1114">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e5457-1115">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1115">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e5457-1116">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1116">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e5457-1117">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1117">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e5457-1118">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1118">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e5457-1119">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1119">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e5457-1120">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="e5457-1120">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e5457-1121">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1121">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e5457-1122">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1122">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e5457-1123">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1123">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="e5457-1124">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="e5457-1124">web.config file</span></span>

<span data-ttu-id="e5457-1125">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1125">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e5457-1126">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1126">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="e5457-1127">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1127">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e5457-1128">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1128">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e5457-1129">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1129">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="e5457-1130">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수* 로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1130">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="e5457-1131">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1131">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="e5457-1132">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1132">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="e5457-1133">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1133">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="e5457-1134">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1134">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="e5457-1135">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수* 를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1135">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="e5457-1136">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1136">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="e5457-1137">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="e5457-1137">web.config file location</span></span>

<span data-ttu-id="e5457-1138">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1138">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="e5457-1139">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1139">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="e5457-1140">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1140">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="e5457-1141">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json* 과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1141">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="e5457-1142">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1142">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="e5457-1143">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1143">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="e5457-1144">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="e5457-1144">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="e5457-1145">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="e5457-1145">Transform web.config</span></span>

<span data-ttu-id="e5457-1146">게시할 때 *web.config* 를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1146">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e5457-1147">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-1147">IIS configuration</span></span>

<span data-ttu-id="e5457-1148">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="e5457-1148">**Windows Server operating systems**</span></span>

<span data-ttu-id="e5457-1149">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1149">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e5457-1150">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1150">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e5457-1151">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1151">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e5457-1153">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1153">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e5457-1154">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1154">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e5457-1156">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-1156">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e5457-1157">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1157">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e5457-1158">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1158">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e5457-1159">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1159">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e5457-1160">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-1160">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e5457-1161">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1161">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e5457-1162">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1162">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e5457-1163">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1163">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e5457-1164">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1164">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e5457-1165">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1165">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e5457-1166">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1166">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e5457-1167">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="e5457-1167">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e5457-1168">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1168">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e5457-1169">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1169">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e5457-1170">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1170">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e5457-1171">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1171">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e5457-1172">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1172">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e5457-1173">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1173">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e5457-1174">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1174">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e5457-1175">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-1175">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e5457-1176">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1176">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e5457-1177">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1177">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e5457-1178">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1178">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e5457-1179">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-1179">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e5457-1180">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1180">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e5457-1181">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1181">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e5457-1182">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1182">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e5457-1183">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1183">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e5457-1184">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1184">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e5457-1186">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-1186">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e5457-1187">호스팅 시스템에 *.NET Core 호스팅 번들* 을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1187">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="e5457-1188">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1188">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e5457-1189">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1189">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e5457-1190">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1190">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e5457-1191">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1191">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e5457-1192">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="e5457-1192">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e5457-1193">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1193">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="e5457-1194">다운로드</span><span class="sxs-lookup"><span data-stu-id="e5457-1194">Download</span></span>

1. <span data-ttu-id="e5457-1195">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1195">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e5457-1196">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1196">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e5457-1197">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1197">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e5457-1198">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1198">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e5457-1199">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1199">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e5457-1200">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1200">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e5457-1201">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-1201">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e5457-1202">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1202">Run the installer on the server.</span></span> <span data-ttu-id="e5457-1203">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1203">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e5457-1204">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1204">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e5457-1205">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1205">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e5457-1206">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1206">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e5457-1207">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1207">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e5457-1208">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1208">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e5457-1209">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1209">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="e5457-1210">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1210">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e5457-1211">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1211">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e5457-1212">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1212">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e5457-1213">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e5457-1213">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e5457-1214">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1214">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e5457-1215">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1215">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e5457-1216">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1216">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e5457-1217">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1217">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="e5457-1218">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1218">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="e5457-1219">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1219">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="e5457-1220">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1220">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e5457-1221">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1221">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="e5457-1222">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1222">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="e5457-1223">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1223">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="e5457-1224">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="e5457-1224">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="e5457-1225">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1225">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="e5457-1226">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1226">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="e5457-1227">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1227">The preferred method is to use WebPI.</span></span> <span data-ttu-id="e5457-1228">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1228">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e5457-1229">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="e5457-1229">Create the IIS site</span></span>

1. <span data-ttu-id="e5457-1230">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1230">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e5457-1231">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1231">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e5457-1232">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1232">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e5457-1233">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1233">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e5457-1234">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1234">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e5457-1235">상황에 맞는 메뉴에서 **웹 사이트 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1235">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e5457-1236">**사이트 이름** 을 제공하고 **실제 경로** 를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1236">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="e5457-1237">**바인딩** 구성을 제공하고 **확인** 을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1237">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="e5457-1239">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1239">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e5457-1240">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1240">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e5457-1241">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1241">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e5457-1242">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1242">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e5457-1243">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="e5457-1243">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e5457-1244">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1244">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e5457-1245">서버 노드에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1245">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="e5457-1246">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1246">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="e5457-1247">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1247">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="e5457-1249">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1249">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="e5457-1250">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1250">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="e5457-1251">**.NET CLR 버전** 을 **관리 코드 없음** 으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1251">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="e5457-1252">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1252">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="e5457-1253">IIS 관리자의 **애플리케이션 풀** 에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1253">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="e5457-1254">**32비트 애플리케이션 사용** 을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1254">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="e5457-1255">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1255">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="e5457-1256">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1256">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e5457-1257">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1257">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e5457-1258">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1258">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="e5457-1259">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="e5457-1259">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="e5457-1260">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1260">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="e5457-1261">앱 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-1261">Deploy the app</span></span>

<span data-ttu-id="e5457-1262">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1262">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="e5457-1263">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1263">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="e5457-1264">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-1264">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="e5457-1265">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1265">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="e5457-1266">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1266">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="e5457-1268">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-1268">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="e5457-1269">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1269">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="e5457-1270">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1270">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="e5457-1271">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="e5457-1271">Alternatives to Web Deploy</span></span>

<span data-ttu-id="e5457-1272">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1272">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="e5457-1273">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1273">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="e5457-1274">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="e5457-1274">Browse the website</span></span>

<span data-ttu-id="e5457-1275">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1275">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="e5457-1276">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름** 에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1276">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="e5457-1277">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1277">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="e5457-1279">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="e5457-1279">Locked deployment files</span></span>

<span data-ttu-id="e5457-1280">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1280">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="e5457-1281">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1281">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="e5457-1282">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나** 를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1282">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="e5457-1283">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1283">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="e5457-1284">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1284">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e5457-1285">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1285">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="e5457-1286">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1286">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="e5457-1287">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1287">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="e5457-1288">PowerShell을 사용하여 *app_offline.htm* 을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="e5457-1288">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="e5457-1289">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="e5457-1289">Data protection</span></span>

<span data-ttu-id="e5457-1290">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1290">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e5457-1291">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1291">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e5457-1292">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1292">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e5457-1293">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1293">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e5457-1294">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1294">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e5457-1295">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1295">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e5457-1296">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1296">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e5457-1297">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1297">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e5457-1298">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1298">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e5457-1299">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="e5457-1299">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="e5457-1300">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1300">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e5457-1301">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1301">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="e5457-1302">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1302">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e5457-1303">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1303">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e5457-1304">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1304">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e5457-1305">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1305">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="e5457-1306">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1306">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="e5457-1307">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1307">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="e5457-1308">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1308">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e5457-1309">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1309">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e5457-1310">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1310">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="e5457-1311">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="e5457-1311">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e5457-1312">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1312">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e5457-1313">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1313">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e5457-1314">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1314">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e5457-1315">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1315">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="e5457-1316">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1316">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e5457-1317">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1317">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e5457-1318">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1318">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e5457-1319">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1319">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e5457-1320">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1320">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="e5457-1321">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1321">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="e5457-1322">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1322">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e5457-1323">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1323">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e5457-1324">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="e5457-1324">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e5457-1325">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1325">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e5457-1326">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1326">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e5457-1327">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1327">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e5457-1328">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1328">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e5457-1329">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1329">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e5457-1330">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1330">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e5457-1331">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1331">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e5457-1332">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="e5457-1332">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="e5457-1333">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1333">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e5457-1334">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1334">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="e5457-1335">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="e5457-1335">Virtual Directories</span></span>

<span data-ttu-id="e5457-1336">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1336">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e5457-1337">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1337">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e5457-1338">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="e5457-1338">Sub-applications</span></span>

<span data-ttu-id="e5457-1339">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1339">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e5457-1340">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1340">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e5457-1341">하위 앱에는 ASP.NET Core 모듈이 처리기로 포함되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1341">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="e5457-1342">하위 앱의 *web.config* 파일에 모듈이 처리기로 추가되면, 하위 앱을 찾으려고 할 때 잘못된 구성 파일을 참조하는 *500.19 내부 서버 오류* 가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1342">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="e5457-1343">다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1343">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="e5457-1344">ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하는 경우, 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1344">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="e5457-1345">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1345">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e5457-1346">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1346">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e5457-1347">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1347">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e5457-1348">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1348">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e5457-1349">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1349">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e5457-1350">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1350">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e5457-1351">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1351">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e5457-1352">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1352">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e5457-1353">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1353">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e5457-1354">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1354">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e5457-1355">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1355">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e5457-1356">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1356">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e5457-1357">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1357">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e5457-1358">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1358">Select **OK**.</span></span>

<span data-ttu-id="e5457-1359">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1359">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e5457-1360">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1360">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="e5457-1361">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="e5457-1361">Configuration of IIS with web.config</span></span>

<span data-ttu-id="e5457-1362">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config* 에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1362">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="e5457-1363">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1363">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="e5457-1364">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1364">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="e5457-1365">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1365">For more information, see the following topics:</span></span>

* [<span data-ttu-id="e5457-1366">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="e5457-1366">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="e5457-1367">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1367">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="e5457-1368">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="e5457-1368">Configuration sections of web.config</span></span>

<span data-ttu-id="e5457-1369">*web.config* 에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1369">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="e5457-1370">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1370">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="e5457-1371">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1371">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="e5457-1372">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="e5457-1372">Application Pools</span></span>

<span data-ttu-id="e5457-1373">서버에서 여러 웹 사이트를 호스트하는 경우 각 앱을 해당 앱 풀에서 실행하여 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1373">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="e5457-1374">이 구성은 IIS **웹 사이트 추가** 대화 상자의 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1374">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="e5457-1375">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1375">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e5457-1376">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1376">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="e5457-1377">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="e5457-1377">Application Pool Identity</span></span>

<span data-ttu-id="e5457-1378">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1378">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e5457-1379">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1379">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e5457-1380">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1380">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="e5457-1382">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1382">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e5457-1383">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1383">Resources can be secured using this identity.</span></span> <span data-ttu-id="e5457-1384">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1384">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e5457-1385">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1385">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e5457-1386">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1386">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e5457-1387">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1387">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e5457-1388">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1388">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e5457-1389">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1389">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e5457-1390">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1390">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="e5457-1391">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1391">Select the **Check Names** button.</span></span> <span data-ttu-id="e5457-1392">*DefaultAppPool* 의 경우 **IIS AppPool\DefaultAppPool** 을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1392">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="e5457-1393">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1393">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="e5457-1394">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1394">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e5457-1395">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1395">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e5457-1397">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1397">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e5457-1399">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1399">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e5457-1400">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1400">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e5457-1401">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1401">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e5457-1402">*DefaultAppPool* 을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1402">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e5457-1403">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1403">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e5457-1404">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="e5457-1404">HTTP/2 support</span></span>

<span data-ttu-id="e5457-1405">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음 기본 요구 사항을 충족하는 Out of Process 배포에 대해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1405">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="e5457-1406">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="e5457-1406">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="e5457-1407">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1407">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="e5457-1408">대상 프레임워크: HTTP/2 연결은 IIS에 의해 완전히 처리되므로 Out of Process 배포에는 해당하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1408">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="e5457-1409">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="e5457-1409">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e5457-1410">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1410">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e5457-1411">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1411">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e5457-1412">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1412">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e5457-1413">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1413">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e5457-1414">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="e5457-1414">CORS preflight requests</span></span>

<span data-ttu-id="e5457-1415">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1415">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e5457-1416">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5457-1416">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e5457-1417">OPTIONS 요청을 전달하도록 *web.config* 에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e5457-1417">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e5457-1418">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="e5457-1418">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e5457-1419">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="e5457-1419">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e5457-1420">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="e5457-1420">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e5457-1421">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="e5457-1421">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="e5457-1422">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e5457-1422">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e5457-1423">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="e5457-1423">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e5457-1424">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e5457-1424">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e5457-1425">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e5457-1425">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
