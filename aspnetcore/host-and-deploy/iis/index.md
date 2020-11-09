---
title: IIS가 있는 Windows에서 ASP.NET Core 호스팅
author: rick-anderson
description: Windows Server IIS(인터넷 정보 서비스)에서 ASP.NET Core 앱을 호스팅하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: e4a94ca9e3607868f3eb25d88338e8156f7f5206
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061524"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="7ef5b-103">IIS가 있는 Windows에서 ASP.NET Core 호스팅</span><span class="sxs-lookup"><span data-stu-id="7ef5b-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7ef5b-104">IIS(인터넷 정보 서비스)는 ASP.NET Core를 비롯한 웹앱을 호스팅하기 위한 유연하고 안전하며 관리하기 쉬운 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-104">Internet Information Services (IIS) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7ef5b-105">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="7ef5b-105">Supported platforms</span></span>

<span data-ttu-id="7ef5b-106">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-106">The following operating systems are supported:</span></span>

* <span data-ttu-id="7ef5b-107">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-107">Windows 7 or later</span></span>
* <span data-ttu-id="7ef5b-108">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-108">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="7ef5b-109">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-109">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7ef5b-110">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-110">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7ef5b-111">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-111">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7ef5b-112">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-112">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7ef5b-113">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-113">Has 64-bit native dependencies.</span></span>

## <a name="install-the-aspnet-core-modulehosting-bundle"></a><span data-ttu-id="7ef5b-114">ASP.NET Core 모듈/호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-114">Install the ASP.NET Core Module/Hosting Bundle</span></span>

<span data-ttu-id="7ef5b-115">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-115">Download the installer using the following link:</span></span>

