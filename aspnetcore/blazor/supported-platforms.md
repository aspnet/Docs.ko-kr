---
title: 'ASP.NET Core Blazor 지원 플랫폼'
author: guardrex
description: 'ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054959"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="177e1-103">ASP.NET Core Blazor 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="177e1-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="177e1-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="177e1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="177e1-105">Blazor WebAssembly 및 Blazor Server는 다음 표에 나와 있는 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="177e1-106">브라우저</span><span class="sxs-lookup"><span data-stu-id="177e1-106">Browser</span></span>                          | <span data-ttu-id="177e1-107">버전</span><span class="sxs-lookup"><span data-stu-id="177e1-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="177e1-108">Apple Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="177e1-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="177e1-109">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-109">Current&dagger;</span></span> |
| <span data-ttu-id="177e1-110">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="177e1-110">Google Chrome, including Android</span></span> | <span data-ttu-id="177e1-111">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-111">Current&dagger;</span></span> |
| <span data-ttu-id="177e1-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="177e1-112">Microsoft Edge</span></span>                   | <span data-ttu-id="177e1-113">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-113">Current&dagger;</span></span> |
| <span data-ttu-id="177e1-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="177e1-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="177e1-115">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-115">Current&dagger;</span></span> |  

<span data-ttu-id="177e1-116">&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="177e1-117">브라우저</span><span class="sxs-lookup"><span data-stu-id="177e1-117">Browser</span></span>                          | <span data-ttu-id="177e1-118">버전</span><span class="sxs-lookup"><span data-stu-id="177e1-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="177e1-119">Apple Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="177e1-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="177e1-120">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-120">Current&dagger;</span></span>       |
| <span data-ttu-id="177e1-121">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="177e1-121">Google Chrome, including Android</span></span> | <span data-ttu-id="177e1-122">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-122">Current&dagger;</span></span>       |
| <span data-ttu-id="177e1-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="177e1-123">Microsoft Edge</span></span>                   | <span data-ttu-id="177e1-124">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-124">Current&dagger;</span></span>       |
| <span data-ttu-id="177e1-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="177e1-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="177e1-126">지원되지 않음&Dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="177e1-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="177e1-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="177e1-128">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-128">Current&dagger;</span></span>       |  

<span data-ttu-id="177e1-129">&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="177e1-130">&Dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="177e1-131">브라우저</span><span class="sxs-lookup"><span data-stu-id="177e1-131">Browser</span></span>                          | <span data-ttu-id="177e1-132">버전</span><span class="sxs-lookup"><span data-stu-id="177e1-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="177e1-133">Apple Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="177e1-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="177e1-134">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-134">Current&dagger;</span></span> |
| <span data-ttu-id="177e1-135">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="177e1-135">Google Chrome, including Android</span></span> | <span data-ttu-id="177e1-136">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-136">Current&dagger;</span></span> |
| <span data-ttu-id="177e1-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="177e1-137">Microsoft Edge</span></span>                   | <span data-ttu-id="177e1-138">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-138">Current&dagger;</span></span> |
| <span data-ttu-id="177e1-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="177e1-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="177e1-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-140">11&Dagger;</span></span>      |
| <span data-ttu-id="177e1-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="177e1-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="177e1-142">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="177e1-142">Current&dagger;</span></span> |

<span data-ttu-id="177e1-143">&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="177e1-144">&Dagger;추가 보충 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="177e1-145">예를 들어 프라미스는 [`Polyfill.io`](https://polyfill.io/v3/) 번들을 통해 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="177e1-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="177e1-146">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="177e1-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
