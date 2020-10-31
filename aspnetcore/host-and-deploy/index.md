---
title: ASP.NET Core 호스트 및 배포
author: rick-anderson
description: 호스팅 환경을 설정하고 ASP.NET Core 앱을 배포하는 방법을 알아봅니다.
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
uid: host-and-deploy/index
ms.openlocfilehash: 19e888859cea35624491a516404c57e30aa9db05
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057221"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="f9226-103">ASP.NET Core 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="f9226-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f9226-104">일반적으로 ASP.NET Core 앱을 호스팅 환경에 배포하기 위해서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="f9226-105">게시한 앱을 호스팅 서버의 폴더에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="f9226-106">요청이 도착할 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="f9226-107">역항뱡 프록시 구성이 필요한 경우, 요청을 앱으로 전달하는 역방향 프록시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="f9226-108">폴더에 게시</span><span class="sxs-lookup"><span data-stu-id="f9226-108">Publish to a folder</span></span>

<span data-ttu-id="f9226-109">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 앱 코드를 컴파일하고 앱을 실행하는 데 필요한 파일을 *publish* 폴더로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="f9226-110">Visual Studio에서 배포하는 경우에는 파일이 배포 대상으로 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="f9226-111">폴더 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="f9226-111">Folder contents</span></span>

<span data-ttu-id="f9226-112">*publish* 폴더에는 하나 이상의 앱 어셈블리 파일, 종속성, 그리고 선택적으로 .NET 런타임이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="f9226-113">.NET Core 앱은 *자체 포함된 배포* 또는 *프레임워크 종속 배포* 로 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment* .</span></span> <span data-ttu-id="f9226-114">앱이 자체 포함된 배포인 경우, .NET 런타임이 포함된 어셈블리 파일은 *publish* 폴더에 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="f9226-115">앱이 프레임워크 종속인 경우 서버에 설치된 .NET 버전에 대한 참조가 앱에 포함되므로 .NET 런타임 파일이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="f9226-116">기본 배포 모델은 프레임워크 종속입니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="f9226-117">자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="f9226-118">*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="f9226-119">자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="f9226-120">프로세스 관리자 설정</span><span class="sxs-lookup"><span data-stu-id="f9226-120">Set up a process manager</span></span>