[<span data-ttu-id="7ef5b-116">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="7ef5b-116">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="7ef5b-117">ASP.NET Core 모듈을 설치하거나 다른 버전을 설치하는 방법에 대한 자세한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-117">For more details instructions on how to install the ASP.NET Core Module, or installing different versions, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

## <a name="get-started"></a><span data-ttu-id="7ef5b-118">시작하기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-118">Get started</span></span>

<span data-ttu-id="7ef5b-119">IIS에서 웹 사이트 호스팅을 시작하려면 [시작 가이드](xref:tutorials/publish-to-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-119">For getting started with hosting a website on IIS, see our [getting started guide](xref:tutorials/publish-to-iis).</span></span>

<span data-ttu-id="7ef5b-120">Azure App Services에서 웹 사이트 호스팅을 시작하려면 [Azure App Service에 배포 가이드](xref:host-and-deploy/azure-apps/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7ef5b-121">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="7ef5b-121">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7ef5b-122">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="7ef5b-122">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7ef5b-123">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="7ef5b-123">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7ef5b-124">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-124">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7ef5b-125">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ef5b-125">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7ef5b-126">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="7ef5b-126">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7ef5b-127">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-127">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7ef5b-128">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ef5b-128">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7ef5b-129">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-129">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="7ef5b-130">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-130">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="7ef5b-131">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="7ef5b-131">Supported operating systems</span></span>

<span data-ttu-id="7ef5b-132">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-132">The following operating systems are supported:</span></span>

* <span data-ttu-id="7ef5b-133">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-133">Windows 7 or later</span></span>
* <span data-ttu-id="7ef5b-134">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-134">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="7ef5b-135">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-135">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="7ef5b-136">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-136">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7ef5b-137">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-137">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="7ef5b-138">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-138">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7ef5b-139">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="7ef5b-139">Supported platforms</span></span>

<span data-ttu-id="7ef5b-140">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-140">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7ef5b-141">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-141">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7ef5b-142">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-142">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7ef5b-143">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-143">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7ef5b-144">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-144">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="7ef5b-145">32비트(x86)용으로 게시된 앱은 해당 IIS 애플리케이션 풀에 대해 32비트를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-145">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="7ef5b-146">자세한 내용은 [IIS 사이트 만들기](#create-the-iis-site) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-146">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="7ef5b-147">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-147">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="7ef5b-148">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-148">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="7ef5b-149">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7ef5b-149">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="7ef5b-150">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7ef5b-150">In-process hosting model</span></span>

<span data-ttu-id="7ef5b-151">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-151">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="7ef5b-152">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-152">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="7ef5b-153">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-153">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7ef5b-154">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="7ef5b-154">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="7ef5b-155">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-155">Performs app initialization.</span></span>
  * <span data-ttu-id="7ef5b-156">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-156">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="7ef5b-157">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-157">Calls `Program.Main`.</span></span>
* <span data-ttu-id="7ef5b-158">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-158">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="7ef5b-159">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-159">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="7ef5b-161">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-161">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="7ef5b-162">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-162">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="7ef5b-163">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-163">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="7ef5b-164">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-164">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="7ef5b-165">IIS HTTP 서버에서 요청을 처리한 후:</span><span class="sxs-lookup"><span data-stu-id="7ef5b-165">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="7ef5b-166">해당 요청은 ASP.NET Core 미들웨어 파이프라인으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-166">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="7ef5b-167">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="7ef5b-168">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-168">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="7ef5b-169">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-169">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="7ef5b-170">In Process 호스팅은 기존 앱에 대해 옵트인(opt in)됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-170">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="7ef5b-171">ASP.NET Core 웹 템플릿은 In Process 호스팅 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-171">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="7ef5b-172">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(`w3wp.exe` 또는 `iisexpress.exe`) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-172">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="7ef5b-173">성능 테스트의 결과 .NET Core 앱 in-process를 호스팅하는 것이 앱 out-of-process 및 [Kestrel](xref:fundamentals/servers/kestrel)에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-173">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7ef5b-174">단일 실행 파일로 게시된 앱은 In Process 호스팅 모델을 통해 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-174">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="7ef5b-175">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7ef5b-175">Out-of-process hosting model</span></span>

<span data-ttu-id="7ef5b-176">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-176">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="7ef5b-177">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-177">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7ef5b-178">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-178">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7ef5b-179">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-179">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="7ef5b-181">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-181">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="7ef5b-182">드라이버는 웹 사이트의 구성된 포트에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-182">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="7ef5b-183">구성된 포트는 일반적으로 80(HTTP) 또는 443(HTTPS)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-183">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="7ef5b-184">모듈은 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-184">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="7ef5b-185">임의 포트는 80 또는 443이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-185">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="7ef5b-186">ASP.NET Core 모듈은 시작 시 환경 변수를 통해 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-186">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="7ef5b-187"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 확장은 `http://localhost:{PORT}`에서 수신하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-187">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="7ef5b-188">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-188">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7ef5b-189">이 모듈은 HTTPS 전달을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-189">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="7ef5b-190">HTTPS를 통해 IIS에서 수신하는 경우에도 요청은 HTTP를 통해 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-190">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7ef5b-191">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-191">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7ef5b-192">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-192">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7ef5b-193">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-193">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7ef5b-194">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-194">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7ef5b-195">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-195">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7ef5b-196">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-196">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7ef5b-197">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-197">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7ef5b-198">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="7ef5b-198">Enable the IISIntegration components</span></span>

<span data-ttu-id="7ef5b-199">`CreateHostBuilder`(`Program.cs`)에서 호스트를 빌드하는 경우 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-199">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7ef5b-200">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#default-builder-settings>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-200">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="7ef5b-201">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-201">IIS options</span></span>

<span data-ttu-id="7ef5b-202">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-202">**In-process hosting model**</span></span>

<span data-ttu-id="7ef5b-203">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-203">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7ef5b-204">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-204">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="7ef5b-205">옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-205">Option</span></span>                         | <span data-ttu-id="7ef5b-206">기본값</span><span class="sxs-lookup"><span data-stu-id="7ef5b-206">Default</span></span> | <span data-ttu-id="7ef5b-207">설정</span><span class="sxs-lookup"><span data-stu-id="7ef5b-207">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7ef5b-208">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-208">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7ef5b-209">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-209">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7ef5b-210">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-210">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7ef5b-211">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-211">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7ef5b-212">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-212">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="7ef5b-213">`HttpContext.Request` 및 `HttpContext.Response`에 대해 동기 I/O가 허용되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-213">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="7ef5b-214">`HttpRequest`의 최대 요청 본문 크기를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-214">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="7ef5b-215">IIS 자체에는 `IISServerOptions`에 설정된 `MaxRequestBodySize` 앞에 처리되는 `maxAllowedContentLength` 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-215">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="7ef5b-216">`MaxRequestBodySize`를 변경해도 `maxAllowedContentLength`에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-216">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="7ef5b-217">`maxAllowedContentLength`를 늘리려면 `web.config`에 항목을 추가하여 `maxAllowedContentLength`를 더 높은 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-217">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="7ef5b-218">자세한 내용은 [구성](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-218">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="7ef5b-219">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-219">**Out-of-process hosting model**</span></span>

<span data-ttu-id="7ef5b-220">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-220">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7ef5b-221">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-221">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7ef5b-222">옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-222">Option</span></span>                         | <span data-ttu-id="7ef5b-223">기본값</span><span class="sxs-lookup"><span data-stu-id="7ef5b-223">Default</span></span> | <span data-ttu-id="7ef5b-224">설정</span><span class="sxs-lookup"><span data-stu-id="7ef5b-224">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7ef5b-225">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-225">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7ef5b-226">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-226">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7ef5b-227">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-227">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7ef5b-228">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-228">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7ef5b-229">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-229">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7ef5b-230">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-230">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7ef5b-231">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7ef5b-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7ef5b-232">[IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 다음을 전달하도록 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-232">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="7ef5b-233">체계(HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-233">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="7ef5b-234">요청이 시작된 원격 IP 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-234">Remote IP address where the request originated.</span></span>

<span data-ttu-id="7ef5b-235">[IIS 통합 미들웨어](#enable-the-iisintegration-components)는 전달된 헤더 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-235">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="7ef5b-236">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-236">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7ef5b-237">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-237">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="7ef5b-238">`web.config` 파일:</span><span class="sxs-lookup"><span data-stu-id="7ef5b-238">`web.config` file</span></span>

<span data-ttu-id="7ef5b-239">`web.config` 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-239">The `web.config` file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ef5b-240">`web.config` 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-240">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="7ef5b-241">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-241">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="7ef5b-242">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-242">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7ef5b-243">프로젝트에 `web.config` 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments` 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-243">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="7ef5b-244">프로젝트에 `web.config` 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 `processPath` 및 `arguments`로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-244">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="7ef5b-245">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-245">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="7ef5b-246">`web.config` 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-246">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="7ef5b-247">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-247">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="7ef5b-248">웹 SDK가 `web.config` 파일을 변환하지 못하도록 하려면 프로젝트 파일의 `<IsTransformWebConfigDisabled>` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-248">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="7ef5b-249">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 `processPath` 및 `arguments`를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-249">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="7ef5b-250">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-250">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="7ef5b-251">`web.config` 파일 위치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-251">`web.config` file location</span></span>

<span data-ttu-id="7ef5b-252">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 `web.config` 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-252">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="7ef5b-253">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-253">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="7ef5b-254">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 `web.config` 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-254">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="7ef5b-255">중요한 파일은 `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml`(XML 문서 주석) 및 `{ASSEMBLY}.deps.json`과 같은 앱의 실제 경로에 있습니다. 여기서 자리 표시자 `{ASSEMBLY}`는 어셈플리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-255">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="7ef5b-256">`web.config` 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-256">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="7ef5b-257">`web.config` 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-257">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="7ef5b-258">**`web.config` 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 `web.config` 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-258">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="7ef5b-259">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="7ef5b-259">Transform web.config</span></span>

<span data-ttu-id="7ef5b-260">게시할 때 `web.config`를 변환해야 하는 경우 <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-260">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="7ef5b-261">구성, 프로필 또는 환경을 기반으로 하는 환경 변수를 설정하려면 게시할 때 `web.config`를 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-261">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="7ef5b-262">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-262">IIS configuration</span></span>

<span data-ttu-id="7ef5b-263">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-263">**Windows Server operating systems**</span></span>

<span data-ttu-id="7ef5b-264">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-264">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="7ef5b-265">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-265">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span> <span data-ttu-id="7ef5b-266">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-266">On the **Server Roles** step, check the box for **Web Server (IIS)** .</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="7ef5b-268">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-268">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="7ef5b-269">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-269">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="7ef5b-271">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-271">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-272">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-272">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security** .</span></span> <span data-ttu-id="7ef5b-273">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-273">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7ef5b-274">자세한 내용은 [Windows 인증 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-274">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7ef5b-275">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-275">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-276">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-276">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ef5b-277">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-277">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development** .</span></span> <span data-ttu-id="7ef5b-278">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-278">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ef5b-279">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-279">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7ef5b-280">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-280">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="7ef5b-281">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-281">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="7ef5b-282">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-282">**Windows desktop operating systems**</span></span>

<span data-ttu-id="7ef5b-283">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-283">Enable the **IIS Management Console** and **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7ef5b-284">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-284">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="7ef5b-285">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-285">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="7ef5b-286">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-286">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="7ef5b-287">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-287">Check the box for **IIS Management Console** .</span></span>

1. <span data-ttu-id="7ef5b-288">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-288">Check the box for **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7ef5b-289">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-289">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="7ef5b-290">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-290">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-291">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-291">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security** .</span></span> <span data-ttu-id="7ef5b-292">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-292">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7ef5b-293">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-293">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7ef5b-294">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-294">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-295">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-295">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ef5b-296">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-296">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features** .</span></span> <span data-ttu-id="7ef5b-297">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-297">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ef5b-298">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-298">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7ef5b-299">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-299">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7ef5b-301">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-301">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7ef5b-302">호스팅 시스템에 *.NET Core 호스팅 번들* 을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-302">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="7ef5b-303">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-303">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ef5b-304">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-304">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7ef5b-305">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-305">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7ef5b-306">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-306">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7ef5b-307">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-307">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7ef5b-308">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-308">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="7ef5b-309">직접 다운로드(현재 버전)</span><span class="sxs-lookup"><span data-stu-id="7ef5b-309">Direct download (current version)</span></span>

<span data-ttu-id="7ef5b-310">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-310">Download the installer using the following link:</span></span>

[<span data-ttu-id="7ef5b-311">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="7ef5b-311">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="7ef5b-312">이전 버전의 설치 관리자</span><span class="sxs-lookup"><span data-stu-id="7ef5b-312">Earlier versions of the installer</span></span>

<span data-ttu-id="7ef5b-313">이전 버전의 설치 관리자를 가져오려면:</span><span class="sxs-lookup"><span data-stu-id="7ef5b-313">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="7ef5b-314">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-314">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7ef5b-315">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-315">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7ef5b-316">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-316">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7ef5b-317">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-317">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7ef5b-318">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-318">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7ef5b-319">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-319">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="7ef5b-320">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-320">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="7ef5b-321">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-321">Run the installer on the server.</span></span> <span data-ttu-id="7ef5b-322">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-322">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7ef5b-323">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-323">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7ef5b-324">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-324">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7ef5b-325">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-325">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ef5b-326">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-326">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7ef5b-327">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-327">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ef5b-328">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-328">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7ef5b-329">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-329">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7ef5b-330">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-330">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7ef5b-331">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(`applicationHost.config`)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-331">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7ef5b-332">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="7ef5b-332">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7ef5b-333">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-333">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="7ef5b-334">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-334">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="7ef5b-335">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-335">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="7ef5b-336">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-336">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="7ef5b-337">새 호스팅 번들을 설치하여 공유 프레임워크를 업그레이드한 후, 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-337">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="7ef5b-338">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-338">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="7ef5b-339">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-339">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="7ef5b-340">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-340">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="7ef5b-341">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-341">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="7ef5b-342">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-342">The preferred method is to use WebPI.</span></span> <span data-ttu-id="7ef5b-343">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-343">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7ef5b-344">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-344">Create the IIS site</span></span>

1. <span data-ttu-id="7ef5b-345">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-345">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7ef5b-346">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-346">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="7ef5b-347">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-347">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="7ef5b-348">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-348">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7ef5b-349">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-349">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7ef5b-350">상황에 맞는 메뉴에서 **웹 사이트 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-350">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7ef5b-351">**사이트 이름** 을 제공하고 **실제 경로** 를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-351">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="7ef5b-352">**바인딩** 구성을 제공하고 **확인** 을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-352">Provide the **Binding** configuration and create the website by selecting **OK** :</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="7ef5b-354">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-354">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7ef5b-355">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-355">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="7ef5b-356">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-356">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7ef5b-357">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-357">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="7ef5b-358">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-358">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7ef5b-359">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-359">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="7ef5b-360">서버 노드에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-360">Under the server's node, select **Application Pools** .</span></span>

1. <span data-ttu-id="7ef5b-361">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-361">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="7ef5b-362">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-362">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code** :</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="7ef5b-364">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-364">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="7ef5b-365">ASP.NET Core에서는 데스크톱 CLR(.NET CLR)을 로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-365">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="7ef5b-366">.NET Core에 대한 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-366">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="7ef5b-367">**.NET CLR 버전** 을 **관리 코드 없음** 으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-367">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="7ef5b-368">*ASP.NET Core 2.2 이상* :</span><span class="sxs-lookup"><span data-stu-id="7ef5b-368">*ASP.NET Core 2.2 or later* :</span></span>

   * <span data-ttu-id="7ef5b-369">[In-Process 호스팅 모델](#in-process-hosting-model)을 사용하는 32비트 SDK로 게시된 32비트(x86) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트의 애플리케이션 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-369">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="7ef5b-370">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-370">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="7ef5b-371">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-371">Select the app's Application Pool.</span></span> <span data-ttu-id="7ef5b-372">**작업** 사이드바에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-372">In the **Actions** sidebar, select **Advanced Settings** .</span></span> <span data-ttu-id="7ef5b-373">**32비트 애플리케이션 사용** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-373">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="7ef5b-374">[In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-374">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="7ef5b-375">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀** 로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-375">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="7ef5b-376">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-376">Select the app's Application Pool.</span></span> <span data-ttu-id="7ef5b-377">**작업** 사이드바에서 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-377">In the **Actions** sidebar, select **Advanced Settings** .</span></span> <span data-ttu-id="7ef5b-378">**32비트 애플리케이션 사용** 을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-378">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="7ef5b-379">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-379">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="7ef5b-380">애플리케이션 풀의 기본 ID( **프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-380">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="7ef5b-381">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-381">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="7ef5b-382">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-382">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="7ef5b-383">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-383">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="7ef5b-384">앱 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-384">Deploy the app</span></span>

<span data-ttu-id="7ef5b-385">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-385">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="7ef5b-386">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 `publish` 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-386">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's `publish` folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="7ef5b-387">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-387">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="7ef5b-388">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-388">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="7ef5b-389">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-389">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="7ef5b-391">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-391">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="7ef5b-392">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-392">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="7ef5b-393">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-393">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="7ef5b-394">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="7ef5b-394">Alternatives to Web Deploy</span></span>

<span data-ttu-id="7ef5b-395">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-395">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="7ef5b-396">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-396">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="7ef5b-397">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-397">Browse the website</span></span>

<span data-ttu-id="7ef5b-398">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-398">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="7ef5b-399">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름** 에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-399">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="7ef5b-400">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-400">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="7ef5b-402">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="7ef5b-402">Locked deployment files</span></span>

<span data-ttu-id="7ef5b-403">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-403">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="7ef5b-404">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-404">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="7ef5b-405">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나** 를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-405">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-406">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-406">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="7ef5b-407">`app_offline.htm` 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-407">An `app_offline.htm` file is placed at the root of the web app directory.</span></span> <span data-ttu-id="7ef5b-408">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 `app_offline.htm` 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-408">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the `app_offline.htm` file during the deployment.</span></span> <span data-ttu-id="7ef5b-409">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-409">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="7ef5b-410">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-410">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="7ef5b-411">PowerShell을 사용하여 `app_offline.htm`을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-411">Use PowerShell to drop `app_offline.htm` (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a><span data-ttu-id="7ef5b-412">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="7ef5b-412">Data protection</span></span>

<span data-ttu-id="7ef5b-413">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-413">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="7ef5b-414">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-414">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="7ef5b-415">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-415">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="7ef5b-416">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-416">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="7ef5b-417">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-417">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="7ef5b-418">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-418">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="7ef5b-419">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-419">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="7ef5b-420">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-420">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="7ef5b-421">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-421">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-422">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-422">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="7ef5b-423">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-423">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="7ef5b-424">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-424">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="7ef5b-425">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-425">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="7ef5b-426">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-426">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="7ef5b-427">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-427">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="7ef5b-428">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-428">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="7ef5b-429">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-429">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="7ef5b-430">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-430">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="7ef5b-431">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-431">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="7ef5b-432">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-432">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="7ef5b-433">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-433">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="7ef5b-434">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-434">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="7ef5b-435">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-435">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="7ef5b-436">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-436">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="7ef5b-437">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-437">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="7ef5b-438">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-438">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="7ef5b-439">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-439">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="7ef5b-440">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-440">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="7ef5b-441">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-441">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="7ef5b-442">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-442">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="7ef5b-443">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-443">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="7ef5b-444">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-444">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="7ef5b-445">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-445">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="7ef5b-446">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-446">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="7ef5b-447">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-447">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="7ef5b-448">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-448">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="7ef5b-449">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-449">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="7ef5b-450">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-450">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="7ef5b-451">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-451">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="7ef5b-452">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-452">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="7ef5b-453">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-453">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="7ef5b-454">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-454">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="7ef5b-455">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-455">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="7ef5b-456">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-456">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="7ef5b-457">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-457">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="7ef5b-458">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-458">Virtual Directories</span></span>

<span data-ttu-id="7ef5b-459">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-459">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="7ef5b-460">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-460">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="7ef5b-461">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-461">Sub-applications</span></span>

<span data-ttu-id="7ef5b-462">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-462">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="7ef5b-463">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-463">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="7ef5b-464">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-464">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="7ef5b-465">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-465">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="7ef5b-466">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-466">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="7ef5b-467">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-467">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="7ef5b-468">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-468">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="7ef5b-469">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-469">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="7ef5b-470">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-470">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="7ef5b-471">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-471">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="7ef5b-472">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-472">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="7ef5b-473">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-473">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="7ef5b-474">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-474">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="7ef5b-475">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application** (애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-475">Right-click the sub-app folder in IIS Manager and select **Convert to Application** .</span></span>

1. <span data-ttu-id="7ef5b-476">**Add Application** (애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-476">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="7ef5b-477">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-477">Select **OK** .</span></span>

<span data-ttu-id="7ef5b-478">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-478">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="7ef5b-479">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-479">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="7ef5b-480">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-480">Configuration of IIS with web.config</span></span>

<span data-ttu-id="7ef5b-481">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config* 에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-481">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="7ef5b-482">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-482">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="7ef5b-483">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-483">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="7ef5b-484">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-484">For more information, see the following topics:</span></span>

* [<span data-ttu-id="7ef5b-485">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="7ef5b-485">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="7ef5b-486">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-486">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="7ef5b-487">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-487">Configuration sections of web.config</span></span>

<span data-ttu-id="7ef5b-488">*web.config* 에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-488">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="7ef5b-489">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-489">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="7ef5b-490">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-490">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="7ef5b-491">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="7ef5b-491">Application Pools</span></span>

<span data-ttu-id="7ef5b-492">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-492">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="7ef5b-493">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-493">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="7ef5b-494">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-494">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="7ef5b-495">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-495">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="7ef5b-496">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-496">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="7ef5b-497">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-497">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="7ef5b-498">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="7ef5b-498">Application Pool Identity</span></span>

<span data-ttu-id="7ef5b-499">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-499">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="7ef5b-500">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-500">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="7ef5b-501">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-501">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** :</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="7ef5b-503">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-503">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="7ef5b-504">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-504">Resources can be secured using this identity.</span></span> <span data-ttu-id="7ef5b-505">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-505">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="7ef5b-506">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-506">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="7ef5b-507">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-507">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="7ef5b-508">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-508">Right-click on the directory and select **Properties** .</span></span>

1. <span data-ttu-id="7ef5b-509">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-509">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="7ef5b-510">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-510">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="7ef5b-511">**선택할 개체 이름 입력** 영역에 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-511">Enter `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="7ef5b-512">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-512">Select the **Check Names** button.</span></span> <span data-ttu-id="7ef5b-513">*DefaultAppPool* 의 경우 `IIS AppPool\DefaultAppPool`을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-513">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="7ef5b-514">**이름 확인** 단추를 선택하면 개체 이름 영역에 `DefaultAppPool` 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-514">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="7ef5b-515">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-515">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="7ef5b-516">개체 이름을 확인할 때 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-516">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="7ef5b-518">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-518">Select **OK** .</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="7ef5b-520">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-520">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="7ef5b-521">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-521">Provide additional permissions as needed.</span></span>

<span data-ttu-id="7ef5b-522">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-522">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="7ef5b-523">`DefaultAppPool`을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-523">Using the `DefaultAppPool` as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="7ef5b-524">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-524">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7ef5b-525">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7ef5b-525">HTTP/2 support</span></span>

<span data-ttu-id="7ef5b-526">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-526">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="7ef5b-527">In-Process</span><span class="sxs-lookup"><span data-stu-id="7ef5b-527">In-process</span></span>
  * <span data-ttu-id="7ef5b-528">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-528">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7ef5b-529">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7ef5b-529">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="7ef5b-530">Out of Process</span><span class="sxs-lookup"><span data-stu-id="7ef5b-530">Out-of-process</span></span>
  * <span data-ttu-id="7ef5b-531">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-531">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7ef5b-532">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-532">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7ef5b-533">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7ef5b-533">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7ef5b-534">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-534">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="7ef5b-535">Out-of-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-535">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7ef5b-536">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-536">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7ef5b-537">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-537">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7ef5b-538">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-538">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="7ef5b-539">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-539">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="7ef5b-540">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="7ef5b-540">CORS preflight requests</span></span>

<span data-ttu-id="7ef5b-541">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-541">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="7ef5b-542">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-542">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="7ef5b-543">OPTIONS 요청을 전달하도록 `web.config`에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-543">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="7ef5b-544">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="7ef5b-544">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="7ef5b-545">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="7ef5b-545">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="7ef5b-546">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-546">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="7ef5b-547">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-547">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="7ef5b-548">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="7ef5b-548">Application Initialization Module</span></span>

<span data-ttu-id="7ef5b-549">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="7ef5b-549">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="7ef5b-550">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-550">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="7ef5b-551">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-551">The request triggers the app to start.</span></span> <span data-ttu-id="7ef5b-552">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-552">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="7ef5b-553">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-553">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="7ef5b-554">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7ef5b-554">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="7ef5b-555">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-555">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7ef5b-556">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-556">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features** .</span></span>
1. <span data-ttu-id="7ef5b-557">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-557">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7ef5b-558">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-558">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="7ef5b-559">**역할 및 기능 추가 마법사** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-559">Open the **Add Roles and Features Wizard** .</span></span>
1. <span data-ttu-id="7ef5b-560">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-560">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="7ef5b-561">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-561">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7ef5b-562">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-562">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="7ef5b-563">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="7ef5b-563">Using IIS Manager:</span></span>

  1. <span data-ttu-id="7ef5b-564">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-564">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7ef5b-565">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-565">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7ef5b-566">기본 **시작 모드** 는 **OnDemand** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-566">The default **Start Mode** is **OnDemand** .</span></span> <span data-ttu-id="7ef5b-567">**시작 모드** 를 **AlwaysRunning** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-567">Set the **Start Mode** to **AlwaysRunning** .</span></span> <span data-ttu-id="7ef5b-568">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-568">Select **OK** .</span></span>
  1. <span data-ttu-id="7ef5b-569">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-569">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7ef5b-570">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-570">Right-click the app and select **Manage Website** > **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7ef5b-571">기본 **미리 로드 사용** 설정은 **False** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-571">The default **Preload Enabled** setting is **False** .</span></span> <span data-ttu-id="7ef5b-572">**미리 로드 사용** 을 **True** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-572">Set **Preload Enabled** to **True** .</span></span> <span data-ttu-id="7ef5b-573">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-573">Select **OK** .</span></span>

* <span data-ttu-id="7ef5b-574">`web.config`를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 web.config` 파일에 있는 `<system.webServer>\` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-574">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's web.config\` file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="7ef5b-575">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="7ef5b-575">Idle Timeout</span></span>

<span data-ttu-id="7ef5b-576">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="7ef5b-576">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="7ef5b-577">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-577">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="7ef5b-578">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-578">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="7ef5b-579">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-579">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
1. <span data-ttu-id="7ef5b-580">기본 **유휴 시간 제한(분)** 은 **20** 분입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-580">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="7ef5b-581">**유휴 시간 제한(분)** 을 **0** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-581">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="7ef5b-582">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-582">Select **OK** .</span></span>
1. <span data-ttu-id="7ef5b-583">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-583">Recycle the worker process.</span></span>

<span data-ttu-id="7ef5b-584">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-584">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-585">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-585">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="7ef5b-586">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-586">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="7ef5b-587">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7ef5b-587">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="7ef5b-588">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="7ef5b-588">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="7ef5b-589">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-589">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="7ef5b-590">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-590">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7ef5b-591">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="7ef5b-591">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7ef5b-592">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="7ef5b-592">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7ef5b-593">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="7ef5b-593">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7ef5b-594">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-594">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7ef5b-595">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ef5b-595">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7ef5b-596">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="7ef5b-596">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7ef5b-597">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-597">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7ef5b-598">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ef5b-598">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7ef5b-599">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-599">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="7ef5b-600">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-600">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="7ef5b-601">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="7ef5b-601">Supported operating systems</span></span>

<span data-ttu-id="7ef5b-602">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-602">The following operating systems are supported:</span></span>

* <span data-ttu-id="7ef5b-603">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-603">Windows 7 or later</span></span>
* <span data-ttu-id="7ef5b-604">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-604">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7ef5b-605">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-605">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="7ef5b-606">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-606">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7ef5b-607">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-607">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="7ef5b-608">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-608">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7ef5b-609">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="7ef5b-609">Supported platforms</span></span>

<span data-ttu-id="7ef5b-610">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-610">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7ef5b-611">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-611">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7ef5b-612">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-612">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7ef5b-613">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-613">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7ef5b-614">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-614">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="7ef5b-615">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-615">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="7ef5b-616">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-616">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="7ef5b-617">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7ef5b-617">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="7ef5b-618">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7ef5b-618">In-process hosting model</span></span>

<span data-ttu-id="7ef5b-619">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-619">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="7ef5b-620">In Process 호스팅은 다음과 같은 이유로 Out of Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-620">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="7ef5b-621">요청은 루프백 어댑터를 통해 프록시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-621">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="7ef5b-622">루프백 어댑터는 나가는 네트워크 트래픽을 동일한 컴퓨터로 다시 반환하는 네트워크 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-622">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="7ef5b-623">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-623">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7ef5b-624">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="7ef5b-624">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="7ef5b-625">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-625">Performs app initialization.</span></span>
  * <span data-ttu-id="7ef5b-626">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-626">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="7ef5b-627">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-627">Calls `Program.Main`.</span></span>
* <span data-ttu-id="7ef5b-628">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-628">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="7ef5b-629">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-629">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="7ef5b-630">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-630">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

<span data-ttu-id="7ef5b-632">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-632">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7ef5b-633">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-633">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7ef5b-634">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-634">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="7ef5b-635">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-635">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="7ef5b-636">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-636">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7ef5b-637">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-637">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7ef5b-638">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-638">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="7ef5b-639">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-639">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="7ef5b-640">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-Process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-640">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="7ef5b-641">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스( *w3wp.exe* 또는 *iisexpress.exe* ) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-641">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process ( *w3wp.exe* or *iisexpress.exe* ).</span></span> <span data-ttu-id="7ef5b-642">성능 테스트의 결과 .NET Core 앱을 in-process로 호스트하는 것이 앱을 out-of-process로 호스트하고 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-642">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="7ef5b-643">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="7ef5b-643">Out-of-process hosting model</span></span>

<span data-ttu-id="7ef5b-644">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-644">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="7ef5b-645">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-645">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7ef5b-646">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-646">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7ef5b-647">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-647">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="7ef5b-649">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-649">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7ef5b-650">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-650">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7ef5b-651">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-651">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7ef5b-652">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-652">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="7ef5b-653">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-653">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7ef5b-654">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-654">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7ef5b-655">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-655">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7ef5b-656">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-656">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7ef5b-657">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-657">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7ef5b-658">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-658">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7ef5b-659">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-659">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7ef5b-660">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-660">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7ef5b-661">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-661">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7ef5b-662">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="7ef5b-662">Enable the IISIntegration components</span></span>

<span data-ttu-id="7ef5b-663">`CreateWebHostBuilder`( *Program.cs* )에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-663">When building a host in `CreateWebHostBuilder` ( *Program.cs* ), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7ef5b-664">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-664">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="7ef5b-665">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-665">IIS options</span></span>

<span data-ttu-id="7ef5b-666">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-666">**In-process hosting model**</span></span>

<span data-ttu-id="7ef5b-667">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-667">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7ef5b-668">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-668">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="7ef5b-669">옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-669">Option</span></span>                         | <span data-ttu-id="7ef5b-670">기본값</span><span class="sxs-lookup"><span data-stu-id="7ef5b-670">Default</span></span> | <span data-ttu-id="7ef5b-671">설정</span><span class="sxs-lookup"><span data-stu-id="7ef5b-671">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7ef5b-672">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-672">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7ef5b-673">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-673">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7ef5b-674">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-674">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7ef5b-675">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-675">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7ef5b-676">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-676">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="7ef5b-677">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-677">**Out-of-process hosting model**</span></span>

<span data-ttu-id="7ef5b-678">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-678">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="7ef5b-679">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-679">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7ef5b-680">옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-680">Option</span></span>                         | <span data-ttu-id="7ef5b-681">기본값</span><span class="sxs-lookup"><span data-stu-id="7ef5b-681">Default</span></span> | <span data-ttu-id="7ef5b-682">설정</span><span class="sxs-lookup"><span data-stu-id="7ef5b-682">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7ef5b-683">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-683">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7ef5b-684">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-684">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7ef5b-685">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-685">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7ef5b-686">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-686">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7ef5b-687">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-687">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7ef5b-688">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-688">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7ef5b-689">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7ef5b-689">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7ef5b-690">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-690">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="7ef5b-691">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-691">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7ef5b-692">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-692">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="7ef5b-693">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="7ef5b-693">web.config file</span></span>

<span data-ttu-id="7ef5b-694">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-694">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ef5b-695">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-695">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="7ef5b-696">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-696">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="7ef5b-697">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-697">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7ef5b-698">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-698">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="7ef5b-699">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수* 로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-699">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="7ef5b-700">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-700">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="7ef5b-701">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-701">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="7ef5b-702">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-702">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="7ef5b-703">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-703">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="7ef5b-704">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수* 를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-704">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="7ef5b-705">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-705">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="7ef5b-706">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-706">web.config file location</span></span>

<span data-ttu-id="7ef5b-707">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-707">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="7ef5b-708">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-708">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="7ef5b-709">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-709">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="7ef5b-710">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json* , *\<assembly>assembly>.xml* (XML 문서 주석) 및 *\<assembly>assembly>.deps.json* 과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-710">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json* , *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json* .</span></span> <span data-ttu-id="7ef5b-711">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-711">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="7ef5b-712">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-712">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="7ef5b-713">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-713">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="7ef5b-714">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="7ef5b-714">Transform web.config</span></span>

<span data-ttu-id="7ef5b-715">게시할 때 *web.config* 를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-715">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="7ef5b-716">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-716">IIS configuration</span></span>

<span data-ttu-id="7ef5b-717">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-717">**Windows Server operating systems**</span></span>

<span data-ttu-id="7ef5b-718">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-718">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="7ef5b-719">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-719">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span> <span data-ttu-id="7ef5b-720">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-720">On the **Server Roles** step, check the box for **Web Server (IIS)** .</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="7ef5b-722">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-722">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="7ef5b-723">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-723">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="7ef5b-725">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-725">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-726">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-726">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security** .</span></span> <span data-ttu-id="7ef5b-727">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-727">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7ef5b-728">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-728">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7ef5b-729">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-729">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-730">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-730">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ef5b-731">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-731">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development** .</span></span> <span data-ttu-id="7ef5b-732">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-732">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ef5b-733">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-733">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7ef5b-734">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-734">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="7ef5b-735">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-735">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="7ef5b-736">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-736">**Windows desktop operating systems**</span></span>

<span data-ttu-id="7ef5b-737">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-737">Enable the **IIS Management Console** and **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7ef5b-738">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-738">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="7ef5b-739">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-739">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="7ef5b-740">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-740">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="7ef5b-741">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-741">Check the box for **IIS Management Console** .</span></span>

1. <span data-ttu-id="7ef5b-742">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-742">Check the box for **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7ef5b-743">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-743">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="7ef5b-744">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-744">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-745">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-745">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security** .</span></span> <span data-ttu-id="7ef5b-746">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-746">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7ef5b-747">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-747">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7ef5b-748">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-748">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-749">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-749">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ef5b-750">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-750">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features** .</span></span> <span data-ttu-id="7ef5b-751">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-751">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ef5b-752">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-752">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7ef5b-753">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-753">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7ef5b-755">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-755">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7ef5b-756">호스팅 시스템에 *.NET Core 호스팅 번들* 을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-756">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="7ef5b-757">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-757">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ef5b-758">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-758">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7ef5b-759">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-759">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7ef5b-760">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-760">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7ef5b-761">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-761">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7ef5b-762">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-762">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="7ef5b-763">다운로드</span><span class="sxs-lookup"><span data-stu-id="7ef5b-763">Download</span></span>

1. <span data-ttu-id="7ef5b-764">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-764">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7ef5b-765">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-765">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7ef5b-766">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-766">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7ef5b-767">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-767">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7ef5b-768">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-768">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7ef5b-769">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-769">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="7ef5b-770">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-770">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="7ef5b-771">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-771">Run the installer on the server.</span></span> <span data-ttu-id="7ef5b-772">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-772">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7ef5b-773">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-773">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7ef5b-774">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-774">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7ef5b-775">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-775">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ef5b-776">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-776">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7ef5b-777">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-777">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ef5b-778">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-778">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7ef5b-779">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-779">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7ef5b-780">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-780">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7ef5b-781">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성( *applicationHost.config* )이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-781">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration ( *applicationHost.config* ) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7ef5b-782">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="7ef5b-782">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7ef5b-783">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-783">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="7ef5b-784">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-784">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="7ef5b-785">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-785">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="7ef5b-786">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-786">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="7ef5b-787">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-787">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="7ef5b-788">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-788">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="7ef5b-789">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-789">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="7ef5b-790">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-790">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="7ef5b-791">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-791">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="7ef5b-792">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-792">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="7ef5b-793">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-793">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="7ef5b-794">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-794">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="7ef5b-795">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-795">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="7ef5b-796">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-796">The preferred method is to use WebPI.</span></span> <span data-ttu-id="7ef5b-797">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-797">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7ef5b-798">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-798">Create the IIS site</span></span>

1. <span data-ttu-id="7ef5b-799">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-799">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7ef5b-800">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-800">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="7ef5b-801">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-801">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="7ef5b-802">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-802">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7ef5b-803">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-803">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7ef5b-804">상황에 맞는 메뉴에서 **웹 사이트 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-804">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7ef5b-805">**사이트 이름** 을 제공하고 **실제 경로** 를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-805">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="7ef5b-806">**바인딩** 구성을 제공하고 **확인** 을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-806">Provide the **Binding** configuration and create the website by selecting **OK** :</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="7ef5b-808">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-808">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7ef5b-809">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-809">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="7ef5b-810">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-810">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7ef5b-811">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-811">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="7ef5b-812">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-812">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7ef5b-813">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-813">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="7ef5b-814">서버 노드에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-814">Under the server's node, select **Application Pools** .</span></span>

1. <span data-ttu-id="7ef5b-815">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-815">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="7ef5b-816">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-816">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code** :</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="7ef5b-818">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-818">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="7ef5b-819">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-819">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="7ef5b-820">**.NET CLR 버전** 을 **관리 코드 없음** 으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-820">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="7ef5b-821">*ASP.NET Core 2.2 이상* : [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-821">*ASP.NET Core 2.2 or later* : For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="7ef5b-822">IIS 관리자의 **애플리케이션 풀** 에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-822">In the **Actions** sidebar of IIS Manager > **Application Pools** , select **Set Application Pool Defaults** or **Advanced Settings** .</span></span> <span data-ttu-id="7ef5b-823">**32비트 애플리케이션 사용** 을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-823">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="7ef5b-824">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-824">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="7ef5b-825">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-825">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="7ef5b-826">애플리케이션 풀의 기본 ID( **프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-826">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="7ef5b-827">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-827">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="7ef5b-828">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-828">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="7ef5b-829">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-829">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="7ef5b-830">앱 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-830">Deploy the app</span></span>

<span data-ttu-id="7ef5b-831">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-831">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="7ef5b-832">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-832">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="7ef5b-833">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-833">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="7ef5b-834">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-834">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="7ef5b-835">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-835">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="7ef5b-837">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-837">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="7ef5b-838">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-838">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="7ef5b-839">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-839">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="7ef5b-840">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="7ef5b-840">Alternatives to Web Deploy</span></span>

<span data-ttu-id="7ef5b-841">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-841">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="7ef5b-842">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-842">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="7ef5b-843">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-843">Browse the website</span></span>

<span data-ttu-id="7ef5b-844">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-844">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="7ef5b-845">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름** 에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-845">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="7ef5b-846">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-846">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="7ef5b-848">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="7ef5b-848">Locked deployment files</span></span>

<span data-ttu-id="7ef5b-849">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-849">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="7ef5b-850">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-850">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="7ef5b-851">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나** 를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-851">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-852">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-852">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="7ef5b-853">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-853">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="7ef5b-854">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-854">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="7ef5b-855">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-855">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="7ef5b-856">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-856">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="7ef5b-857">PowerShell을 사용하여 *app_offline.htm* 을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-857">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="7ef5b-858">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="7ef5b-858">Data protection</span></span>

<span data-ttu-id="7ef5b-859">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-859">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="7ef5b-860">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-860">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="7ef5b-861">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-861">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="7ef5b-862">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-862">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="7ef5b-863">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-863">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="7ef5b-864">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-864">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="7ef5b-865">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-865">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="7ef5b-866">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-866">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="7ef5b-867">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-867">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-868">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-868">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="7ef5b-869">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-869">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="7ef5b-870">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-870">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="7ef5b-871">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-871">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="7ef5b-872">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-872">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="7ef5b-873">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-873">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="7ef5b-874">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-874">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="7ef5b-875">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-875">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="7ef5b-876">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-876">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="7ef5b-877">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-877">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="7ef5b-878">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-878">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="7ef5b-879">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-879">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="7ef5b-880">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-880">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="7ef5b-881">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-881">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="7ef5b-882">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-882">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="7ef5b-883">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-883">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="7ef5b-884">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-884">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="7ef5b-885">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-885">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="7ef5b-886">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-886">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="7ef5b-887">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-887">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="7ef5b-888">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-888">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="7ef5b-889">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-889">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="7ef5b-890">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-890">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="7ef5b-891">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-891">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="7ef5b-892">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-892">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="7ef5b-893">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-893">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="7ef5b-894">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-894">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="7ef5b-895">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-895">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="7ef5b-896">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-896">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="7ef5b-897">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-897">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="7ef5b-898">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-898">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="7ef5b-899">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-899">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="7ef5b-900">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-900">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="7ef5b-901">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-901">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="7ef5b-902">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-902">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="7ef5b-903">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-903">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="7ef5b-904">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-904">Virtual Directories</span></span>

<span data-ttu-id="7ef5b-905">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-905">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="7ef5b-906">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-906">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="7ef5b-907">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-907">Sub-applications</span></span>

<span data-ttu-id="7ef5b-908">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-908">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="7ef5b-909">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-909">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="7ef5b-910">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-910">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="7ef5b-911">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-911">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="7ef5b-912">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-912">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="7ef5b-913">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-913">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="7ef5b-914">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-914">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="7ef5b-915">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-915">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="7ef5b-916">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-916">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="7ef5b-917">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-917">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="7ef5b-918">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-918">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="7ef5b-919">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-919">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="7ef5b-920">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-920">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="7ef5b-921">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application** (애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-921">Right-click the sub-app folder in IIS Manager and select **Convert to Application** .</span></span>

1. <span data-ttu-id="7ef5b-922">**Add Application** (애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-922">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="7ef5b-923">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-923">Select **OK** .</span></span>

<span data-ttu-id="7ef5b-924">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-924">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="7ef5b-925">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-925">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="7ef5b-926">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-926">Configuration of IIS with web.config</span></span>

<span data-ttu-id="7ef5b-927">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config* 에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-927">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="7ef5b-928">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-928">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="7ef5b-929">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-929">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="7ef5b-930">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-930">For more information, see the following topics:</span></span>

* [<span data-ttu-id="7ef5b-931">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="7ef5b-931">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="7ef5b-932">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-932">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="7ef5b-933">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-933">Configuration sections of web.config</span></span>

<span data-ttu-id="7ef5b-934">*web.config* 에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-934">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="7ef5b-935">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-935">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="7ef5b-936">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-936">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="7ef5b-937">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="7ef5b-937">Application Pools</span></span>

<span data-ttu-id="7ef5b-938">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-938">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="7ef5b-939">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-939">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="7ef5b-940">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-940">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="7ef5b-941">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-941">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="7ef5b-942">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-942">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="7ef5b-943">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-943">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="7ef5b-944">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="7ef5b-944">Application Pool Identity</span></span>

<span data-ttu-id="7ef5b-945">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-945">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="7ef5b-946">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-946">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="7ef5b-947">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-947">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** :</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="7ef5b-949">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-949">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="7ef5b-950">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-950">Resources can be secured using this identity.</span></span> <span data-ttu-id="7ef5b-951">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-951">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="7ef5b-952">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-952">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="7ef5b-953">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-953">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="7ef5b-954">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-954">Right-click on the directory and select **Properties** .</span></span>

1. <span data-ttu-id="7ef5b-955">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-955">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="7ef5b-956">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-956">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="7ef5b-957">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-957">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="7ef5b-958">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-958">Select the **Check Names** button.</span></span> <span data-ttu-id="7ef5b-959">*DefaultAppPool* 의 경우 **IIS AppPool\DefaultAppPool** 을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-959">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool** .</span></span> <span data-ttu-id="7ef5b-960">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-960">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="7ef5b-961">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-961">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="7ef5b-962">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-962">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="7ef5b-964">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-964">Select **OK** .</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="7ef5b-966">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-966">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="7ef5b-967">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-967">Provide additional permissions as needed.</span></span>

<span data-ttu-id="7ef5b-968">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-968">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="7ef5b-969">*DefaultAppPool* 을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-969">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="7ef5b-970">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-970">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7ef5b-971">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7ef5b-971">HTTP/2 support</span></span>

<span data-ttu-id="7ef5b-972">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-972">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="7ef5b-973">In-Process</span><span class="sxs-lookup"><span data-stu-id="7ef5b-973">In-process</span></span>
  * <span data-ttu-id="7ef5b-974">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-974">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7ef5b-975">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7ef5b-975">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="7ef5b-976">Out of Process</span><span class="sxs-lookup"><span data-stu-id="7ef5b-976">Out-of-process</span></span>
  * <span data-ttu-id="7ef5b-977">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-977">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7ef5b-978">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-978">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7ef5b-979">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7ef5b-979">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7ef5b-980">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-980">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="7ef5b-981">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-981">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7ef5b-982">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-982">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7ef5b-983">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-983">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7ef5b-984">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-984">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="7ef5b-985">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-985">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="7ef5b-986">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="7ef5b-986">CORS preflight requests</span></span>

<span data-ttu-id="7ef5b-987">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-987">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="7ef5b-988">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-988">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="7ef5b-989">OPTIONS 요청을 전달하도록 *web.config* 에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-989">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="7ef5b-990">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="7ef5b-990">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="7ef5b-991">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="7ef5b-991">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="7ef5b-992">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-992">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="7ef5b-993">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-993">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="7ef5b-994">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="7ef5b-994">Application Initialization Module</span></span>

<span data-ttu-id="7ef5b-995">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="7ef5b-995">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="7ef5b-996">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-996">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="7ef5b-997">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-997">The request triggers the app to start.</span></span> <span data-ttu-id="7ef5b-998">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-998">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="7ef5b-999">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-999">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="7ef5b-1000">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1000">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="7ef5b-1001">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1001">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7ef5b-1002">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1002">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features** .</span></span>
1. <span data-ttu-id="7ef5b-1003">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1003">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7ef5b-1004">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1004">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="7ef5b-1005">**역할 및 기능 추가 마법사** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1005">Open the **Add Roles and Features Wizard** .</span></span>
1. <span data-ttu-id="7ef5b-1006">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1006">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="7ef5b-1007">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1007">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7ef5b-1008">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1008">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="7ef5b-1009">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1009">Using IIS Manager:</span></span>

  1. <span data-ttu-id="7ef5b-1010">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1010">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7ef5b-1011">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1011">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7ef5b-1012">기본 **시작 모드** 는 **OnDemand** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1012">The default **Start Mode** is **OnDemand** .</span></span> <span data-ttu-id="7ef5b-1013">**시작 모드** 를 **AlwaysRunning** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1013">Set the **Start Mode** to **AlwaysRunning** .</span></span> <span data-ttu-id="7ef5b-1014">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1014">Select **OK** .</span></span>
  1. <span data-ttu-id="7ef5b-1015">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1015">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7ef5b-1016">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1016">Right-click the app and select **Manage Website** > **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7ef5b-1017">기본 **미리 로드 사용** 설정은 **False** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1017">The default **Preload Enabled** setting is **False** .</span></span> <span data-ttu-id="7ef5b-1018">**미리 로드 사용** 을 **True** 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1018">Set **Preload Enabled** to **True** .</span></span> <span data-ttu-id="7ef5b-1019">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1019">Select **OK** .</span></span>

* <span data-ttu-id="7ef5b-1020">*web.config* 를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1020">Using *web.config* , add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="7ef5b-1021">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1021">Idle Timeout</span></span>

<span data-ttu-id="7ef5b-1022">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1022">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="7ef5b-1023">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1023">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="7ef5b-1024">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1024">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="7ef5b-1025">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1025">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
1. <span data-ttu-id="7ef5b-1026">기본 **유휴 시간 제한(분)** 은 **20** 분입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1026">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="7ef5b-1027">**유휴 시간 제한(분)** 을 **0** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1027">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="7ef5b-1028">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1028">Select **OK** .</span></span>
1. <span data-ttu-id="7ef5b-1029">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1029">Recycle the worker process.</span></span>

<span data-ttu-id="7ef5b-1030">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1030">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-1031">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1031">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="7ef5b-1032">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1032">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="7ef5b-1033">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1033">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="7ef5b-1034">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1034">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="7ef5b-1035">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1035">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="7ef5b-1036">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1036">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7ef5b-1037">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1037">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7ef5b-1038">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1038">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7ef5b-1039">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1039">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7ef5b-1040">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1040">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7ef5b-1041">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1041">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7ef5b-1042">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1042">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7ef5b-1043">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1043">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7ef5b-1044">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1044">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7ef5b-1045">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1045">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="7ef5b-1046">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1046">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="7ef5b-1047">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1047">Supported operating systems</span></span>

<span data-ttu-id="7ef5b-1048">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1048">The following operating systems are supported:</span></span>

* <span data-ttu-id="7ef5b-1049">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1049">Windows 7 or later</span></span>
* <span data-ttu-id="7ef5b-1050">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1050">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7ef5b-1051">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1051">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="7ef5b-1052">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1052">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7ef5b-1053">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1053">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="7ef5b-1054">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1054">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7ef5b-1055">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1055">Supported platforms</span></span>

<span data-ttu-id="7ef5b-1056">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1056">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7ef5b-1057">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1057">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7ef5b-1058">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1058">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7ef5b-1059">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1059">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7ef5b-1060">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1060">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="7ef5b-1061">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1061">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="7ef5b-1062">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1062">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="7ef5b-1063">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1063">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="7ef5b-1064">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](xref:fundamentals/servers/index#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서( *Out-of-Process* ) 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1064">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process ( *out-of-process* ) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="7ef5b-1065">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1065">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="7ef5b-1066">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1066">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7ef5b-1067">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1067">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7ef5b-1068">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1068">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="7ef5b-1070">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1070">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7ef5b-1071">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1071">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7ef5b-1072">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1072">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7ef5b-1073">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1073">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="7ef5b-1074">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1074">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7ef5b-1075">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1075">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7ef5b-1076">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1076">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7ef5b-1077">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1077">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7ef5b-1078">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1078">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7ef5b-1079">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1079">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7ef5b-1080">`CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1080">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="7ef5b-1081">ASP.NET Core 모듈은 동적 포트를 생성하여 백 엔드 프로세스에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1081">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="7ef5b-1082">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1082">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> method.</span></span> <span data-ttu-id="7ef5b-1083">`UseIISIntegration`은 localhost IP 주소(`127.0.0.1`)의 동적 포트에서 수신 대기하도록 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1083">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="7ef5b-1084">동적 포트가 1234인 경우 Kestrel은 `127.0.0.1:1234`에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1084">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="7ef5b-1085">이 구성은 다음에서 제공된 다른 URL 구성을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1085">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="7ef5b-1086">Kestrel의 수신 대기 API</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1086">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="7ef5b-1087">[구성](xref:fundamentals/configuration/index)(또는 [명령줄 --urls 옵션](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1087">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="7ef5b-1088">모듈을 사용하는 경우 `UseUrls` 호출 또는 Kestrel의 `Listen` API가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1088">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="7ef5b-1089">`UseUrls` 또는 `Listen`을 호출하는 경우 Kestrel은 IIS 없이 앱을 실행할 때만 지정된 포트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1089">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="7ef5b-1090">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1090">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7ef5b-1091">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1091">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7ef5b-1092">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1092">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7ef5b-1093">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1093">Enable the IISIntegration components</span></span>

<span data-ttu-id="7ef5b-1094">`CreateWebHostBuilder`( *Program.cs* )에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1094">When building a host in `CreateWebHostBuilder` ( *Program.cs* ), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7ef5b-1095">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1095">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="7ef5b-1096">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1096">IIS options</span></span>

| <span data-ttu-id="7ef5b-1097">옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1097">Option</span></span>                         | <span data-ttu-id="7ef5b-1098">기본값</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1098">Default</span></span> | <span data-ttu-id="7ef5b-1099">설정</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1099">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7ef5b-1100">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1100">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7ef5b-1101">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1101">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7ef5b-1102">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1102">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7ef5b-1103">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1103">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7ef5b-1104">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1104">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="7ef5b-1105">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1105">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="7ef5b-1106">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1106">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7ef5b-1107">옵션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1107">Option</span></span>                         | <span data-ttu-id="7ef5b-1108">기본값</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1108">Default</span></span> | <span data-ttu-id="7ef5b-1109">설정</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1109">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7ef5b-1110">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1110">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7ef5b-1111">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1111">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7ef5b-1112">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1112">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7ef5b-1113">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1113">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7ef5b-1114">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1114">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7ef5b-1115">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1115">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7ef5b-1116">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1116">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7ef5b-1117">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1117">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="7ef5b-1118">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1118">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7ef5b-1119">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1119">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="7ef5b-1120">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1120">web.config file</span></span>

<span data-ttu-id="7ef5b-1121">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1121">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ef5b-1122">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1122">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="7ef5b-1123">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1123">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="7ef5b-1124">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1124">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7ef5b-1125">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1125">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="7ef5b-1126">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수* 로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1126">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="7ef5b-1127">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1127">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="7ef5b-1128">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1128">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="7ef5b-1129">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1129">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="7ef5b-1130">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1130">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="7ef5b-1131">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수* 를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1131">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="7ef5b-1132">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1132">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="7ef5b-1133">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1133">web.config file location</span></span>

<span data-ttu-id="7ef5b-1134">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1134">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="7ef5b-1135">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1135">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="7ef5b-1136">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1136">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="7ef5b-1137">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json* , *\<assembly>assembly>.xml* (XML 문서 주석) 및 *\<assembly>assembly>.deps.json* 과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1137">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json* , *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json* .</span></span> <span data-ttu-id="7ef5b-1138">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1138">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="7ef5b-1139">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1139">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="7ef5b-1140">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1140">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="7ef5b-1141">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1141">Transform web.config</span></span>

<span data-ttu-id="7ef5b-1142">게시할 때 *web.config* 를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1142">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="7ef5b-1143">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1143">IIS configuration</span></span>

<span data-ttu-id="7ef5b-1144">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1144">**Windows Server operating systems**</span></span>

<span data-ttu-id="7ef5b-1145">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1145">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="7ef5b-1146">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1146">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span> <span data-ttu-id="7ef5b-1147">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1147">On the **Server Roles** step, check the box for **Web Server (IIS)** .</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="7ef5b-1149">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1149">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="7ef5b-1150">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1150">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="7ef5b-1152">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1152">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-1153">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1153">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security** .</span></span> <span data-ttu-id="7ef5b-1154">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1154">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7ef5b-1155">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1155">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7ef5b-1156">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1156">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-1157">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1157">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ef5b-1158">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1158">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development** .</span></span> <span data-ttu-id="7ef5b-1159">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1159">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ef5b-1160">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1160">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7ef5b-1161">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1161">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="7ef5b-1162">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1162">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="7ef5b-1163">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1163">**Windows desktop operating systems**</span></span>

<span data-ttu-id="7ef5b-1164">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1164">Enable the **IIS Management Console** and **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7ef5b-1165">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함** (화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1165">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="7ef5b-1166">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1166">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="7ef5b-1167">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1167">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="7ef5b-1168">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1168">Check the box for **IIS Management Console** .</span></span>

1. <span data-ttu-id="7ef5b-1169">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1169">Check the box for **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7ef5b-1170">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1170">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="7ef5b-1171">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1171">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-1172">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1172">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security** .</span></span> <span data-ttu-id="7ef5b-1173">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1173">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7ef5b-1174">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1174">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7ef5b-1175">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1175">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7ef5b-1176">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1176">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ef5b-1177">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1177">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features** .</span></span> <span data-ttu-id="7ef5b-1178">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1178">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7ef5b-1179">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1179">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7ef5b-1180">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1180">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7ef5b-1182">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1182">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7ef5b-1183">호스팅 시스템에 *.NET Core 호스팅 번들* 을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1183">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="7ef5b-1184">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1184">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ef5b-1185">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1185">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7ef5b-1186">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1186">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7ef5b-1187">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1187">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7ef5b-1188">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1188">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7ef5b-1189">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1189">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="7ef5b-1190">다운로드</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1190">Download</span></span>

1. <span data-ttu-id="7ef5b-1191">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1191">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7ef5b-1192">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1192">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7ef5b-1193">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1193">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7ef5b-1194">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1194">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7ef5b-1195">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1195">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7ef5b-1196">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1196">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="7ef5b-1197">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1197">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="7ef5b-1198">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1198">Run the installer on the server.</span></span> <span data-ttu-id="7ef5b-1199">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1199">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7ef5b-1200">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1200">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7ef5b-1201">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1201">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7ef5b-1202">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1202">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ef5b-1203">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1203">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7ef5b-1204">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1204">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ef5b-1205">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1205">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7ef5b-1206">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1206">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7ef5b-1207">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1207">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7ef5b-1208">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성( *applicationHost.config* )이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1208">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration ( *applicationHost.config* ) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7ef5b-1209">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1209">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7ef5b-1210">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1210">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="7ef5b-1211">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1211">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="7ef5b-1212">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1212">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="7ef5b-1213">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1213">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="7ef5b-1214">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1214">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="7ef5b-1215">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1215">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="7ef5b-1216">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1216">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="7ef5b-1217">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1217">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="7ef5b-1218">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1218">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="7ef5b-1219">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1219">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="7ef5b-1220">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1220">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="7ef5b-1221">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1221">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="7ef5b-1222">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1222">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="7ef5b-1223">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1223">The preferred method is to use WebPI.</span></span> <span data-ttu-id="7ef5b-1224">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1224">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7ef5b-1225">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1225">Create the IIS site</span></span>

1. <span data-ttu-id="7ef5b-1226">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1226">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7ef5b-1227">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1227">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="7ef5b-1228">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1228">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="7ef5b-1229">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1229">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7ef5b-1230">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1230">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7ef5b-1231">상황에 맞는 메뉴에서 **웹 사이트 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1231">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7ef5b-1232">**사이트 이름** 을 제공하고 **실제 경로** 를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1232">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="7ef5b-1233">**바인딩** 구성을 제공하고 **확인** 을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1233">Provide the **Binding** configuration and create the website by selecting **OK** :</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="7ef5b-1235">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1235">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7ef5b-1236">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1236">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="7ef5b-1237">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1237">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7ef5b-1238">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1238">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="7ef5b-1239">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1239">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7ef5b-1240">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1240">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="7ef5b-1241">서버 노드에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1241">Under the server's node, select **Application Pools** .</span></span>

1. <span data-ttu-id="7ef5b-1242">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1242">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="7ef5b-1243">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1243">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code** :</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="7ef5b-1245">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1245">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="7ef5b-1246">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1246">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="7ef5b-1247">**.NET CLR 버전** 을 **관리 코드 없음** 으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1247">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="7ef5b-1248">*ASP.NET Core 2.2 이상* : [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1248">*ASP.NET Core 2.2 or later* : For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="7ef5b-1249">IIS 관리자의 **애플리케이션 풀** 에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1249">In the **Actions** sidebar of IIS Manager > **Application Pools** , select **Set Application Pool Defaults** or **Advanced Settings** .</span></span> <span data-ttu-id="7ef5b-1250">**32비트 애플리케이션 사용** 을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1250">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="7ef5b-1251">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1251">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="7ef5b-1252">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1252">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="7ef5b-1253">애플리케이션 풀의 기본 ID( **프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1253">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="7ef5b-1254">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1254">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="7ef5b-1255">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1255">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="7ef5b-1256">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1256">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="7ef5b-1257">앱 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1257">Deploy the app</span></span>

<span data-ttu-id="7ef5b-1258">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1258">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="7ef5b-1259">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1259">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="7ef5b-1260">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1260">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="7ef5b-1261">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1261">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="7ef5b-1262">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1262">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="7ef5b-1264">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1264">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="7ef5b-1265">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1265">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="7ef5b-1266">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1266">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="7ef5b-1267">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1267">Alternatives to Web Deploy</span></span>

<span data-ttu-id="7ef5b-1268">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1268">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="7ef5b-1269">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1269">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="7ef5b-1270">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1270">Browse the website</span></span>

<span data-ttu-id="7ef5b-1271">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1271">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="7ef5b-1272">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름** 에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1272">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="7ef5b-1273">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1273">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="7ef5b-1275">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1275">Locked deployment files</span></span>

<span data-ttu-id="7ef5b-1276">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1276">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="7ef5b-1277">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1277">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="7ef5b-1278">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나** 를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1278">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-1279">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1279">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="7ef5b-1280">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1280">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="7ef5b-1281">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1281">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="7ef5b-1282">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1282">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="7ef5b-1283">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1283">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="7ef5b-1284">PowerShell을 사용하여 *app_offline.htm* 을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1284">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="7ef5b-1285">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1285">Data protection</span></span>

<span data-ttu-id="7ef5b-1286">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1286">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="7ef5b-1287">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1287">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="7ef5b-1288">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1288">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="7ef5b-1289">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1289">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="7ef5b-1290">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1290">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="7ef5b-1291">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1291">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="7ef5b-1292">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1292">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="7ef5b-1293">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1293">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="7ef5b-1294">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1294">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="7ef5b-1295">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1295">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="7ef5b-1296">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1296">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="7ef5b-1297">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1297">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="7ef5b-1298">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1298">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="7ef5b-1299">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1299">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="7ef5b-1300">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1300">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="7ef5b-1301">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1301">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="7ef5b-1302">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1302">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="7ef5b-1303">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1303">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="7ef5b-1304">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1304">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="7ef5b-1305">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1305">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="7ef5b-1306">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1306">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="7ef5b-1307">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1307">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="7ef5b-1308">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1308">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="7ef5b-1309">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1309">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="7ef5b-1310">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1310">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="7ef5b-1311">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1311">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="7ef5b-1312">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1312">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="7ef5b-1313">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1313">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="7ef5b-1314">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1314">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="7ef5b-1315">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1315">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="7ef5b-1316">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1316">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="7ef5b-1317">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1317">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="7ef5b-1318">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1318">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="7ef5b-1319">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1319">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="7ef5b-1320">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1320">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="7ef5b-1321">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1321">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="7ef5b-1322">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1322">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="7ef5b-1323">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1323">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="7ef5b-1324">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1324">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="7ef5b-1325">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1325">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="7ef5b-1326">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1326">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="7ef5b-1327">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1327">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="7ef5b-1328">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1328">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="7ef5b-1329">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1329">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="7ef5b-1330">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1330">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="7ef5b-1331">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1331">Virtual Directories</span></span>

<span data-ttu-id="7ef5b-1332">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1332">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="7ef5b-1333">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1333">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="7ef5b-1334">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1334">Sub-applications</span></span>

<span data-ttu-id="7ef5b-1335">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1335">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="7ef5b-1336">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1336">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="7ef5b-1337">하위 앱에는 ASP.NET Core 모듈이 처리기로 포함되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1337">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="7ef5b-1338">하위 앱의 *web.config* 파일에 모듈이 처리기로 추가되면, 하위 앱을 찾으려고 할 때 잘못된 구성 파일을 참조하는 *500.19 내부 서버 오류* 가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1338">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="7ef5b-1339">다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1339">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="7ef5b-1340">ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하는 경우, 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1340">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="7ef5b-1341">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1341">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="7ef5b-1342">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1342">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="7ef5b-1343">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1343">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="7ef5b-1344">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1344">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="7ef5b-1345">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1345">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="7ef5b-1346">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1346">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="7ef5b-1347">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1347">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="7ef5b-1348">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1348">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="7ef5b-1349">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1349">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="7ef5b-1350">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1350">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="7ef5b-1351">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1351">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="7ef5b-1352">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application** (애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1352">Right-click the sub-app folder in IIS Manager and select **Convert to Application** .</span></span>

1. <span data-ttu-id="7ef5b-1353">**Add Application** (애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1353">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="7ef5b-1354">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1354">Select **OK** .</span></span>

<span data-ttu-id="7ef5b-1355">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1355">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="7ef5b-1356">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1356">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="7ef5b-1357">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1357">Configuration of IIS with web.config</span></span>

<span data-ttu-id="7ef5b-1358">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config* 에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1358">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="7ef5b-1359">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1359">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="7ef5b-1360">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1360">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="7ef5b-1361">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1361">For more information, see the following topics:</span></span>

* [<span data-ttu-id="7ef5b-1362">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1362">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="7ef5b-1363">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1363">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="7ef5b-1364">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1364">Configuration sections of web.config</span></span>

<span data-ttu-id="7ef5b-1365">*web.config* 에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1365">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="7ef5b-1366">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1366">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="7ef5b-1367">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1367">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="7ef5b-1368">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1368">Application Pools</span></span>

<span data-ttu-id="7ef5b-1369">서버에서 여러 웹 사이트를 호스트하는 경우 각 앱을 해당 앱 풀에서 실행하여 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1369">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="7ef5b-1370">이 구성은 IIS **웹 사이트 추가** 대화 상자의 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1370">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="7ef5b-1371">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1371">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="7ef5b-1372">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1372">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="7ef5b-1373">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1373">Application Pool Identity</span></span>

<span data-ttu-id="7ef5b-1374">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1374">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="7ef5b-1375">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1375">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="7ef5b-1376">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1376">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** :</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="7ef5b-1378">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1378">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="7ef5b-1379">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1379">Resources can be secured using this identity.</span></span> <span data-ttu-id="7ef5b-1380">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1380">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="7ef5b-1381">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1381">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="7ef5b-1382">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1382">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="7ef5b-1383">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1383">Right-click on the directory and select **Properties** .</span></span>

1. <span data-ttu-id="7ef5b-1384">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1384">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="7ef5b-1385">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1385">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="7ef5b-1386">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1386">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="7ef5b-1387">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1387">Select the **Check Names** button.</span></span> <span data-ttu-id="7ef5b-1388">*DefaultAppPool* 의 경우 **IIS AppPool\DefaultAppPool** 을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1388">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool** .</span></span> <span data-ttu-id="7ef5b-1389">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1389">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="7ef5b-1390">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1390">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="7ef5b-1391">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1391">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="7ef5b-1393">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1393">Select **OK** .</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="7ef5b-1395">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1395">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="7ef5b-1396">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1396">Provide additional permissions as needed.</span></span>

<span data-ttu-id="7ef5b-1397">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1397">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="7ef5b-1398">*DefaultAppPool* 을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1398">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="7ef5b-1399">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1399">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7ef5b-1400">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1400">HTTP/2 support</span></span>

<span data-ttu-id="7ef5b-1401">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음 기본 요구 사항을 충족하는 Out of Process 배포에 대해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1401">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="7ef5b-1402">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1402">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="7ef5b-1403">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1403">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="7ef5b-1404">대상 프레임워크: HTTP/2 연결은 IIS에 의해 완전히 처리되므로 Out of Process 배포에는 해당하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1404">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="7ef5b-1405">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1405">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7ef5b-1406">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1406">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7ef5b-1407">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1407">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7ef5b-1408">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1408">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="7ef5b-1409">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1409">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="7ef5b-1410">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1410">CORS preflight requests</span></span>

<span data-ttu-id="7ef5b-1411">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1411">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="7ef5b-1412">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1412">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="7ef5b-1413">OPTIONS 요청을 전달하도록 *web.config* 에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1413">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7ef5b-1414">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1414">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7ef5b-1415">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1415">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7ef5b-1416">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1416">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7ef5b-1417">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1417">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7ef5b-1418">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1418">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7ef5b-1419">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1419">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7ef5b-1420">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1420">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7ef5b-1421">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7ef5b-1421">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
