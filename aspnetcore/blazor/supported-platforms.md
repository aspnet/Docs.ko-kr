---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280722"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="44f13-103">ASP.NET Core Blazor 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="44f13-103">ASP.NET Core Blazor supported platforms</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="44f13-104">Blazor WebAssembly 및 Blazor Server는 다음 표에 나와 있는 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-104">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="44f13-105">브라우저</span><span class="sxs-lookup"><span data-stu-id="44f13-105">Browser</span></span>                          | <span data-ttu-id="44f13-106">버전</span><span class="sxs-lookup"><span data-stu-id="44f13-106">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="44f13-107">Apple Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="44f13-107">Apple Safari, including iOS</span></span>      | <span data-ttu-id="44f13-108">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-108">Current&dagger;</span></span> |
| <span data-ttu-id="44f13-109">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="44f13-109">Google Chrome, including Android</span></span> | <span data-ttu-id="44f13-110">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-110">Current&dagger;</span></span> |
| <span data-ttu-id="44f13-111">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44f13-111">Microsoft Edge</span></span>                   | <span data-ttu-id="44f13-112">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-112">Current&dagger;</span></span> |
| <span data-ttu-id="44f13-113">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44f13-113">Mozilla Firefox</span></span>                  | <span data-ttu-id="44f13-114">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-114">Current&dagger;</span></span> |  

<span data-ttu-id="44f13-115">&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-115">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="44f13-116">브라우저</span><span class="sxs-lookup"><span data-stu-id="44f13-116">Browser</span></span>                          | <span data-ttu-id="44f13-117">버전</span><span class="sxs-lookup"><span data-stu-id="44f13-117">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="44f13-118">Apple Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="44f13-118">Apple Safari, including iOS</span></span>      | <span data-ttu-id="44f13-119">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-119">Current&dagger;</span></span>       |
| <span data-ttu-id="44f13-120">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="44f13-120">Google Chrome, including Android</span></span> | <span data-ttu-id="44f13-121">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-121">Current&dagger;</span></span>       |
| <span data-ttu-id="44f13-122">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44f13-122">Microsoft Edge</span></span>                   | <span data-ttu-id="44f13-123">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-123">Current&dagger;</span></span>       |
| <span data-ttu-id="44f13-124">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44f13-124">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="44f13-125">지원되지 않음&Dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-125">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="44f13-126">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44f13-126">Mozilla Firefox</span></span>                  | <span data-ttu-id="44f13-127">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-127">Current&dagger;</span></span>       |  

<span data-ttu-id="44f13-128">&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-128">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="44f13-129">&Dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-129">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="44f13-130">브라우저</span><span class="sxs-lookup"><span data-stu-id="44f13-130">Browser</span></span>                          | <span data-ttu-id="44f13-131">버전</span><span class="sxs-lookup"><span data-stu-id="44f13-131">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="44f13-132">Apple Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="44f13-132">Apple Safari, including iOS</span></span>      | <span data-ttu-id="44f13-133">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-133">Current&dagger;</span></span> |
| <span data-ttu-id="44f13-134">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="44f13-134">Google Chrome, including Android</span></span> | <span data-ttu-id="44f13-135">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-135">Current&dagger;</span></span> |
| <span data-ttu-id="44f13-136">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44f13-136">Microsoft Edge</span></span>                   | <span data-ttu-id="44f13-137">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-137">Current&dagger;</span></span> |
| <span data-ttu-id="44f13-138">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44f13-138">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="44f13-139">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-139">11&Dagger;</span></span>      |
| <span data-ttu-id="44f13-140">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44f13-140">Mozilla Firefox</span></span>                  | <span data-ttu-id="44f13-141">현재&dagger;</span><span class="sxs-lookup"><span data-stu-id="44f13-141">Current&dagger;</span></span> |

<span data-ttu-id="44f13-142">&dagger;‘현재’는 최신 버전의 브라우저를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-142">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="44f13-143">&Dagger;추가 보충 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-143">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="44f13-144">예를 들어 프라미스는 [`Polyfill.io`](https://polyfill.io/v3/) 번들을 통해 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44f13-144">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="44f13-145">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="44f13-145">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
