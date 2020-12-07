---
title: ASP.NET Core 호스트 및 배포
author: rick-anderson
description: 호스팅 환경을 설정하고 ASP.NET Core 앱을 배포하는 방법을 알아봅니다.
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
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556621"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="fb32b-103">ASP.NET Core 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="fb32b-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fb32b-104">일반적으로 ASP.NET Core 앱을 호스팅 환경에 배포하기 위해서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="fb32b-105">게시한 앱을 호스팅 서버의 폴더에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="fb32b-106">요청이 도착할 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="fb32b-107">역항뱡 프록시 구성이 필요한 경우, 요청을 앱으로 전달하는 역방향 프록시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="fb32b-108">폴더에 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-108">Publish to a folder</span></span>

<span data-ttu-id="fb32b-109">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 앱 코드를 컴파일하고 앱을 실행하는 데 필요한 파일을 *publish* 폴더로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="fb32b-110">Visual Studio에서 배포하는 경우에는 파일이 배포 대상으로 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="fb32b-111">설정 파일 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-111">Publish settings files</span></span>

<span data-ttu-id="fb32b-112">`*.json` 파일은 기본적으로 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-112">`*.json` files are published by default.</span></span> <span data-ttu-id="fb32b-113">다른 설정 파일을 게시하려면 프로젝트 파일의 [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) 요소에 해당 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="fb32b-114">다음 예에서는 XML 파일을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="fb32b-115">폴더 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="fb32b-115">Folder contents</span></span>

<span data-ttu-id="fb32b-116">*publish* 폴더에는 하나 이상의 앱 어셈블리 파일, 종속성, 그리고 선택적으로 .NET 런타임이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="fb32b-117">.NET Core 앱은 *자체 포함된 배포* 또는 *프레임워크 종속 배포* 로 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="fb32b-118">앱이 자체 포함된 배포인 경우, .NET 런타임이 포함된 어셈블리 파일은 *publish* 폴더에 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="fb32b-119">앱이 프레임워크 종속인 경우 서버에 설치된 .NET 버전에 대한 참조가 앱에 포함되므로 .NET 런타임 파일이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="fb32b-120">기본 배포 모델은 프레임워크 종속입니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="fb32b-121">자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="fb32b-122">*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="fb32b-123">자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="fb32b-124">프로세스 관리자 설정</span><span class="sxs-lookup"><span data-stu-id="fb32b-124">Set up a process manager</span></span>

