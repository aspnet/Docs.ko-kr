---
title: ASP.NET Core의 Docker 이미지
author: rick-anderson
description: Docker 레지스트리에서 게시된 ASP.NET Core Docker 이미지를 사용하는 방법을 알아봅니다. 고유한 이미지를 풀하고 빌드합니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: b29ce03366e5c0e815de0874f5b96efb9ba5326c
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585957"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="24f83-104">ASP.NET Core의 Docker 이미지</span><span class="sxs-lookup"><span data-stu-id="24f83-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="24f83-105">이 자습서에서는 Docker 컨테이너에서 ASP.NET Core 앱을 실행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="24f83-106">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="24f83-107">ASP.NET Core Docker 이미지에 관해 알아보기</span><span class="sxs-lookup"><span data-stu-id="24f83-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="24f83-108">ASP.NET Core 샘플 앱 다운로드</span><span class="sxs-lookup"><span data-stu-id="24f83-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="24f83-109">샘플 앱을 로컬로 실행</span><span class="sxs-lookup"><span data-stu-id="24f83-109">Run the sample app locally</span></span>
> * <span data-ttu-id="24f83-110">Linux 컨테이너에서 샘플 앱 실행</span><span class="sxs-lookup"><span data-stu-id="24f83-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="24f83-111">Windows 컨테이너에서 샘플 앱 실행</span><span class="sxs-lookup"><span data-stu-id="24f83-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="24f83-112">수동으로 빌드 및 배포</span><span class="sxs-lookup"><span data-stu-id="24f83-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="24f83-113">ASP.NET Core Docker 이미지</span><span class="sxs-lookup"><span data-stu-id="24f83-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="24f83-114">이 자습서의 경우 ASP.NET Core 샘플 앱을 다운로드하고 Docker 컨테이너에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="24f83-115">샘플은 Linux 컨테이너와 Windows 컨테이너 둘 다에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="24f83-116">샘플 Dockerfile은 [Docker 다단계 빌드 기능](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)을 사용하여 서로 다른 컨테이너에서 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="24f83-117">빌드 및 실행 컨테이너는 Microsoft에서 Docker 허브에 제공한 이미지를 기반으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="24f83-118">샘플에서는 앱을 빌드하는 데 이 이미지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="24f83-119">이미지는 CLI(명령줄 도구)가 포함된 .NET SDK를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="24f83-120">이미지는 로컬 개발, 디버깅 및 유닛 테스트에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="24f83-121">개발 및 컴파일용으로 설치된 도구는 이미지를 비교적 크게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="24f83-122">샘플에서는 앱을 빌드하는 데 이 이미지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="24f83-123">이미지는 CLI(명령줄 도구)가 포함된 .NET Core SDK를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="24f83-124">이미지는 로컬 개발, 디버깅 및 유닛 테스트에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="24f83-125">개발 및 컴파일용으로 설치된 도구는 이미지를 비교적 크게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="24f83-126">샘플에서는 앱을 실행하는 데 이 이미지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="24f83-127">이 이미지는 ASP.NET Core 런타임 및 라이브러리를 포함하며 프로덕션에서 실행 중인 앱에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="24f83-128">배포 및 앱 시작 속도를 위해 디자인된 이 이미지는 비교적 작기 때문에 Docker 레지스트리에서 Docker 호스트까지 네트워크 성능이 최적화됩니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="24f83-129">앱을 실행하는 데 필요한 이진 파일 및 콘텐츠만 컨테이너에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="24f83-130">콘텐츠를 실행할 준비가 되면 `docker run`부터 앱 시작까지 가장 빠른 시간에 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="24f83-131">동적 코드 컴파일은 Docker 모델에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="24f83-132">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="24f83-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="24f83-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="24f83-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="24f83-134">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="24f83-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="24f83-135">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="24f83-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="24f83-136">Docker 클라이언트 18.03 이상</span><span class="sxs-lookup"><span data-stu-id="24f83-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="24f83-137">Linux 배포</span><span class="sxs-lookup"><span data-stu-id="24f83-137">Linux distributions</span></span>
    * [<span data-ttu-id="24f83-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="24f83-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="24f83-139">Debian</span><span class="sxs-lookup"><span data-stu-id="24f83-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="24f83-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="24f83-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="24f83-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="24f83-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="24f83-142">macOS</span><span class="sxs-lookup"><span data-stu-id="24f83-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="24f83-143">Windows</span><span class="sxs-lookup"><span data-stu-id="24f83-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="24f83-144">Git</span><span class="sxs-lookup"><span data-stu-id="24f83-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="24f83-145">샘플 앱 다운로드</span><span class="sxs-lookup"><span data-stu-id="24f83-145">Download the sample app</span></span>

* <span data-ttu-id="24f83-146">[.NET Docker 리포지토리](https://github.com/dotnet/dotnet-docker)를 복제하여 샘플을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="24f83-147">로컬에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="24f83-147">Run the app locally</span></span>

* <span data-ttu-id="24f83-148">*dotnet-docker/samples/aspnetapp/aspnetapp* 에 있는 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="24f83-149">다음 명령을 실행하여 앱을 로컬로 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="24f83-150">브라우저에서 `http://localhost:5000`으로 이동하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="24f83-151">명령 프롬프트에서 Ctrl+C를 눌러 앱을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="24f83-152">Linux 컨테이너에서 실행</span><span class="sxs-lookup"><span data-stu-id="24f83-152">Run in a Linux container</span></span>

* <span data-ttu-id="24f83-153">Docker 클라이언트에서 [Linux 컨테이너로 전환](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="24f83-154">*dotnet-docker/samples/aspnetapp* 에 있는 Dockerfile 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="24f83-155">다음 명령을 실행하여 Docker에서 샘플을 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="24f83-156">`build` 명령 인수:</span><span class="sxs-lookup"><span data-stu-id="24f83-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="24f83-157">이미지 이름을 aspnetapp으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="24f83-158">현재 폴더(끝에 마침표)에서 Dockerfile을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="24f83-159">실행 명령 인수:</span><span class="sxs-lookup"><span data-stu-id="24f83-159">The run command arguments:</span></span>
  * <span data-ttu-id="24f83-160">의사 TTY를 할당하고 연결되지 않은 경우에도 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="24f83-161">(`--interactive --tty`와 결과가 동일함)</span><span class="sxs-lookup"><span data-stu-id="24f83-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="24f83-162">종료될 때 자동으로 컨테이너를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="24f83-163">로컬 머신의 포트 5000을 컨테이너의 포트 80에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="24f83-164">컨테이너 이름을 aspnetcore_sample로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="24f83-165">aspnetapp 이미지를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="24f83-166">브라우저에서 `http://localhost:5000`으로 이동하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="24f83-167">Windows 컨테이너에서 실행</span><span class="sxs-lookup"><span data-stu-id="24f83-167">Run in a Windows container</span></span>

* <span data-ttu-id="24f83-168">Docker 클라이언트에서 [Windows 컨테이너로 전환](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="24f83-169">`dotnet-docker/samples/aspnetapp`에 있는 Docker 파일 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="24f83-170">다음 명령을 실행하여 Docker에서 샘플을 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="24f83-171">Windows 컨테이너의 경우 컨테이너의 IP 주소가 필요합니다(`http://localhost:5000`로 이동되지 않음).</span><span class="sxs-lookup"><span data-stu-id="24f83-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="24f83-172">다른 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="24f83-173">`docker ps`를 실행하여 실행 중인 컨테이너를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="24f83-174">“aspnetcore_sample” 컨테이너가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="24f83-175">`docker exec aspnetcore_sample ipconfig`를 실행하여 컨테이너의 IP 주소를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="24f83-176">명령의 출력은 다음 예제와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="24f83-177">컨테이너 IPv4 주소(예: 172.29.245.43)를 복사하고 브라우저 주소 표시줄에 붙여넣어 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="24f83-178">수동으로 빌드 및 배포</span><span class="sxs-lookup"><span data-stu-id="24f83-178">Build and deploy manually</span></span>

<span data-ttu-id="24f83-179">일부 시나리오에서는 런타임에 필요한 앱의 자산을 컨테이너에 복사하여 앱을 컨테이너에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="24f83-180">이 섹션에서는 수동으로 배포하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="24f83-181">*dotnet-docker/samples/aspnetapp/aspnetapp* 에 있는 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="24f83-182">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="24f83-183">명령 인수:</span><span class="sxs-lookup"><span data-stu-id="24f83-183">The command arguments:</span></span>
  * <span data-ttu-id="24f83-184">릴리스 모드에서 앱을 빌드합니다(기본값은 디버그 모드).</span><span class="sxs-lookup"><span data-stu-id="24f83-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="24f83-185">‘게시된’ 폴더에 자산을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="24f83-186">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-186">Run the app.</span></span>

  * <span data-ttu-id="24f83-187">Windows:</span><span class="sxs-lookup"><span data-stu-id="24f83-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="24f83-188">Linux:</span><span class="sxs-lookup"><span data-stu-id="24f83-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="24f83-189">`http://localhost:5000`으로 이동하여 홈페이지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="24f83-190">Docker 컨테이너 내에서 수동으로 게시된 앱을 사용하려면 새 *Dockerfile* 을 만들고 `docker build .` 명령을 사용하여 컨테이너를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="24f83-191">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="24f83-191">The Dockerfile</span></span>

<span data-ttu-id="24f83-192">이전에 실행한 `docker build` 명령에서 사용되는 *Dockerfile* 은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="24f83-193">이 섹션에서 빌드 및 배포하기 위해 사용한 것과 동일한 방식으로 `dotnet publish`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="24f83-194">위의 *Dockerfile* 에서 `*.csproj` 파일은 개별 ‘계층’으로 복사 및 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="24f83-195">`docker build` 명령은 이미지를 빌드할 때 기본 제공 캐시를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="24f83-196">`docker build` 명령을 마지막으로 실행한 후 `*.csproj` 파일이 변경되지 않았다면 `dotnet restore` 명령을 다시 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="24f83-197">대신 해당 `dotnet restore` 계층에 대한 기본 제공 캐시가 다시 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="24f83-198">자세한 내용은 [Dockerfiles 작성에 대한 모범 사례](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24f83-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="24f83-199">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="24f83-199">The Dockerfile</span></span>

<span data-ttu-id="24f83-200">이전에 실행한 `docker build` 명령에서 사용되는 *Dockerfile* 은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="24f83-201">이 섹션에서 빌드 및 배포하기 위해 사용한 것과 동일한 방식으로 `dotnet publish`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="24f83-202">위의 Dockerfile에 설명된 것처럼 `*.csproj` 파일은 개별 ‘계층’으로서 복사 및 복원됩니다. </span><span class="sxs-lookup"><span data-stu-id="24f83-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="24f83-203">`docker build` 명령은 이미지를 빌드할 때 기본 제공 캐시를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="24f83-204">`docker build` 명령을 마지막으로 실행한 후 `*.csproj` 파일이 변경되지 않았다면 `dotnet restore` 명령을 다시 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="24f83-205">대신 해당 `dotnet restore` 계층에 대한 기본 제공 캐시가 다시 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="24f83-206">자세한 내용은 [Dockerfiles 작성에 대한 모범 사례](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24f83-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="24f83-207">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="24f83-207">The Dockerfile</span></span>

<span data-ttu-id="24f83-208">이전에 실행한 `docker build` 명령에서 사용되는 *Dockerfile* 은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="24f83-209">이 섹션에서 빌드 및 배포하기 위해 사용한 것과 동일한 방식으로 `dotnet publish`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="24f83-210">추가 자료</span><span class="sxs-lookup"><span data-stu-id="24f83-210">Additional resources</span></span>

* [<span data-ttu-id="24f83-211">Docker 빌드 명령</span><span class="sxs-lookup"><span data-stu-id="24f83-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="24f83-212">Docker 실행 명령</span><span class="sxs-lookup"><span data-stu-id="24f83-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="24f83-213">[ASP.NET Core Docker 샘플](https://github.com/dotnet/dotnet-docker)(이 자습서에서 사용되는 샘플)</span><span class="sxs-lookup"><span data-stu-id="24f83-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="24f83-214">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="24f83-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="24f83-215">Visual Studio Docker 도구로 작업</span><span class="sxs-lookup"><span data-stu-id="24f83-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* <span data-ttu-id="24f83-216">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="24f83-216">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)</span></span>
* <span data-ttu-id="24f83-217">[GC using Docker and small containers](xref:performance/memory#sc)(Docker 및 소형 컨테이너를 사용하는 GC)</span><span class="sxs-lookup"><span data-stu-id="24f83-217">[GC using Docker and small containers](xref:performance/memory#sc)</span></span>

## <a name="next-steps"></a><span data-ttu-id="24f83-218">다음 단계</span><span class="sxs-lookup"><span data-stu-id="24f83-218">Next steps</span></span>

<span data-ttu-id="24f83-219">샘플 앱이 포함된 Git 리포지토리도 문서를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="24f83-220">리포지토리에서 사용 가능한 리소스의 개요를 보려면 [README 파일](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="24f83-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="24f83-221">특히, HTTPS를 구현하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="24f83-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="24f83-222">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)(HTTPS를 통해 Docker를 사용하여 ASP.NET Core 애플리케이션 개발)</span><span class="sxs-lookup"><span data-stu-id="24f83-222">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)</span></span>
