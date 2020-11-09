---
title: Docker 컨테이너에서 ASP.NET Core 호스트
author: rick-anderson
description: Docker 컨테이너에서 ASP.NET Core 앱을 호스트하는 방법을 학습하기 위한 리소스 링크를 검색합니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059782"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="23fa2-103">Docker 컨테이너에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="23fa2-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="23fa2-104">다음 문서는 Docker에서 ASP.NET Core 앱을 호스팅하는 학습에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="23fa2-105">컨테이너 및 Docker 소개</span><span class="sxs-lookup"><span data-stu-id="23fa2-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="23fa2-106">애플리케이션 또는 서비스, 이에 해당하는 종속성 및 구성이 컨테이너 이미지로 패키지되는 소프트웨어 개발 방법인 컨테이너화에 대해 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="23fa2-107">이미지를 테스트한 후 호스트에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="23fa2-108">Docker란?</span><span class="sxs-lookup"><span data-stu-id="23fa2-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="23fa2-109">클라우드 또는 온-프레미스로 실행될 수 있는 이식 가능하고 문제를 스스로 해결할 수 있는 컨테이너로서 앱 배포를 자동화하기 위한 오픈 소스 프로젝트인 Docker에 대해 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="23fa2-110">Docker 용어</span><span class="sxs-lookup"><span data-stu-id="23fa2-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="23fa2-111">Docker 기술에 대한 용어 및 정의를 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="23fa2-112">Docker 컨테이너, 이미지 및 레지스트리</span><span class="sxs-lookup"><span data-stu-id="23fa2-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="23fa2-113">여러 환경 간의 일관된 배포를 위해 Docker 컨테이너 이미지가 이미지 레지스트리에 저장되는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="23fa2-114"><xref:host-and-deploy/docker/building-net-docker-images> ASP.NET Core 앱을 빌드하고 Docker화하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="23fa2-115">Microsoft에서 관리하는 Docker 이미지를 살펴보고 사용 사례를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="23fa2-116">Visual Studio 컨테이너 도구</span><span class="sxs-lookup"><span data-stu-id="23fa2-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="23fa2-117">Visual Studio에서 Windows용 Docker에 대해 .NET Framework 또는 .NET Core를 대상으로 하는 ASP.NET Core 앱의 빌드, 디버깅 및 실행을 지원하는 방법을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="23fa2-118">Windows 및 Linux 컨테이너가 모두 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="23fa2-119">Azure Container Registry에 게시</span><span class="sxs-lookup"><span data-stu-id="23fa2-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="23fa2-120">PowerShell을 사용하여 Azure의 Docker 호스트에 ASP.NET Core 앱을 배포하기 위해 Visual Studio 컨테이너 도구 확장을 사용하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="23fa2-121">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="23fa2-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="23fa2-122">프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="23fa2-123">프록시를 통해 요청을 전달하면 체계 및 클라이언트 IP와 같은 원래 요청에 대한 정보를 모호하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="23fa2-124">앱에 대한 수동 요청에 대한 정보를 전달해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="23fa2-125">[Docker 및 소형 컨테이너를 사용하는 GC](xref:performance/memory#sc) 소형 컨테이너를 사용하여 GC 선택을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="23fa2-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>