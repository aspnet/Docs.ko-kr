---
title: ASP.NET Core 및 Azure에서 DevOps
author: CamSoper
description: Azure에서 호스팅되는 ASP.NET Core 앱에 대한 DevOps 파이프라인을 빌드하는 방법에 대한 엔드투엔드 지침을 제공하는 가이드입니다.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: 10a6bae73743d3063ad2e1ab49fc418ad1d63756
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052294"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="0a377-103">ASP.NET Core 및 Azure에서 DevOps</span><span class="sxs-lookup"><span data-stu-id="0a377-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="0a377-104">[![표지 이미지](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="0a377-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="0a377-105">작성자: [Cam Soper](https://twitter.com/camsoper) 및 [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0a377-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="0a377-106">이 가이드는 [다운로드 가능한 PDF eBook](https://aka.ms/devopsbook)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="0a377-107">환영</span><span class="sxs-lookup"><span data-stu-id="0a377-107">Welcome</span></span> 

<span data-ttu-id="0a377-108">.NET용 Azure 개발 수명 주기 가이드를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="0a377-109">이 가이드에서는 .NET 도구 및 프로세스를 사용하여 Azure 관련 개발 수명 주기를 빌드하는 기본 개념을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="0a377-110">이 가이드를 완료하면 성숙한 DevOps 도구 체인의 혜택을 누릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="0a377-111">이 가이드의 대상</span><span class="sxs-lookup"><span data-stu-id="0a377-111">Who this guide is for</span></span>

<span data-ttu-id="0a377-112">ASP.NET Core에 익숙한 개발자여야 합니다(200~300레벨).</span><span class="sxs-lookup"><span data-stu-id="0a377-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="0a377-113">이 소개에서 설명할 것처럼 Azure에 대한 지식이 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="0a377-114">이 가이드는 개발보다 작업에 더 집중하는 DevOps 엔지니어의 경우에 유용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="0a377-115">이 가이드는 Windows 개발자를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-115">This guide targets Windows developers.</span></span> <span data-ttu-id="0a377-116">그러나 Linux 및 macOS는 .NET Core에서 완전히 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="0a377-117">Linux/macOS에 이 가이드를 적용하려면 Linux/macOS 차이점에 대한 설명선에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="0a377-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="0a377-118">이 가이드에서 다루지 않는 내용</span><span class="sxs-lookup"><span data-stu-id="0a377-118">What this guide doesn't cover</span></span>

<span data-ttu-id="0a377-119">이 가이드는 .NET 개발자를 위한 엔드투엔드 연속 배포 환경에 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="0a377-120">Azure의 모든 항목에 대한 완전한 가이드가 아니며 Azure 서비스에 대한 .NET API에만 광범위하게 집중하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="0a377-121">지속적인 통합, 배포, 모니터링 및 디버깅과 관련된 모든 항목을 강조합니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="0a377-122">이 가이드의 끝에서 다음 단계에 대한 권장 사항이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="0a377-123">ASP.NET Core 개발자에게 유용한 Azure 플랫폼 서비스가 제안에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="0a377-124">설명서의 내용</span><span class="sxs-lookup"><span data-stu-id="0a377-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="0a377-125">도구 및 다운로드</span><span class="sxs-lookup"><span data-stu-id="0a377-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="0a377-126">이 가이드에 사용되는 도구를 얻을 수 있는 위치에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="0a377-127">App Service에 배포</span><span class="sxs-lookup"><span data-stu-id="0a377-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="0a377-128">Azure App Service에 ASP.NET Core 앱을 배포하는 다양한 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="0a377-129">연속 통합 및 배포</span><span class="sxs-lookup"><span data-stu-id="0a377-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="0a377-130">GitHub, Azure DevOps Services, Azure를 사용하여 엔드투엔드 연속 통합 및 ASP.NET Core 앱에 대한 배포 솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="0a377-131">모니터링 및 디버그</span><span class="sxs-lookup"><span data-stu-id="0a377-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="0a377-132">Azure 도구를 사용하여 애플리케이션을 모니터링하고, 문제를 해결하고, 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="0a377-133">다음 단계</span><span class="sxs-lookup"><span data-stu-id="0a377-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="0a377-134">Azure를 학습하는 ASP.NET Core 개발자를 위한 다른 학습 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="0a377-135">추가 소개 읽기</span><span class="sxs-lookup"><span data-stu-id="0a377-135">Additional introductory reading</span></span>

<span data-ttu-id="0a377-136">클라우드 컴퓨팅을 처음 접하는 경우 다음 문서를 통해 기본 사항을 알아볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a377-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="0a377-137">클라우드 컴퓨팅이란?</span><span class="sxs-lookup"><span data-stu-id="0a377-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="0a377-138">클라우드 컴퓨팅의 예제</span><span class="sxs-lookup"><span data-stu-id="0a377-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="0a377-139">IaaS란?</span><span class="sxs-lookup"><span data-stu-id="0a377-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="0a377-140">란?</span><span class="sxs-lookup"><span data-stu-id="0a377-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