<span data-ttu-id="f9226-121">ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="f9226-122">시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="f9226-123">ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="f9226-124">Linux</span><span class="sxs-lookup"><span data-stu-id="f9226-124">Linux</span></span>
  * [<span data-ttu-id="f9226-125">Nginx</span><span class="sxs-lookup"><span data-stu-id="f9226-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f9226-126">Apache</span><span class="sxs-lookup"><span data-stu-id="f9226-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="f9226-127">Windows</span><span class="sxs-lookup"><span data-stu-id="f9226-127">Windows</span></span>
  * [<span data-ttu-id="f9226-128">IIS</span><span class="sxs-lookup"><span data-stu-id="f9226-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f9226-129">Windows 서비스</span><span class="sxs-lookup"><span data-stu-id="f9226-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="f9226-130">역방향 프록시 설정</span><span class="sxs-lookup"><span data-stu-id="f9226-130">Set up a reverse proxy</span></span>

<span data-ttu-id="f9226-131">앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 사용하는 경우 [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) 또는 [IIS](xref:host-and-deploy/iis/index)를 역방향 프록시 서버로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="f9226-132">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="f9226-133">&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="f9226-134">자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f9226-135">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="f9226-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f9226-136">프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="f9226-137">추가 구성이 없는 앱에는 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소에 대한 액세스 권한이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="f9226-138">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="f9226-139">Visual Studio 및 MSBuild를 사용하여 배포 자동화</span><span class="sxs-lookup"><span data-stu-id="f9226-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="f9226-140">일반적으로 배포에는 [dotnet publish](/dotnet/core/tools/dotnet-publish)에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="f9226-141">예를 들어 추가 파일이 필요하거나 *publish* 폴더에서 제외될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="f9226-142">Visual Studio에서는 웹 배포에 [MSBuild](/visualstudio/msbuild/msbuild)를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-142">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="f9226-143">자세한 내용은 <xref:host-and-deploy/visual-studio-publish-profiles> 및 [Using MSBuild and Team Foundation Build](http://msbuildbook.com/)(MSBuild 및 Team Foundation Build 사용) 책을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="f9226-144">[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:host-and-deploy/azure-apps/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 앱을 직접 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="f9226-145">Azure DevOps Services는 [Azure App Service에 지속적인 배포](/azure/devops/pipelines/targets/webapp)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="f9226-146">자세한 내용은 [ASP.NET Core 및 Azure에서 DevOps](xref:azure/devops/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="f9226-147">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="f9226-147">Publish to Azure</span></span>

<span data-ttu-id="f9226-148">Visual Studio를 사용하여 Azure에 앱을 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="f9226-149">추가 예제는 [Azure에서 ASP.NET Core 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="f9226-150">Windows에서 MSDeploy를 사용하여 게시</span><span class="sxs-lookup"><span data-stu-id="f9226-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="f9226-151">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령을 사용하여 Windows 명령 프롬프트에서 수행하는 방법을 비롯하여 Visual Studio 게시 프로필을 사용하여 앱을 게시하는 방법에 대한 지침은 <xref:host-and-deploy/visual-studio-publish-profiles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="f9226-152">IIS(인터넷 정보 서비스)</span><span class="sxs-lookup"><span data-stu-id="f9226-152">Internet Information Services (IIS)</span></span>

<span data-ttu-id="f9226-153">*web.config* 파일에 제공된 구성을 사용하여 IIS(인터넷 정보 서비스)에 배포하는 경우 <xref:host-and-deploy/iis/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-153">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="f9226-154">웹 팜에서 호스트</span><span class="sxs-lookup"><span data-stu-id="f9226-154">Host in a web farm</span></span>

<span data-ttu-id="f9226-155">웹 팜 환경에서 ASP.NET Core 앱을 호스팅하는 구성에 대한 자세한 내용(예: 확장성을 위해 앱의 여러 인스턴스 배포)은 <xref:host-and-deploy/web-farm>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="f9226-156">Docker에서 호스트</span><span class="sxs-lookup"><span data-stu-id="f9226-156">Host on Docker</span></span>

<span data-ttu-id="f9226-157">자세한 내용은 <xref:host-and-deploy/docker/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-157">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="f9226-158">상태 검사 수행</span><span class="sxs-lookup"><span data-stu-id="f9226-158">Perform health checks</span></span>

<span data-ttu-id="f9226-159">상태 검사 미들웨어를 사용하여 앱 및 그 종속성에 대해 상태 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-159">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="f9226-160">자세한 내용은 <xref:host-and-deploy/health-checks>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-160">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9226-161">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9226-161">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="f9226-162">ASP.NET 호스팅</span><span class="sxs-lookup"><span data-stu-id="f9226-162">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f9226-163">일반적으로 ASP.NET Core 앱을 호스팅 환경에 배포하기 위해서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-163">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="f9226-164">게시한 앱을 호스팅 서버의 폴더에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-164">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="f9226-165">요청이 도착할 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-165">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="f9226-166">역항뱡 프록시 구성이 필요한 경우, 요청을 앱으로 전달하는 역방향 프록시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-166">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="f9226-167">폴더에 게시</span><span class="sxs-lookup"><span data-stu-id="f9226-167">Publish to a folder</span></span>

<span data-ttu-id="f9226-168">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 앱 코드를 컴파일하고 앱을 실행하는 데 필요한 파일을 *publish* 폴더로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-168">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="f9226-169">Visual Studio에서 배포하는 경우에는 파일이 배포 대상으로 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-169">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="f9226-170">폴더 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="f9226-170">Folder contents</span></span>

<span data-ttu-id="f9226-171">*publish* 폴더에는 하나 이상의 앱 어셈블리 파일, 종속성, 그리고 선택적으로 .NET 런타임이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-171">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="f9226-172">.NET Core 앱은 *자체 포함된 배포* 또는 *프레임워크 종속 배포* 로 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-172">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment* .</span></span> <span data-ttu-id="f9226-173">앱이 자체 포함된 배포인 경우, .NET 런타임이 포함된 어셈블리 파일은 *publish* 폴더에 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-173">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="f9226-174">앱이 프레임워크 종속인 경우 서버에 설치된 .NET 버전에 대한 참조가 앱에 포함되므로 .NET 런타임 파일이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-174">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="f9226-175">기본 배포 모델은 프레임워크 종속입니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-175">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="f9226-176">자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-176">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="f9226-177">*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-177">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="f9226-178">자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-178">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="f9226-179">프로세스 관리자 설정</span><span class="sxs-lookup"><span data-stu-id="f9226-179">Set up a process manager</span></span>

<span data-ttu-id="f9226-180">ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-180">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="f9226-181">시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-181">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="f9226-182">ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-182">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="f9226-183">Linux</span><span class="sxs-lookup"><span data-stu-id="f9226-183">Linux</span></span>
  * [<span data-ttu-id="f9226-184">Nginx</span><span class="sxs-lookup"><span data-stu-id="f9226-184">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f9226-185">Apache</span><span class="sxs-lookup"><span data-stu-id="f9226-185">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="f9226-186">Windows</span><span class="sxs-lookup"><span data-stu-id="f9226-186">Windows</span></span>
  * [<span data-ttu-id="f9226-187">IIS</span><span class="sxs-lookup"><span data-stu-id="f9226-187">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f9226-188">Windows 서비스</span><span class="sxs-lookup"><span data-stu-id="f9226-188">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="f9226-189">역방향 프록시 설정</span><span class="sxs-lookup"><span data-stu-id="f9226-189">Set up a reverse proxy</span></span>

<span data-ttu-id="f9226-190">앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 사용하는 경우 [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) 또는 [IIS](xref:host-and-deploy/iis/index)를 역방향 프록시 서버로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-190">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="f9226-191">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-191">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="f9226-192">&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-192">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="f9226-193">자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-193">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f9226-194">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="f9226-194">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f9226-195">프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-195">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="f9226-196">추가 구성이 없는 앱에는 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소에 대한 액세스 권한이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-196">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="f9226-197">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-197">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="f9226-198">Visual Studio 및 MSBuild를 사용하여 배포 자동화</span><span class="sxs-lookup"><span data-stu-id="f9226-198">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="f9226-199">일반적으로 배포에는 [dotnet publish](/dotnet/core/tools/dotnet-publish)에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-199">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="f9226-200">예를 들어 추가 파일이 필요하거나 *publish* 폴더에서 제외될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-200">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="f9226-201">Visual Studio에서는 웹 배포에 MSBuild를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-201">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="f9226-202">자세한 내용은 <xref:host-and-deploy/visual-studio-publish-profiles> 및 [Using MSBuild and Team Foundation Build](http://msbuildbook.com/)(MSBuild 및 Team Foundation Build 사용) 책을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-202">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="f9226-203">[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:host-and-deploy/azure-apps/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 앱을 직접 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-203">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="f9226-204">Azure DevOps Services는 [Azure App Service에 지속적인 배포](/azure/devops/pipelines/targets/webapp)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-204">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="f9226-205">자세한 내용은 [ASP.NET Core 및 Azure에서 DevOps](xref:azure/devops/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-205">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="f9226-206">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="f9226-206">Publish to Azure</span></span>

<span data-ttu-id="f9226-207">Visual Studio를 사용하여 Azure에 앱을 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-207">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="f9226-208">추가 예제는 [Azure에서 ASP.NET Core 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9226-208">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="f9226-209">Windows에서 MSDeploy를 사용하여 게시</span><span class="sxs-lookup"><span data-stu-id="f9226-209">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="f9226-210">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령을 사용하여 Windows 명령 프롬프트에서 수행하는 방법을 비롯하여 Visual Studio 게시 프로필을 사용하여 앱을 게시하는 방법에 대한 지침은 <xref:host-and-deploy/visual-studio-publish-profiles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-210">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="f9226-211">IIS(인터넷 정보 서비스)</span><span class="sxs-lookup"><span data-stu-id="f9226-211">Internet Information Services (IIS)</span></span>

<span data-ttu-id="f9226-212">*web.config* 파일에 제공된 구성을 사용하여 IIS(인터넷 정보 서비스)에 배포하는 경우 <xref:host-and-deploy/iis/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-212">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="f9226-213">웹 팜에서 호스트</span><span class="sxs-lookup"><span data-stu-id="f9226-213">Host in a web farm</span></span>

<span data-ttu-id="f9226-214">웹 팜 환경에서 ASP.NET Core 앱을 호스팅하는 구성에 대한 자세한 내용(예: 확장성을 위해 앱의 여러 인스턴스 배포)은 <xref:host-and-deploy/web-farm>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-214">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="f9226-215">Docker에서 호스트</span><span class="sxs-lookup"><span data-stu-id="f9226-215">Host on Docker</span></span>

<span data-ttu-id="f9226-216">자세한 내용은 <xref:host-and-deploy/docker/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9226-216">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9226-217">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9226-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="f9226-218">ASP.NET 호스팅</span><span class="sxs-lookup"><span data-stu-id="f9226-218">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
