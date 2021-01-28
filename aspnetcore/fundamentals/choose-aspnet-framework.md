---
title: ASP.NET 4.x와 ASP.NET Core 중에서 선택
author: rick-anderson
description: ASP.NET Core 및 ASP.NET 4.x에 대해 설명하고 둘 중 선택하는 방법을 설명합니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 192784b4f2cb3b80511814de4f777c4a49fc594b
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710739"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="b18df-103">ASP.NET 4.x와 ASP.NET Core 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="b18df-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="b18df-104">ASP.NET Core는 ASP.NET 4.x를 새롭게 디자인한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b18df-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="b18df-105">이 문서에 차이점이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b18df-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="b18df-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b18df-106">ASP.NET Core</span></span>

<span data-ttu-id="b18df-107">ASP.NET Core는 Windows, macOS 또는 Linux에서 클라우드 기반 최신 웹앱을 빌드하기 위한 오픈 소스 플랫폼 간 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="b18df-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="b18df-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="b18df-108">ASP.NET 4.x</span></span>

<span data-ttu-id="b18df-109">ASP.NET 4.x는 Windows에서 엔터프라이즈급 서버 기반 웹앱을 빌드할 때 필요한 서비스를 제공하는 완성도 있는 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="b18df-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="b18df-110">프레임워크 선택 영역</span><span class="sxs-lookup"><span data-stu-id="b18df-110">Framework selection</span></span>

<span data-ttu-id="b18df-111">다음 표는 ASP.NET Core를 ASP.NET 4.x와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="b18df-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="b18df-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b18df-112">ASP.NET Core</span></span> | <span data-ttu-id="b18df-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="b18df-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="b18df-114">Windows, macOS 또는 Linux용 빌드</span><span class="sxs-lookup"><span data-stu-id="b18df-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="b18df-115">Windows용 빌드</span><span class="sxs-lookup"><span data-stu-id="b18df-115">Build for Windows</span></span>|
|<span data-ttu-id="b18df-116">[Razor Pages](xref:razor-pages/index)는 ASP.NET Core 2.x에서 웹 UI를 만드는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="b18df-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="b18df-117">[MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api) 및 [SignalR](xref:signalr/introduction)도 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b18df-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="b18df-118">[Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [웹후크](/aspnet/webhooks/) 또는 [웹 페이지](/aspnet/web-pages) 사용</span><span class="sxs-lookup"><span data-stu-id="b18df-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="b18df-119">컴퓨터당 여러 버전</span><span class="sxs-lookup"><span data-stu-id="b18df-119">Multiple versions per machine</span></span>|<span data-ttu-id="b18df-120">컴퓨터당 하나의 버전</span><span class="sxs-lookup"><span data-stu-id="b18df-120">One version per machine</span></span>|
|<span data-ttu-id="b18df-121">C# 또는 F#을 사용하여 [Visual Studio](https://visualstudio.microsoft.com/vs/), [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/), 또는 [Visual Studio Code](https://code.visualstudio.com/)에서 개방</span><span class="sxs-lookup"><span data-stu-id="b18df-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="b18df-122">C#, VB 또는 F#을 사용하여 [Visual Studio](https://visualstudio.microsoft.com/vs/)에서 개발</span><span class="sxs-lookup"><span data-stu-id="b18df-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="b18df-123">ASP.NET 4.x보다 고성능</span><span class="sxs-lookup"><span data-stu-id="b18df-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="b18df-124">성능 양호</span><span class="sxs-lookup"><span data-stu-id="b18df-124">Good performance</span></span>|
|[<span data-ttu-id="b18df-125">Use .NET Core 런타임</span><span class="sxs-lookup"><span data-stu-id="b18df-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="b18df-126">.NET Framework 런타임 사용</span><span class="sxs-lookup"><span data-stu-id="b18df-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="b18df-127">.NET Framework의 ASP.NET Core 2.x 지원에 대한 자세한 내용은 [ASP.NET Core 대상 .NET Framework](xref:index#target-framework)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b18df-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="b18df-128">ASP.NET Core 시나리오</span><span class="sxs-lookup"><span data-stu-id="b18df-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="b18df-129">웹 사이트</span><span class="sxs-lookup"><span data-stu-id="b18df-129">Websites</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="b18df-130">API</span><span class="sxs-lookup"><span data-stu-id="b18df-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="b18df-131">실시간</span><span class="sxs-lookup"><span data-stu-id="b18df-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="b18df-132">Azure에 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="b18df-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="b18df-133">ASP.NET 4.x 시나리오</span><span class="sxs-lookup"><span data-stu-id="b18df-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="b18df-134">웹 사이트</span><span class="sxs-lookup"><span data-stu-id="b18df-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="b18df-135">API</span><span class="sxs-lookup"><span data-stu-id="b18df-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="b18df-136">실시간</span><span class="sxs-lookup"><span data-stu-id="b18df-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="b18df-137">Azure에서 ASP.NET 4.x 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b18df-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="b18df-138">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b18df-138">Additional resources</span></span>

* [<span data-ttu-id="b18df-139">ASP.NET 소개</span><span class="sxs-lookup"><span data-stu-id="b18df-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="b18df-140">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="b18df-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
