---
title: ASP.NET Core를 포함한 Visual Studio 컨테이너 도구
author: spboyer
description: Visual Studio 도구 및 Windows용 Docker를 사용하여 ASP.NET Core 앱을 컨테이너화하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 2cfd200c44290a0931cdeb2f68e99b90c11aa612
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059821"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="49ef0-103">ASP.NET Core를 포함한 Visual Studio 컨테이너 도구</span><span class="sxs-lookup"><span data-stu-id="49ef0-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="49ef0-104">Visual Studio 2017 이상 버전은 .NET Core를 대상으로 하는 컨테이너화된 ASP.NET Core 앱의 빌드, 디버그 및 실행을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="49ef0-105">Windows 및 Linux 컨테이너가 모두 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="49ef0-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49ef0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49ef0-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="49ef0-107">Prerequisites</span></span>

* [<span data-ttu-id="49ef0-108">Windows용 Docker</span><span class="sxs-lookup"><span data-stu-id="49ef0-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="49ef0-109">**.NET Core 플랫폼 간 개발** 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="49ef0-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="49ef0-110">설치 및 설정</span><span class="sxs-lookup"><span data-stu-id="49ef0-110">Installation and setup</span></span>

<span data-ttu-id="49ef0-111">Docker를 설치하려면 우선 [Windows용 Docker 클라이언트에 있는 정보를 검토합니다. 설치하기 전에 알아야 할 사항](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)의 정보를 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="49ef0-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="49ef0-112">다음으로 [Windows용 Docker](https://docs.docker.com/docker-for-windows/install/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="49ef0-113">Windows용 Docker에서 **[공유 드라이브](https://docs.docker.com/docker-for-windows/#shared-drives)** 는 볼륨 매핑 및 디버깅을 지원하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="49ef0-114">시스템 트레이의 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 **설정** 을 선택한 다음, **공유 드라이브** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="49ef0-115">Docker에서 파일을 저장하는 드라이브를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="49ef0-116">**적용** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-116">Click **Apply**.</span></span>

![컨테이너에 로컬 C 드라이브 공유를 선택하는 대화 상자](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="49ef0-118">Visual Studio 2017 버전 15.6 이상은 **공유 드라이브** 가 구성되지 않은 경우 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="49ef0-119">Docker 컨테이너에 프로젝트 추가</span><span class="sxs-lookup"><span data-stu-id="49ef0-119">Add a project to a Docker container</span></span>

<span data-ttu-id="49ef0-120">ASP.NET Core 프로젝트를 컨테이너화하려면 프로젝트가 .NET Core를 대상으로 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="49ef0-121">Linux 및 Windows 컨테이너가 둘 다 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="49ef0-122">프로젝트에 Docker 지원을 추가할 때 Windows 또는 Linux 컨테이너를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="49ef0-123">Docker 호스트는 동일한 컨테이너 형식을 실행 중이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="49ef0-124">실행 중인 Docker 인스턴스에서 컨테이너 형식을 변경하려면 시스템 트레이의 Docker 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환** 또는 **Linux 컨테이너로 전환** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="49ef0-125">새 앱</span><span class="sxs-lookup"><span data-stu-id="49ef0-125">New app</span></span>

<span data-ttu-id="49ef0-126">**ASP.NET Core 웹 애플리케이션** 프로젝트 템플릿을 사용하여 새 앱을 만들 때 **Docker 지원 사용** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Docker 지원 사용 확인란](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="49ef0-128">대상 프레임워크가 .NET Core인 경우 **OS** 드롭다운에서 컨테이너 유형을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="49ef0-129">기존 앱</span><span class="sxs-lookup"><span data-stu-id="49ef0-129">Existing app</span></span>

<span data-ttu-id="49ef0-130">.NET Core를 대상으로 하는 ASP.NET Core 프로젝트의 경우 도구를 통해 Docker 지원을 추가하는 두 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="49ef0-131">Visual Studio에서 프로젝트를 열고 다음 옵션 중 하나를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="49ef0-132">**프로젝트** 메뉴에서 **Docker 지원** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="49ef0-133">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Docker 지원** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="49ef0-134">Visual Studio 컨테이너 도구는 .NET Framework를 대상으로 하는 기존 ASP.NET Core 프로젝트에 Docker 추가를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="49ef0-135">Dockerfile 개요</span><span class="sxs-lookup"><span data-stu-id="49ef0-135">Dockerfile overview</span></span>

<span data-ttu-id="49ef0-136">최종 Docker 이미지를 만들기 위한 레시피인 *Dockerfile* 은 프로젝트 루트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="49ef0-137">그 안의 명령을 이해하려면 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="49ef0-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="49ef0-138">이 특정 *Dockerfile* 은 빌드 단계라는 네 개의 단계를 통해 [다단계 빌드](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="49ef0-139">앞의 *Dockerfile* 은 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) 이미지를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="49ef0-140">이 기본 이미지에는 ASP.NET Core 런타임 및 NuGet 패키지가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="49ef0-141">패키지는 시작 성능을 향상시키기 위해 JIT(Just-In-Time) 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="49ef0-142">새 프로젝트 대화 상자의 **HTTPS에 대한 구성** 확인란이 선택되면 *Dockerfile* 은 두 개의 포트를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="49ef0-143">한 포트는 HTTP 트래픽에 사용되고 다른 포트는 HTTPS에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="49ef0-144">이 확인란을 선택하지 않으면 단일 포트(80)가 HTTP 트래픽에 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="49ef0-145">앞의 *Dockerfile* 은 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) 이미지를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="49ef0-146">이 기본 이미지는 시작 성능을 개선하기 위해 JIT(Just-In-Time) 컴파일된 ASP.NET Core NuGet 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="49ef0-147">앱에 컨테이너 오케스트레이터 지원 추가</span><span class="sxs-lookup"><span data-stu-id="49ef0-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="49ef0-148">Visual Studio 2017 버전 15.7 또는 이전 버전에서는 [Docker Compose](https://docs.docker.com/compose/overview/)를 단독 컨테이너 오케스트레이션 솔루션으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="49ef0-149">Docker Compose 아티팩트는 **추가** > **Docker 지원** 을 통해 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="49ef0-150">Visual Studio 2017 버전 15.8 또는 이후 버전에서는 지시하는 경우에만 오케스트레이션이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="49ef0-151">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **컨테이너 오케스트레이터 지원** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="49ef0-152">다음 중에서 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-152">The following choices are available:</span></span> 

* [<span data-ttu-id="49ef0-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="49ef0-153">Docker Compose</span></span>](#docker-compose)
* [<span data-ttu-id="49ef0-154">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="49ef0-154">Service Fabric</span></span>](#service-fabric)
* [<span data-ttu-id="49ef0-155">Kubernetes/Helm </span><span class="sxs-lookup"><span data-stu-id="49ef0-155">Kubernetes/Helm </span></span>](https://helm.sh/)

### <a name="docker-compose"></a><span data-ttu-id="49ef0-156">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="49ef0-156">Docker Compose</span></span>

<span data-ttu-id="49ef0-157">Visual Studio 컨테이너 도구는 다음 파일을 통해 솔루션에 *docker-compose* 프로젝트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-157">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="49ef0-158">*docker-compose.dcproj*: 프로젝트를 나타내는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-158">*docker-compose.dcproj*: The file representing the project.</span></span> <span data-ttu-id="49ef0-159">사용할 OS를 지정하는 `<DockerTargetOS>` 요소를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-159">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="49ef0-160">*.dockerignore*: 빌드 컨텍스트를 생성할 때 제외할 파일 및 디렉터리 패턴을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-160">*.dockerignore*: Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="49ef0-161">*docker-compose.yml*: `docker-compose build` 및 `docker-compose run`을 각각 사용하여 빌드하고 실행할 이미지 컬렉션을 정의하는 데 사용되는 기본 [Docker Compose](https://docs.docker.com/compose/overview/) 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-161">*docker-compose.yml*: The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="49ef0-162">*docker-compose.override.yml*: Docker Compose에서 읽은 선택적 파일로, 서비스에 대한 구성 재정의를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-162">*docker-compose.override.yml*: An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="49ef0-163">Visual Studio는 `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"`을 실행하여 이러한 파일을 병합합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-163">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="49ef0-164">*docker-compose.yml* 파일에는 프로젝트를 실행할 때 생성되는 이미지의 이름을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-164">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="49ef0-165">앞의 예제에서 `image: hellodockertools`는 앱이 **디버그** 모드에서 실행될 때 `hellodockertools:dev` 이미지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-165">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="49ef0-166">앱이 **릴리스** 모드에서 실행될 때 `hellodockertools:latest` 이미지가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-166">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="49ef0-167">이미지가 레지스트리로 푸시되는 경우 이미지 이름 앞에 [Docker 허브](https://hub.docker.com/) 사용자 이름을 추가합니다(예: `dockerhubusername/hellodockertools`).</span><span class="sxs-lookup"><span data-stu-id="49ef0-167">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="49ef0-168">또는 구성에 따라 개인 레지스트리 URL을 포함하도록 이미지 이름을 변경합니다(예: `privateregistry.domain.com/hellodockertools`).</span><span class="sxs-lookup"><span data-stu-id="49ef0-168">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="49ef0-169">빌드 구성(예: 디버그 또는 릴리스)에 따라 다른 동작을 원하는 경우 구성별 *docker-compose* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-169">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="49ef0-170">파일은 빌드 구성에 따라 이름이 지정되어야(예: *docker-compose.vs.debug.yml* 및 *docker-compose.vs.release.yml*) 하며 *docker-compose-override.yml* 파일과 동일한 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-170">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="49ef0-171">구성별 재정의 파일을 사용하여 디버그 및 릴리스 빌드 구성에 대해 서로 다른 구성 설정(예: 환경 변수 또는 진입점)을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-171">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="49ef0-172">Docker Compose가 Visual Studio에서 실행하는 옵션을 표시하려면 Docker 프로젝트가 시작 프로젝트여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-172">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="49ef0-173">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="49ef0-173">Service Fabric</span></span>

<span data-ttu-id="49ef0-174">기본 [필수 구성 요소](#prerequisites) 외에도 [Service Fabric](/azure/service-fabric/) 오케스트레이션 솔루션에는 다음 필수 구성 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-174">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="49ef0-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) 버전 2.6 이상</span><span class="sxs-lookup"><span data-stu-id="49ef0-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="49ef0-176">Visual Studio의 **Azure 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="49ef0-176">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="49ef0-177">Service Fabric은 Windows의 로컬 개발 클러스터에서 Linux 컨테이너를 실행하는 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-177">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="49ef0-178">프로젝트에 Linux 컨테이너가 이미 사용되고 있으면 Visual Studio는 Windows 컨테이너로 전환하라는 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-178">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="49ef0-179">Visual Studio 컨테이너 도구는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-179">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="49ef0-180">*&lt;project_name&gt;Application* **Service Fabric Application** 프로젝트를 솔루션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-180">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="49ef0-181">*Dockerfile* 및 *.dockerignore* 파일을 ASP.NET Core 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-181">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="49ef0-182">ASP.NET Core 프로젝트에 *Dockerfile* 이 이미 있으면 *Dockerfile.original* 로 이름이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-182">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="49ef0-183">다음과 유사한 새 *Dockerfile* 이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-183">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="49ef0-184">`<IsServiceFabricServiceProject>` 요소를 ASP.NET Core 프로젝트의 *.csproj* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-184">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="49ef0-185">*PackageRoot* 폴더를 ASP.NET Core 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-185">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="49ef0-186">폴더에는 서비스 매니페스트와 새 서비스에 대한 설정이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-186">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="49ef0-187">자세한 내용은 [Azure Service Fabric에 Windows 컨테이너의 .NET 앱 배포](/azure/service-fabric/service-fabric-host-app-in-a-container)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="49ef0-187">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="49ef0-188">디버그</span><span class="sxs-lookup"><span data-stu-id="49ef0-188">Debug</span></span>

<span data-ttu-id="49ef0-189">도구 모음의 디버그 드롭다운에서 **Docker** 를 선택하고 앱에서 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-189">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="49ef0-190">**출력** 창의 **Docker** 뷰는 진행 중인 다음 작업을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-190">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="49ef0-191">*microsoft/dotnet* 런타임 이미지의 *2.1-aspnetcore-runtime* 태그를 가져옵니다(캐시에 아직 없는 경우).</span><span class="sxs-lookup"><span data-stu-id="49ef0-191">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="49ef0-192">이미지는 ASP.NET Core 및 .NET Core 런타임과 관련 라이브러리를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-192">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="49ef0-193">프로덕션 환경에서 ASP.NET Core 앱을 실행하는 데 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-193">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="49ef0-194">`ASPNETCORE_ENVIRONMENT` 환경 변수는 컨테이너 내에서 `Development`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-194">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="49ef0-195">동적으로 지정된 두 개의 포트가 노출됩니다. 하나는 HTTP용이고 다른 하나는 HTTPS용입니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-195">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="49ef0-196">localhost에 할당된 포트는 `docker ps` 명령으로 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-196">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="49ef0-197">앱이 컨테이너에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-197">The app is copied to the container.</span></span>
* <span data-ttu-id="49ef0-198">컨테이너에 연결된 디버거와 함께 기본 브라우저가 시작되며 동적으로 할당된 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-198">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="49ef0-199">앱의 최종 Docker 이미지는 *dev* 로 태그가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-199">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="49ef0-200">이미지는 *microsoft/dotnet* 기본 이미지의 *2.1-aspnetcore-runtime* 태그를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-200">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="49ef0-201">**패키지 관리자 콘솔**(PMC) 창에서 `docker images` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-201">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="49ef0-202">컴퓨터의 이미지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-202">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="49ef0-203">*microsoft/aspnetcore* 런타임 이미지를 가져옵니다(캐시에 아직 없는 경우).</span><span class="sxs-lookup"><span data-stu-id="49ef0-203">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="49ef0-204">`ASPNETCORE_ENVIRONMENT` 환경 변수는 컨테이너 내에서 `Development`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-204">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="49ef0-205">포트 80이 공개되고 localhost의 동적으로 할당된 포트에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-205">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="49ef0-206">포트는 Docker 호스트에 의해 결정되며 `docker ps` 명령을 사용하여 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-206">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="49ef0-207">앱이 컨테이너에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-207">The app is copied to the container.</span></span>
* <span data-ttu-id="49ef0-208">컨테이너에 연결된 디버거와 함께 기본 브라우저가 시작되며 동적으로 할당된 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-208">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="49ef0-209">앱의 최종 Docker 이미지는 *dev* 로 태그가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-209">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="49ef0-210">이미지는 *microsoft/aspnetcore* 기본 이미지를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-210">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="49ef0-211">**패키지 관리자 콘솔**(PMC) 창에서 `docker images` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-211">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="49ef0-212">컴퓨터의 이미지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-212">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="49ef0-213">**디버그** 구성은 볼륨 탑재를 사용하여 반복 환경을 제공하므로 *dev* 이미지에는 앱 콘텐츠가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-213">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="49ef0-214">이미지를 푸시하려면 **Release** 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-214">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="49ef0-215">PMC에서 `docker ps` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-215">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="49ef0-216">앱은 컨테이너를 사용하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-216">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="49ef0-217">편집하며 계속하기</span><span class="sxs-lookup"><span data-stu-id="49ef0-217">Edit and continue</span></span>

<span data-ttu-id="49ef0-218">정적 파일 및 Razor 뷰에 대한 변경 사항은 자동 업데이트되므로 컴파일 단계가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-218">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="49ef0-219">내용을 변경 및 저장하고 브라우저를 새로 고쳐 업데이트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-219">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="49ef0-220">코드 파일을 수정하려면 컨테이너 내에서 컴파일하고 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-220">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="49ef0-221">변경 후 컨테이너 내에서 `CTRL+F5`를 사용하여 프로세스를 수행하고 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-221">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="49ef0-222">Docker 컨테이너는 다시 빌드되거나 중지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-222">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="49ef0-223">PMC에서 `docker ps` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-223">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="49ef0-224">원래 컨테이너는 10분 이전을 기준으로 여전히 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-224">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="49ef0-225">Docker 이미지 게시</span><span class="sxs-lookup"><span data-stu-id="49ef0-225">Publish Docker images</span></span>

<span data-ttu-id="49ef0-226">앱의 개발 및 디버그 주기를 완료한 후 앱의 프로덕션 이미지를 만들 때 Visual Studio 컨테이너 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-226">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="49ef0-227">구성 드롭다운을 **릴리스** 로 변경하고 앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-227">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="49ef0-228">도구는 Docker 허브에서 compile/publish 이미지를 가져옵니다(캐시에 아직 없는 경우).</span><span class="sxs-lookup"><span data-stu-id="49ef0-228">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="49ef0-229">‘최신’ 태그로 이미지가 생성되면 이를 개인 레지스트리 또는 Docker 허브에 푸시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-229">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="49ef0-230">PMC에서 `docker images` 명령을 실행하여 이미지의 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-230">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="49ef0-231">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-231">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="49ef0-232">위의 출력에 나열되었던 `microsoft/aspnetcore-build` 및 `microsoft/aspnetcore` 이미지가 .NET Core 2.1부터 `microsoft/dotnet` 이미지로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-232">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="49ef0-233">자세한 내용은 [Docker 리포지토리 마이그레이션 공지](https://github.com/aspnet/Announcements/issues/298)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="49ef0-233">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="49ef0-234">`docker images` 명령은 *\<none>* (위에 나열되지 않음)으로 식별된 리포지토리 이름 및 태그로 매개자 이미지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-234">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="49ef0-235">이러한 이름이 지정되지 않는 이미지는 [다단계 빌드](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile* 에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-235">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="49ef0-236">최종 이미지 빌드의 효율성을 향상시키며&mdash;변경될 때 필요한 레이어만 다시 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-236">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="49ef0-237">더 이상 매개자 이미지가 필요하지 않은 경우 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) 명령을 사용하여 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-237">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="49ef0-238">*dev* 이미지와 비교하여 프로덕션 또는 릴리스 이미지의 크기가 작을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-238">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="49ef0-239">볼륨 매핑으로 인해 디버거 및 앱은 컨테이너 내부가 아닌 로컬 컴퓨터에서 실행되었습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-239">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="49ef0-240">*최신* 이미지는 호스트 컴퓨터에서 앱을 실행하는 데 필요한 앱 코드를 패키징했습니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-240">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="49ef0-241">따라서 델타는 앱 코드의 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="49ef0-241">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49ef0-242">추가 자료</span><span class="sxs-lookup"><span data-stu-id="49ef0-242">Additional resources</span></span>

* [<span data-ttu-id="49ef0-243">Visual Studio를 사용한 컨테이너 개발</span><span class="sxs-lookup"><span data-stu-id="49ef0-243">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="49ef0-244">Azure Service Fabric: 개발 환경 준비</span><span class="sxs-lookup"><span data-stu-id="49ef0-244">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="49ef0-245">Azure Service Fabric에 Windows 컨테이너의 .NET 앱 배포</span><span class="sxs-lookup"><span data-stu-id="49ef0-245">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="49ef0-246">Docker 관련 Visual Studio 개발 문제 해결</span><span class="sxs-lookup"><span data-stu-id="49ef0-246">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="49ef0-247">Visual Studio 컨테이너 도구 GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="49ef0-247">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* <span data-ttu-id="49ef0-248">[GC using Docker and small containers](xref:performance/memory#sc)(Docker 및 소형 컨테이너를 사용하는 GC)</span><span class="sxs-lookup"><span data-stu-id="49ef0-248">[GC using Docker and small containers](xref:performance/memory#sc)</span></span>