<span data-ttu-id="fb32b-125">ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="fb32b-126">시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="fb32b-127">ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="fb32b-128">Linux</span><span class="sxs-lookup"><span data-stu-id="fb32b-128">Linux</span></span>
  * [<span data-ttu-id="fb32b-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="fb32b-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="fb32b-130">Apache</span><span class="sxs-lookup"><span data-stu-id="fb32b-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="fb32b-131">Windows</span><span class="sxs-lookup"><span data-stu-id="fb32b-131">Windows</span></span>
  * [<span data-ttu-id="fb32b-132">IIS</span><span class="sxs-lookup"><span data-stu-id="fb32b-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="fb32b-133">Windows 서비스</span><span class="sxs-lookup"><span data-stu-id="fb32b-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="fb32b-134">역방향 프록시 설정</span><span class="sxs-lookup"><span data-stu-id="fb32b-134">Set up a reverse proxy</span></span>

<span data-ttu-id="fb32b-135">앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 사용하는 경우 [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) 또는 [IIS](xref:host-and-deploy/iis/index)를 역방향 프록시 서버로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="fb32b-136">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="fb32b-137">&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="fb32b-138">자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fb32b-139">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="fb32b-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fb32b-140">프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="fb32b-141">추가 구성이 없는 앱에는 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소에 대한 액세스 권한이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="fb32b-142">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="fb32b-143">Visual Studio 및 MSBuild를 사용하여 배포 자동화</span><span class="sxs-lookup"><span data-stu-id="fb32b-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="fb32b-144">일반적으로 배포에는 [dotnet publish](/dotnet/core/tools/dotnet-publish)에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="fb32b-145">예를 들어 추가 파일이 필요하거나 *publish* 폴더에서 제외될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="fb32b-146">Visual Studio에서는 웹 배포에 [MSBuild](/visualstudio/msbuild/msbuild)를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="fb32b-147">자세한 내용은 <xref:host-and-deploy/visual-studio-publish-profiles> 및 [Using MSBuild and Team Foundation Build](http://msbuildbook.com/)(MSBuild 및 Team Foundation Build 사용) 책을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="fb32b-148">[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:host-and-deploy/azure-apps/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 앱을 직접 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="fb32b-149">Azure DevOps Services는 [Azure App Service에 지속적인 배포](/azure/devops/pipelines/targets/webapp)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="fb32b-150">자세한 내용은 [ASP.NET Core 및 Azure에서 DevOps](xref:azure/devops/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="fb32b-151">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-151">Publish to Azure</span></span>

<span data-ttu-id="fb32b-152">Visual Studio를 사용하여 Azure에 앱을 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="fb32b-153">추가 예제는 [Azure에서 ASP.NET Core 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="fb32b-154">Windows에서 MSDeploy를 사용하여 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="fb32b-155">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령을 사용하여 Windows 명령 프롬프트에서 수행하는 방법을 비롯하여 Visual Studio 게시 프로필을 사용하여 앱을 게시하는 방법에 대한 지침은 <xref:host-and-deploy/visual-studio-publish-profiles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="fb32b-156">IIS(인터넷 정보 서비스)</span><span class="sxs-lookup"><span data-stu-id="fb32b-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="fb32b-157">*web.config* 파일에 제공된 구성을 사용하여 IIS(인터넷 정보 서비스)에 배포하는 경우 <xref:host-and-deploy/iis/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="fb32b-158">웹 팜에서 호스트</span><span class="sxs-lookup"><span data-stu-id="fb32b-158">Host in a web farm</span></span>

<span data-ttu-id="fb32b-159">웹 팜 환경에서 ASP.NET Core 앱을 호스팅하는 구성에 대한 자세한 내용(예: 확장성을 위해 앱의 여러 인스턴스 배포)은 <xref:host-and-deploy/web-farm>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="fb32b-160">Docker에서 호스트</span><span class="sxs-lookup"><span data-stu-id="fb32b-160">Host on Docker</span></span>

<span data-ttu-id="fb32b-161">자세한 내용은 <xref:host-and-deploy/docker/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="fb32b-162">상태 검사 수행</span><span class="sxs-lookup"><span data-stu-id="fb32b-162">Perform health checks</span></span>

<span data-ttu-id="fb32b-163">상태 검사 미들웨어를 사용하여 앱 및 그 종속성에 대해 상태 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="fb32b-164">자세한 내용은 <xref:host-and-deploy/health-checks>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb32b-165">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fb32b-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="fb32b-166">ASP.NET 호스팅</span><span class="sxs-lookup"><span data-stu-id="fb32b-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fb32b-167">일반적으로 ASP.NET Core 앱을 호스팅 환경에 배포하기 위해서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="fb32b-168">게시한 앱을 호스팅 서버의 폴더에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="fb32b-169">요청이 도착할 때 앱을 시작하고 작동이 중단되거나 서버가 다시 부팅된 후 앱을 다시 시작하는 프로세스 관리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="fb32b-170">역항뱡 프록시 구성이 필요한 경우, 요청을 앱으로 전달하는 역방향 프록시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="fb32b-171">폴더에 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-171">Publish to a folder</span></span>

<span data-ttu-id="fb32b-172">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 앱 코드를 컴파일하고 앱을 실행하는 데 필요한 파일을 *publish* 폴더로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="fb32b-173">Visual Studio에서 배포하는 경우에는 파일이 배포 대상으로 복사되기 전에 `dotnet publish` 단계가 자동으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="fb32b-174">폴더 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="fb32b-174">Folder contents</span></span>

<span data-ttu-id="fb32b-175">*publish* 폴더에는 하나 이상의 앱 어셈블리 파일, 종속성, 그리고 선택적으로 .NET 런타임이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="fb32b-176">.NET Core 앱은 *자체 포함된 배포* 또는 *프레임워크 종속 배포* 로 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="fb32b-177">앱이 자체 포함된 배포인 경우, .NET 런타임이 포함된 어셈블리 파일은 *publish* 폴더에 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="fb32b-178">앱이 프레임워크 종속인 경우 서버에 설치된 .NET 버전에 대한 참조가 앱에 포함되므로 .NET 런타임 파일이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="fb32b-179">기본 배포 모델은 프레임워크 종속입니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="fb32b-180">자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="fb32b-181">*.exe* 및 *.dll* 파일 이외에 ASP.NET Core 앱에 대한 *publish* 폴더에는 일반적으로 구성 파일, 정적 자산 및 MVC 뷰가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="fb32b-182">자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="fb32b-183">프로세스 관리자 설정</span><span class="sxs-lookup"><span data-stu-id="fb32b-183">Set up a process manager</span></span>

<span data-ttu-id="fb32b-184">ASP.NET Core 앱은 서버가 부팅되고 작동 중단 후 다시 시작될 때 시작되어야 하는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="fb32b-185">시작 및 다시 시작을 자동화하려면 프로세스 관리자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="fb32b-186">ASP.NET Core에 대한 가장 일반적인 프로세스 관리자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="fb32b-187">Linux</span><span class="sxs-lookup"><span data-stu-id="fb32b-187">Linux</span></span>
  * [<span data-ttu-id="fb32b-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="fb32b-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="fb32b-189">Apache</span><span class="sxs-lookup"><span data-stu-id="fb32b-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="fb32b-190">Windows</span><span class="sxs-lookup"><span data-stu-id="fb32b-190">Windows</span></span>
  * [<span data-ttu-id="fb32b-191">IIS</span><span class="sxs-lookup"><span data-stu-id="fb32b-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="fb32b-192">Windows 서비스</span><span class="sxs-lookup"><span data-stu-id="fb32b-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="fb32b-193">역방향 프록시 설정</span><span class="sxs-lookup"><span data-stu-id="fb32b-193">Set up a reverse proxy</span></span>

<span data-ttu-id="fb32b-194">앱에서 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 사용하는 경우 [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) 또는 [IIS](xref:host-and-deploy/iis/index)를 역방향 프록시 서버로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="fb32b-195">역방향 프록시 서버는 인터넷에서 HTTP 요청을 받아서 Kestrel에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="fb32b-196">&mdash;역방향 프록시 서버가 있는 구성과 없는 구성 모두&mdash; 지원되는 호스팅 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="fb32b-197">자세한 내용은 [Kestrel를 역방향 프록시와 함께 사용할 경우](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fb32b-198">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="fb32b-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fb32b-199">프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="fb32b-200">추가 구성이 없는 앱에는 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소에 대한 액세스 권한이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="fb32b-201">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="fb32b-202">Visual Studio 및 MSBuild를 사용하여 배포 자동화</span><span class="sxs-lookup"><span data-stu-id="fb32b-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="fb32b-203">일반적으로 배포에는 [dotnet publish](/dotnet/core/tools/dotnet-publish)에서 서버로 출력을 복사하는 것 외에 추가 작업이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="fb32b-204">예를 들어 추가 파일이 필요하거나 *publish* 폴더에서 제외될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="fb32b-205">Visual Studio에서는 웹 배포에 MSBuild를 사용하고 MSBuild를 사용자 지정하여 배포 중에 많은 다른 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="fb32b-206">자세한 내용은 <xref:host-and-deploy/visual-studio-publish-profiles> 및 [Using MSBuild and Team Foundation Build](http://msbuildbook.com/)(MSBuild 및 Team Foundation Build 사용) 책을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="fb32b-207">[웹 게시 기능](xref:tutorials/publish-to-azure-webapp-using-vs)을 사용하거나 [기본 제공 Git 지원](xref:host-and-deploy/azure-apps/azure-continuous-deployment)을 사용하여 Visual Studio에서 Azure App Service로 앱을 직접 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="fb32b-208">Azure DevOps Services는 [Azure App Service에 지속적인 배포](/azure/devops/pipelines/targets/webapp)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="fb32b-209">자세한 내용은 [ASP.NET Core 및 Azure에서 DevOps](xref:azure/devops/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="fb32b-210">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-210">Publish to Azure</span></span>

<span data-ttu-id="fb32b-211">Visual Studio를 사용하여 Azure에 앱을 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="fb32b-212">추가 예제는 [Azure에서 ASP.NET Core 웹앱 만들기](/azure/app-service/app-service-web-get-started-dotnet)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb32b-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="fb32b-213">Windows에서 MSDeploy를 사용하여 게시</span><span class="sxs-lookup"><span data-stu-id="fb32b-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="fb32b-214">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령을 사용하여 Windows 명령 프롬프트에서 수행하는 방법을 비롯하여 Visual Studio 게시 프로필을 사용하여 앱을 게시하는 방법에 대한 지침은 <xref:host-and-deploy/visual-studio-publish-profiles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="fb32b-215">IIS(인터넷 정보 서비스)</span><span class="sxs-lookup"><span data-stu-id="fb32b-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="fb32b-216">*web.config* 파일에 제공된 구성을 사용하여 IIS(인터넷 정보 서비스)에 배포하는 경우 <xref:host-and-deploy/iis/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="fb32b-217">웹 팜에서 호스트</span><span class="sxs-lookup"><span data-stu-id="fb32b-217">Host in a web farm</span></span>

<span data-ttu-id="fb32b-218">웹 팜 환경에서 ASP.NET Core 앱을 호스팅하는 구성에 대한 자세한 내용(예: 확장성을 위해 앱의 여러 인스턴스 배포)은 <xref:host-and-deploy/web-farm>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="fb32b-219">Docker에서 호스트</span><span class="sxs-lookup"><span data-stu-id="fb32b-219">Host on Docker</span></span>

<span data-ttu-id="fb32b-220">자세한 내용은 <xref:host-and-deploy/docker/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb32b-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb32b-221">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fb32b-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="fb32b-222">ASP.NET 호스팅</span><span class="sxs-lookup"><span data-stu-id="fb32b-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
