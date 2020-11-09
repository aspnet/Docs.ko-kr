---
title: ASP.NET Core ObjectPool에서 개체 다시 사용
author: rick-anderson
description: ObjectPool을 사용 하 여 ASP.NET Core 앱의 성능을 향상 시키기 위한 팁입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/ObjectPool
ms.openlocfilehash: 3bbfde6f68a238131149e67ce72f0ddc68a9ea0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056909"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="d5dcf-103">ASP.NET Core ObjectPool에서 개체 다시 사용</span><span class="sxs-lookup"><span data-stu-id="d5dcf-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="d5dcf-104">작성자, [Steve Gordon](https://twitter.com/stevejgordon), [Ryan nowak](https://github.com/rynowak)및 [Günther oidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="d5dcf-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="d5dcf-105"><xref:Microsoft.Extensions.ObjectPool> 는 개체의 가비지 수집을 허용 하는 대신 다시 사용 하기 위해 메모리의 개체 그룹을 유지 하는 것을 지 원하는 ASP.NET Core 인프라의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="d5dcf-106">관리 중인 개체가 다음과 같은 경우 개체 풀을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="d5dcf-107">할당/초기화 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="d5dcf-108">제한 된 리소스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-108">Represent some limited resource.</span></span>
- <span data-ttu-id="d5dcf-109">예측 가능 하 고 자주 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-109">Used predictably and frequently.</span></span>

<span data-ttu-id="d5dcf-110">예를 들어 ASP.NET Core 프레임 워크는 일부 위치에서 개체 풀을 사용 하 여 인스턴스를 다시 사용 합니다 <xref:System.Text.StringBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d5dcf-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="d5dcf-111">`StringBuilder` 문자 데이터를 저장 하기 위해 자체 버퍼를 할당 하 고 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="d5dcf-112">정기적으로를 사용 하 여 `StringBuilder` 기능을 구현 하 고 재사용 하면 성능상의 이점을 얻을 수 있습니다. ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5dcf-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="d5dcf-113">개체 풀링은 항상 성능을 향상 시 키 지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="d5dcf-114">개체의 초기화 비용이 높을 때를 제외 하 고는 일반적으로 풀에서 개체를 가져오는 속도가 느립니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="d5dcf-115">풀에서 관리 하는 개체는 풀이 할당 취소 될 때까지 할당 취소 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="d5dcf-116">응용 프로그램 또는 라이브러리에 대해 현실적인 시나리오를 사용 하 여 성능 데이터를 수집한 후에만 개체 풀링을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="d5dcf-117">**경고:가를 `ObjectPool` 구현 하지 않습니다 `IDisposable` . 삭제 해야 하는 형식으로는 사용 하지 않는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="d5dcf-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="d5dcf-118">`ObjectPool` ASP.NET Core 3.0 이상에서는을 지원 `IDisposable` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="d5dcf-119">**참고: ObjectPool은 할당할 개체 수에 대 한 제한을 두지 않습니다. 그러면 개체 수에 대 한 제한이 유지 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="d5dcf-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="d5dcf-120">개념</span><span class="sxs-lookup"><span data-stu-id="d5dcf-120">Concepts</span></span>

<span data-ttu-id="d5dcf-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -기본 개체 풀 추상화.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="d5dcf-122">개체를 가져오고 반환 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-122">Used to get and return objects.</span></span>

<span data-ttu-id="d5dcf-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -개체가 만들어지는 방법과 풀로 반환 될 때 *다시 설정* 되는 방법을 사용자 지정 하려면이 메서드를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="d5dcf-124">직접 생성 하는 개체 풀에 전달할 수 있습니다. 디스크나</span><span class="sxs-lookup"><span data-stu-id="d5dcf-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="d5dcf-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 개체 풀을 만들기 위한 팩터리 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="d5dcf-126">ObjectPool은 여러 가지 방법으로 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="d5dcf-127">풀 인스턴스화.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-127">Instantiating a pool.</span></span>
* <span data-ttu-id="d5dcf-128">DI ( [종속성 주입](xref:fundamentals/dependency-injection) )의 풀을 인스턴스로 등록 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="d5dcf-129">`ObjectPoolProvider<>`DI에를 등록 하 고 팩터리로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="d5dcf-130">ObjectPool 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d5dcf-130">How to use ObjectPool</span></span>

<span data-ttu-id="d5dcf-131"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*>를 호출 하 여 개체를 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 가져오고 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="d5dcf-132">모든 개체를 반환 하는 요구 사항은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="d5dcf-133">개체를 반환 하지 않으면 가비지 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="d5dcf-134"><xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider>를 사용 하는 경우 `T` 다음을 구현 합니다 `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="d5dcf-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="d5dcf-135">풀로 반환 되는 \*가 **아닌** 항목은 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-135">Items that are \* **not** _ returned to the pool will be disposed.</span></span>
<span data-ttu-id="d5dcf-136">_ DI에서 풀을 삭제 하면 풀의 모든 항목이 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-136">_ When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="d5dcf-137">참고: 풀이 삭제 된 후:</span><span class="sxs-lookup"><span data-stu-id="d5dcf-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="d5dcf-138">`Get`를 호출 하면이 throw `ObjectDisposedException` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="d5dcf-139">`return` 지정 된 항목을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="d5dcf-140">ObjectPool 샘플</span><span class="sxs-lookup"><span data-stu-id="d5dcf-140">ObjectPool sample</span></span>

<span data-ttu-id="d5dcf-141">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-141">The following code:</span></span>

* <span data-ttu-id="d5dcf-142">`ObjectPoolProvider`DI ( [종속성 주입](xref:fundamentals/dependency-injection) ) 컨테이너에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="d5dcf-143">`ObjectPool<StringBuilder>`DI 컨테이너에를 추가 하 고 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="d5dcf-144">를 추가 `BirthdayMiddleware` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d5dcf-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="d5dcf-145">다음 코드에서는을 구현 합니다. `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="d5dcf-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
