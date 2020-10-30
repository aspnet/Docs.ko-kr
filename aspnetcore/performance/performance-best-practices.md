---
title: ASP.NET Core 성능 모범 사례
author: mjrousos
description: ASP.NET Core 앱의 성능을 향상 하 고 일반적인 성능 문제를 방지 하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: a3fc398569fafefc0b4634e80433a5d4e0e1b4ff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061004"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="d7b6a-103">ASP.NET Core 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="d7b6a-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="d7b6a-104">[Mike Rousos](https://github.com/mjrousos) 작성</span><span class="sxs-lookup"><span data-stu-id="d7b6a-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="d7b6a-105">이 문서에서는 ASP.NET Core의 성능 모범 사례에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="d7b6a-106">적극적으로 캐시</span><span class="sxs-lookup"><span data-stu-id="d7b6a-106">Cache aggressively</span></span>

<span data-ttu-id="d7b6a-107">캐싱은이 문서의 여러 부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="d7b6a-108">자세한 내용은 <xref:performance/caching/response>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="d7b6a-109">핫 코드 경로 이해</span><span class="sxs-lookup"><span data-stu-id="d7b6a-109">Understand hot code paths</span></span>

<span data-ttu-id="d7b6a-110">이 문서에서 *핫 코드 경로* 는 자주 호출 되는 코드 경로로 정의 되며 대부분의 실행 시간이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="d7b6a-111">핫 코드 경로는 일반적으로 앱 확장 및 성능을 제한 하며이 문서의 여러 부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="d7b6a-112">호출 차단 방지</span><span class="sxs-lookup"><span data-stu-id="d7b6a-112">Avoid blocking calls</span></span>

<span data-ttu-id="d7b6a-113">ASP.NET Core 앱은 여러 요청을 동시에 처리 하도록 설계 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="d7b6a-114">비동기 Api를 사용 하면 작은 스레드 풀에서 차단 호출을 기다리지 않고 수천 개의 동시 요청을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="d7b6a-115">장기 실행 동기 작업이 완료 되기를 기다리지 않고 스레드가 다른 요청에서 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="d7b6a-116">ASP.NET Core 앱의 일반적인 성능 문제는 비동기 일 수 있는 호출을 차단 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="d7b6a-117">많은 동기 차단 호출이 [스레드 풀 고갈](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) 및 저하 된 응답 시간을 초래 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-117">Many synchronous blocking calls lead to [Thread Pool starvation](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) and degraded response times.</span></span>

<span data-ttu-id="d7b6a-118">안 **함** :</span><span class="sxs-lookup"><span data-stu-id="d7b6a-118">**Do not** :</span></span>

* <span data-ttu-id="d7b6a-119">[작업. 대기](/dotnet/api/system.threading.tasks.task.wait) 또는 [작업](/dotnet/api/system.threading.tasks.task-1.result)을 호출 하 여 비동기 실행을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="d7b6a-120">공용 코드 경로에서 잠금을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="d7b6a-121">ASP.NET Core 앱은 코드를 병렬로 실행 하도록 설계 될 때 가장 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="d7b6a-122">작업을 호출 [합니다 .를 실행](/dotnet/api/system.threading.tasks.task.run) 하 고 즉시 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="d7b6a-123">ASP.NET Core는 정상적인 스레드 풀 스레드에서 이미 앱 코드를 실행 하 고 있으므로 작업을 호출 하면 추가 불필요 한 스레드 풀 예약이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="d7b6a-124">예약 된 코드에서 스레드를 차단 하는 경우에도 작업. 실행은이를 방지 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="d7b6a-125">**Do** :</span><span class="sxs-lookup"><span data-stu-id="d7b6a-125">**Do** :</span></span>

* <span data-ttu-id="d7b6a-126">[핫 코드 경로](#understand-hot-code-paths) 를 비동기식으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="d7b6a-127">비동기 API를 사용할 수 있는 경우 데이터 액세스, i/o 및 장기 실행 작업 Api를 비동기적으로 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-127">Call data access, I/O, and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="d7b6a-128">작업을 실행 **하 여 동기** API를 비동기식으로 만듭니다 [.](/dotnet/api/system.threading.tasks.task.run)</span><span class="sxs-lookup"><span data-stu-id="d7b6a-128">Do **not** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronous API asynchronous.</span></span>
* <span data-ttu-id="d7b6a-129">컨트롤러/ :::no-loc(Razor)::: 페이지 작업을 비동기식으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-129">Make controller/:::no-loc(Razor)::: Page actions asynchronous.</span></span> <span data-ttu-id="d7b6a-130">비동기 [/](/dotnet/csharp/programming-guide/concepts/async/) 대기 패턴을 활용 하기 위해 전체 호출 스택은 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="d7b6a-131">[Perfview](https://github.com/Microsoft/perfview)와 같은 프로파일러를 사용 하 여 [스레드 풀](/windows/desktop/procthread/thread-pools)에 자주 추가 되는 스레드를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="d7b6a-132">이벤트는 스레드 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 풀에 추가 된 스레드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="d7b6a-133">IEnumerable \<T> 또는 IAsyncEnumerable을 반환 합니다.\<T></span><span class="sxs-lookup"><span data-stu-id="d7b6a-133">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="d7b6a-134">작업에서 반환 하면 serializer에의 `IEnumerable<T>` 한 동기 컬렉션 반복이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-134">Returning `IEnumerable<T>` from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="d7b6a-135">그 결과는 호출 차단이며 스레드 풀 고갈의 가능성도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-135">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="d7b6a-136">동기 열거를 방지 하려면 `ToListAsync` 열거 가능를 반환 하기 전에를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-136">To avoid synchronous enumeration, use `ToListAsync` before returning the enumerable.</span></span>

<span data-ttu-id="d7b6a-137">ASP.NET Core 3.0부터는를 `IAsyncEnumerable<T>` 비동기적으로 열거 하는 대신 사용할 수 있습니다 `IEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-137">Beginning with ASP.NET Core 3.0, `IAsyncEnumerable<T>` can be used as an alternative to `IEnumerable<T>` that enumerates asynchronously.</span></span> <span data-ttu-id="d7b6a-138">자세한 내용은 [컨트롤러 작업 반환 형식](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-138">For more information, see [Controller action return types](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).</span></span>

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="d7b6a-139">대량 개체 할당 최소화</span><span class="sxs-lookup"><span data-stu-id="d7b6a-139">Minimize large object allocations</span></span>

<span data-ttu-id="d7b6a-140">[.Net Core 가비지 수집기](/dotnet/standard/garbage-collection/) 는 ASP.NET Core 앱에서 메모리의 할당 및 해제를 자동으로 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-140">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="d7b6a-141">자동 가비지 수집은 일반적으로 개발자가 메모리 해제 방법 또는 시기에 대해 걱정할 필요가 없음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-141">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="d7b6a-142">그러나 참조 되지 않은 개체를 정리 하는 경우 CPU 시간이 걸리므로 개발자는 [핫 코드 경로](#understand-hot-code-paths)에서 개체 할당을 최소화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-142">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="d7b6a-143">가비지 수집은 큰 개체 (> 85 K 바이트)에서 특히 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-143">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="d7b6a-144">Large 개체는 [large object 힙에](/dotnet/standard/garbage-collection/large-object-heap) 저장 되며 정리 하려면 전체 (2 세대) 가비지 수집이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-144">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="d7b6a-145">0 세대 및 1 세대 컬렉션과 달리 2 세대 수집에는 앱 실행을 임시로 일시 중단 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-145">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="d7b6a-146">자주 큰 개체를 할당 및 할당 취소 하면 성능이 저하 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-146">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="d7b6a-147">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-147">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-148">자주 사용 되는 많은 개체를 캐싱하는 **것이 좋습니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-148">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="d7b6a-149">큰 개체를 캐시 하면 비용이 많이 드는 할당이 방지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-149">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="d7b6a-150">[ArrayPool \<T>](/dotnet/api/system.buffers.arraypool-1) 를 사용 하 여 대량 배열을 저장 함으로써 풀 버퍼를 **수행** 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-150">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="d7b6a-151">[핫 코드 경로](#understand-hot-code-paths)에 단기 수명이 많은 개체를 할당 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-151">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="d7b6a-152">[Perfview](https://github.com/Microsoft/perfview) 및 검사에서 GC (가비지 수집) 통계를 검토 하 여 위와 같은 메모리 문제를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-152">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="d7b6a-153">가비지 수집 일시 중지 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-153">Garbage collection pause time.</span></span>
* <span data-ttu-id="d7b6a-154">가비지 수집에 소요 되는 프로세서 시간의 비율입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-154">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="d7b6a-155">0 세대, 1, 2 세대의 가비지 컬렉션 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-155">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="d7b6a-156">자세한 내용은 [가비지 수집 및 성능](/dotnet/standard/garbage-collection/performance)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-156">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access-and-io"></a><span data-ttu-id="d7b6a-157">데이터 액세스 및 i/o 최적화</span><span class="sxs-lookup"><span data-stu-id="d7b6a-157">Optimize data access and I/O</span></span>

<span data-ttu-id="d7b6a-158">데이터 저장소 및 다른 원격 서비스와의 상호 작용은 종종 ASP.NET Core 앱의 가장 느린 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-158">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="d7b6a-159">데이터를 효율적으로 읽고 쓰는 것이 좋은 성능을 위해 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-159">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="d7b6a-160">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-160">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-161">모든 데이터 액세스 Api를 비동기적 **으로 호출 합니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-161">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="d7b6a-162">필요한 것 보다 더 많은 데이터를 검색 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-162">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="d7b6a-163">현재 HTTP 요청에 필요한 데이터만 반환 하는 쿼리를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-163">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="d7b6a-164">약간 오래 된 데이터를 사용할 수 있는 경우 데이터베이스 또는 원격 서비스에서 검색 되는 자주 액세스 하는 데이터를 캐시 하 **는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="d7b6a-164">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="d7b6a-165">시나리오에 따라 [Memorycache](xref:performance/caching/memory) 또는 [microsoft.web.distributedcache](xref:performance/caching/distributed)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-165">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="d7b6a-166">자세한 내용은 <xref:performance/caching/response>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-166">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="d7b6a-167">네트워크 왕복 **을 최소화 합니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-167">**Do** minimize network round trips.</span></span> <span data-ttu-id="d7b6a-168">목표는 여러 호출이 아닌 단일 호출에서 필요한 데이터를 검색 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-168">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="d7b6a-169">읽기 전용 용도로 데이터에 액세스할 때 Entity Framework Core에서 [추적 안 함 쿼리](/ef/core/querying/tracking#no-tracking-queries) **를 사용 합니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-169">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="d7b6a-170">EF Core 추적 되지 않는 쿼리 결과를 보다 효율적으로 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-170">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="d7b6a-171">LINQ 쿼리 (예:, 또는 문 포함) **를 필터링 하** 고 집계 `.Where` 하 여 `.Select` `.Sum` 데이터베이스에서 필터링을 수행 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-171">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="d7b6a-172">EF Core에서 클라이언트의 일부 쿼리 연산자를 확인 하므로 비효율적인 쿼리 **실행이 발생할** 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-172">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="d7b6a-173">자세한 내용은 [클라이언트 평가 성능 문제](/ef/core/querying/client-eval#client-evaluation-performance-issues)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-173">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="d7b6a-174">컬렉션에 대해 프로젝션 쿼리를 사용 **하지 마십시오. 이렇게** 하면 "N + 1" SQL 쿼리가 실행 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-174">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="d7b6a-175">자세한 내용은 [상관 하위 쿼리 최적화](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-175">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="d7b6a-176">대규모 앱의 성능을 향상 시킬 수 있는 방법에 대해서는 [EF High 성능](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-176">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="d7b6a-177">DbContext 풀링</span><span class="sxs-lookup"><span data-stu-id="d7b6a-177">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="d7b6a-178">명시적으로 컴파일된 쿼리</span><span class="sxs-lookup"><span data-stu-id="d7b6a-178">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="d7b6a-179">코드 베이스를 커밋하기 전에 이전 고성능 방법의 영향을 측정 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-179">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="d7b6a-180">컴파일된 쿼리의 추가적인 복잡성은 성능 향상을 정당화 하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-180">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="d7b6a-181">[Application Insights](/azure/application-insights/app-insights-overview) 또는 프로 파일링 도구를 사용 하 여 데이터에 액세스 하는 데 걸린 시간을 검토 하 여 쿼리 문제를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-181">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="d7b6a-182">또한 대부분의 데이터베이스는 자주 실행 되는 쿼리와 관련 하 여 통계를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-182">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="d7b6a-183">HttpClientFactory를 사용 하 여 HTTP 연결 풀</span><span class="sxs-lookup"><span data-stu-id="d7b6a-183">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="d7b6a-184">[Httpclient](/dotnet/api/system.net.http.httpclient) 는 인터페이스를 구현 하지만 `IDisposable` 다시 사용 하도록 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-184">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="d7b6a-185">닫힌 `HttpClient` 인스턴스 `TIME_WAIT` 는 짧은 시간 동안 소켓을 열린 상태로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-185">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="d7b6a-186">개체를 만들고 삭제 하는 코드 경로가 `HttpClient` 자주 사용 되는 경우 앱에서 사용 가능한 소켓이 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-186">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="d7b6a-187">[Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 는이 문제에 대 한 솔루션으로 ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-187">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="d7b6a-188">성능 및 안정성을 최적화 하기 위해 풀링 HTTP 연결을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-188">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="d7b6a-189">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-189">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-190">인스턴스를 직접 만들고 삭제 **하지 마십시오** `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-190">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="d7b6a-191">[Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) **를 사용 하 여** 인스턴스를 검색 `HttpClient` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-191">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="d7b6a-192">자세한 내용은 [HttpClientFactory를 사용 하 여 복원 력 있는 HTTP 요청 구현](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-192">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="d7b6a-193">자주 공용 코드 경로 유지</span><span class="sxs-lookup"><span data-stu-id="d7b6a-193">Keep common code paths fast</span></span>

<span data-ttu-id="d7b6a-194">모든 코드를 빠르게 만들려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-194">You want all of your code to be fast.</span></span> <span data-ttu-id="d7b6a-195">자주 호출 되는 코드 경로를 최적화 하는 것이 가장 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-195">Frequently-called code paths are the most critical to optimize.</span></span> <span data-ttu-id="d7b6a-196">여기에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-196">These include:</span></span>

* <span data-ttu-id="d7b6a-197">앱의 요청 처리 파이프라인에 있는 미들웨어 구성 요소, 특히 미들웨어는 파이프라인 초기에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-197">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="d7b6a-198">이러한 구성 요소는 성능에 큰 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-198">These components have a large impact on performance.</span></span>
* <span data-ttu-id="d7b6a-199">요청 마다 또는 요청 마다 여러 번 실행 되는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-199">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="d7b6a-200">예를 들어 사용자 지정 로깅, 권한 부여 처리기 또는 임시 서비스 초기화가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-200">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="d7b6a-201">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-201">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-202">장기 실행 작업에는 사용자 지정 미들웨어 구성 요소를 사용 **하지 마십시오** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-202">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="d7b6a-203">[Visual Studio 진단 도구](/visualstudio/profiling/profiling-feature-tour) 또는 [perfview](https://github.com/Microsoft/perfview))와 같은 성능 프로 파일링 도구 **를 사용 하 여** [핫 코드 경로](#understand-hot-code-paths)를 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-203">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="d7b6a-204">HTTP 요청 외부에서 장기 실행 작업 완료</span><span class="sxs-lookup"><span data-stu-id="d7b6a-204">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="d7b6a-205">ASP.NET Core 앱에 대 한 대부분의 요청은 컨트롤러 또는 페이지 모델에서 필요한 서비스를 호출 하 고 HTTP 응답을 반환 하 여 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-205">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="d7b6a-206">장기 실행 작업을 포함 하는 일부 요청의 경우 전체 요청-응답 프로세스를 비동기식으로 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-206">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="d7b6a-207">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-207">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-208">일반 HTTP 요청 처리의 일부로 장기 실행 작업이 완료 될 때까지 기다리지 **마세요** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-208">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="d7b6a-209">[백그라운드 서비스](xref:fundamentals/host/hosted-services) 를 사용 하 여 장기 실행 요청을 처리 하거나 [Azure 함수](/azure/azure-functions/)를 사용 하 여 out-of-process를 처리 하는 **것이 좋습니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-209">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="d7b6a-210">Out-of-process 작업을 완료 하는 작업은 특히 CPU를 많이 사용 하는 작업에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-210">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="d7b6a-211">와 같은 실시간 통신 옵션 **을 사용** 하 여 [:::no-loc(SignalR):::](xref:signalr/introduction) 비동기적으로 클라이언트와 통신 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-211">**Do** use real-time communication options, such as [:::no-loc(SignalR):::](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="d7b6a-212">클라이언트 자산 축소</span><span class="sxs-lookup"><span data-stu-id="d7b6a-212">Minify client assets</span></span>

<span data-ttu-id="d7b6a-213">복잡 한 프런트 엔드가 있는 ASP.NET Core 앱은 종종 많은 JavaScript, CSS 또는 이미지 파일을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-213">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="d7b6a-214">초기 로드 요청의 성능은 다음과 같은 방법으로 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-214">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="d7b6a-215">번들은 여러 파일을 하나로 결합 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-215">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="d7b6a-216">축소-공백과 주석을 제거 하 여 파일 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-216">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="d7b6a-217">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-217">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-218">번들 및 축소 클라이언트 자산에 대 한 ASP.NET Core [기본 제공 지원을](xref:client-side/bundling-and-minification) **사용 합니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-218">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="d7b6a-219">복합 클라이언트 자산 관리를 위해 다른 타사 도구 (예: [Webpack](https://webpack.js.org/)) **를 고려 합니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-219">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="d7b6a-220">응답 압축</span><span class="sxs-lookup"><span data-stu-id="d7b6a-220">Compress responses</span></span>

 <span data-ttu-id="d7b6a-221">응답 크기를 줄이면 일반적으로 앱의 응답성이 크게 향상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-221">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="d7b6a-222">페이로드 크기를 줄이는 한 가지 방법은 앱의 응답을 압축 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-222">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="d7b6a-223">자세한 내용은 [응답 압축](xref:performance/response-compression)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-223">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="d7b6a-224">최신 ASP.NET Core 릴리스 사용</span><span class="sxs-lookup"><span data-stu-id="d7b6a-224">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="d7b6a-225">ASP.NET Core의 새 릴리스에는 성능 향상이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-225">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="d7b6a-226">.NET Core 및 ASP.NET Core의 최적화는 일반적으로 최신 버전이 이전 버전을 내지만 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-226">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="d7b6a-227">예를 들어 .NET Core 2.1에는 컴파일된 정규식에 대 한 지원과 [범위 \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)에서의 benefitted 추가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-227">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay).</span></span> <span data-ttu-id="d7b6a-228">ASP.NET Core 2.2에는 HTTP/2에 대 한 지원이 추가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-228">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="d7b6a-229">[ASP.NET Core 3.0](xref:aspnetcore-3.0) 은 메모리 사용을 줄이고 처리량을 향상 시키는 많은 향상 된 기능을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-229">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="d7b6a-230">성능이 우선 되는 경우 ASP.NET Core의 현재 버전으로 업그레이드 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-230">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="d7b6a-231">예외 최소화</span><span class="sxs-lookup"><span data-stu-id="d7b6a-231">Minimize exceptions</span></span>

<span data-ttu-id="d7b6a-232">예외는 드물게 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-232">Exceptions should be rare.</span></span> <span data-ttu-id="d7b6a-233">예외 throw 및 catch는 다른 코드 흐름 패턴을 기준으로 속도가 느립니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-233">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="d7b6a-234">따라서 일반적인 프로그램 흐름을 제어 하는 데는 예외를 사용 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-234">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="d7b6a-235">권장 사항:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-235">Recommendations:</span></span>

* <span data-ttu-id="d7b6a-236">특히 [핫 코드 경로](#understand-hot-code-paths)에서 일반적인 프로그램 흐름의 수단으로 throw 또는 catch 예외를 사용 **하지 마십시오** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-236">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="d7b6a-237">응용 프로그램에 논리 **를 포함 하** 여 예외를 발생 시키는 조건을 검색 하 고 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-237">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="d7b6a-238">비정상적인 또는 예기치 않은 조건에 대 한 예외 **를 throw 하거나** catch 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-238">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="d7b6a-239">Application Insights와 같은 앱 진단 도구는 응용 프로그램에서 성능에 영향을 줄 수 있는 일반적인 예외를 식별 하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-239">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="d7b6a-240">성능 및 안정성</span><span class="sxs-lookup"><span data-stu-id="d7b6a-240">Performance and reliability</span></span>

<span data-ttu-id="d7b6a-241">다음 섹션에서는 성능 팁과 알려진 안정성 문제 및 해결 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-241">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="d7b6a-242">HttpRequest/Httpresponse.cache 본문에서 동기 읽기 또는 쓰기 방지</span><span class="sxs-lookup"><span data-stu-id="d7b6a-242">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="d7b6a-243">ASP.NET Core의 모든 i/o는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-243">All I/O in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="d7b6a-244">서버는 `Stream` 동기 및 비동기 오버 로드를 모두 포함 하는 인터페이스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-244">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="d7b6a-245">스레드 풀 스레드를 차단 하지 않도록 비동기를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-245">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="d7b6a-246">스레드를 차단 하면 스레드 풀이 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-246">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="d7b6a-247">**이 작업을 수행 하지 마십시오.** 다음 예제에서는를 사용 합니다 <xref:System.IO.StreamReader.ReadToEnd*> .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-247">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="d7b6a-248">결과를 대기 하는 현재 스레드를 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-248">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="d7b6a-249">이는 [async를 통한 동기화](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-249">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="d7b6a-250">위의 코드에서는 `Get` 전체 HTTP 요청 본문을 메모리에 동기적으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-250">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="d7b6a-251">클라이언트에 느린 업로드가 있으면 앱이 비동기를 통해 동기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-251">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="d7b6a-252">Kestrel은 동기 읽기를 지원 **하지** 않기 때문에 앱은 async를 통해 동기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-252">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="d7b6a-253">**다음 작업을 수행 합니다.** 다음 예제에서는를 사용 하 <xref:System.IO.StreamReader.ReadToEndAsync*> 고 읽는 동안 스레드를 차단 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-253">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="d7b6a-254">위의 코드는 전체 HTTP 요청 본문을 메모리에 비동기적으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-254">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="d7b6a-255">요청이 클 경우 전체 HTTP 요청 본문을 메모리로 읽으면 OOM (메모리 부족) 조건이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-255">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="d7b6a-256">OOM은 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-256">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="d7b6a-257">자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-257">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="d7b6a-258">**다음 작업을 수행 합니다.** 다음 예제는 버퍼링 되지 않은 요청 본문을 사용 하 여 완전 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-258">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="d7b6a-259">위의 코드는 요청 본문을 c # 개체로 비동기식으로 역직렬화 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-259">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="d7b6a-260">요청을 통해 ReadFormAsync를 선호 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-260">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="d7b6a-261">`HttpContext.Request.Form` 대신 `HttpContext.Request.ReadFormAsync`을(를) 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-261">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="d7b6a-262">`HttpContext.Request.Form` 는 다음과 같은 경우에만 안전 하 게 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-262">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="d7b6a-263">을 호출 하 여 폼을 읽은 `ReadFormAsync` 경우</span><span class="sxs-lookup"><span data-stu-id="d7b6a-263">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="d7b6a-264">캐시 된 형식 값을 사용 하 여 읽고 있습니다. `HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="d7b6a-264">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="d7b6a-265">**이 작업을 수행 하지 마십시오.** 다음 예에서는를 사용 `HttpContext.Request.Form` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-265">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="d7b6a-266">`HttpContext.Request.Form`[비동기를 통한 동기화](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) 를 사용 하 고 스레드 풀 고갈를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-266">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="d7b6a-267">**다음 작업을 수행 합니다.** 다음 예제에서는 `HttpContext.Request.ReadFormAsync` 를 사용 하 여 양식 본문을 비동기식으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-267">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="d7b6a-268">대량 요청 본문 또는 응답 본문을 메모리로 읽지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-268">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="d7b6a-269">.NET에서 85 KB 보다 큰 모든 개체 할당은[LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)(large object heap)에서 끝납니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-269">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="d7b6a-270">큰 개체는 다음과 같은 두 가지 방법으로 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-270">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="d7b6a-271">새로 할당 된 큰 개체의 메모리를 지워야 하므로 할당 비용이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-271">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="d7b6a-272">CLR은 새로 할당 된 모든 개체의 메모리를 지우도록 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-272">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="d7b6a-273">LOH는 나머지 힙을 사용 하 여 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-273">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="d7b6a-274">LOH에는 전체 [가비지 수집](/dotnet/standard/garbage-collection/fundamentals) 또는 [Gen2 컬렉션이](/dotnet/standard/garbage-collection/fundamentals#generations)필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-274">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="d7b6a-275">이 [블로그 게시물](https://adamsitnik.com/Array-Pool/#the-problem) 에서는 문제 간략하게을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-275">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="d7b6a-276">많은 개체가 할당 되 면 Gen 2 개체로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-276">When a large object is allocated, it's marked as Gen 2 object.</span></span> <span data-ttu-id="d7b6a-277">작은 개체의 경우에는 Gen 0이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-277">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="d7b6a-278">LOH에서 메모리가 부족 한 경우 GC는 LOH 뿐만 아니라 전체 관리 되는 힙을 정리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-278">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="d7b6a-279">따라서 LOH를 포함 하 여 Gen 0, Gen 1 및 Gen 2를 정리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-279">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="d7b6a-280">이를 전체 가비지 수집 이라고 하며 가장 시간이 많이 걸리는 가비지 수집입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-280">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="d7b6a-281">많은 응용 프로그램의 경우 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-281">For many applications, it can be acceptable.</span></span> <span data-ttu-id="d7b6a-282">하지만, 평균 웹 요청을 처리 하는 데 큰 메모리 버퍼가 거의 필요 하지 않은 고성능 웹 서버에 대해서는 명확 하지 않습니다 (소켓에서 읽기, 압축 풀기, JSON & 추가).</span><span class="sxs-lookup"><span data-stu-id="d7b6a-282">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="d7b6a-283">단일 또는에 대량 요청 또는 응답 본문을 저장 하는 Naively `byte[]` `string` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-283">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="d7b6a-284">LOH의 공간이 빠르게 부족 해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-284">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="d7b6a-285">전체 Gc를 실행 하 여 앱에 대 한 성능 문제를 일으킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-285">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="d7b6a-286">동기 데이터 처리 API 작업</span><span class="sxs-lookup"><span data-stu-id="d7b6a-286">Working with a synchronous data processing API</span></span>

<span data-ttu-id="d7b6a-287">동기 읽기 및 쓰기를 지 원하는 serializer/직렬 변환기를 사용 하는 경우 (예:  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span><span class="sxs-lookup"><span data-stu-id="d7b6a-287">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="d7b6a-288">데이터를 직렬 변환기/역직렬화로 전달 하기 전에 비동기적으로 메모리에 버퍼링 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-288">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="d7b6a-289">요청이 크면 OOM (메모리 부족) 조건이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-289">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="d7b6a-290">OOM은 서비스 거부를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-290">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="d7b6a-291">자세한 내용은이 문서의 [메모리에 대 한 대량 요청 본문 또는 응답 본문 읽기 방지](#arlb) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-291">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="d7b6a-292">ASP.NET Core 3.0는 <xref:System.Text.Json> 기본적으로 JSON serialization에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-292">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="d7b6a-293"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-293"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="d7b6a-294">JSON을 비동기적으로 읽고 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-294">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="d7b6a-295">UTF-8 텍스트에 최적화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-295">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="d7b6a-296">일반적으로 `Newtonsoft.Json`보다 성능이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-296">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="d7b6a-297">IHttpContextAccessor를 필드에 저장 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-297">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="d7b6a-298">[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) 는 `HttpContext` 요청 스레드에서 액세스 될 때 활성 요청의를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-298">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="d7b6a-299">는 `IHttpContextAccessor.HttpContext` 필드 또는 변수에 저장 **하면 안 됩니다** .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-299">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="d7b6a-300">**이 작업을 수행 하지 마십시오.** 다음 예에서는를 `HttpContext` 필드에 저장 한 다음 나중에 사용 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-300">**Do not do this:** The following example stores the `HttpContext` in a field and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="d7b6a-301">위의 코드는 생성자에서 null 또는 잘못 된를 캡처하는 경우가 많습니다 `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-301">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="d7b6a-302">**다음 작업을 수행 합니다.** 다음 예제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-302">**Do this:** The following example:</span></span>

* <span data-ttu-id="d7b6a-303">를 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 필드에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-303">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="d7b6a-304">`HttpContext`는 필드를 올바른 시간에 사용 하 고를 확인 `null` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-304">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="d7b6a-305">여러 스레드에서 HttpContext에 액세스 하지 않음</span><span class="sxs-lookup"><span data-stu-id="d7b6a-305">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="d7b6a-306">`HttpContext` 는 스레드로부터 안전 *하지 않습니다* .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-306">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="d7b6a-307">`HttpContext`여러 스레드에서 병렬로 액세스 하면 중단, 충돌 및 데이터 손상과 같은 정의 되지 않은 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-307">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="d7b6a-308">**이 작업을 수행 하지 마십시오.** 다음 예제에서는 세 개의 병렬 요청을 만들고 나가는 HTTP 요청 전후에 들어오는 요청 경로를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-308">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="d7b6a-309">요청 경로는 여러 스레드에서 동시에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-309">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="d7b6a-310">**다음 작업을 수행 합니다.** 다음 예에서는 세 개의 병렬 요청을 만들기 전에 들어오는 요청에서 모든 데이터를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-310">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="d7b6a-311">요청이 완료 된 후에 HttpContext를 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-311">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="d7b6a-312">`HttpContext` 는 ASP.NET Core 파이프라인에 활성 HTTP 요청이 있는 경우에만 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-312">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="d7b6a-313">전체 ASP.NET Core 파이프라인은 모든 요청을 실행 하는 대리자의 비동기 체인입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-313">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="d7b6a-314">`Task`이 체인에서 반환 된이 완료 되 면이 `HttpContext` 재활용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-314">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="d7b6a-315">**이 작업을 수행 하지 마십시오.** 다음 예에서는를 사용 하 여 `async void` 첫 번째에 도달할 때 HTTP 요청이 완료 되도록 합니다 `await` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-315">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="d7b6a-316">ASP.NET Core 앱에서 **항상** 잘못 된 관행입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-316">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="d7b6a-317">HTTP 요청이 완료 된 후에에 액세스 합니다 `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-317">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="d7b6a-318">프로세스를 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-318">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="d7b6a-319">**다음 작업을 수행 합니다.** 다음 예제에서는 `Task` 프레임 워크에 대해를 반환 하므로 작업이 완료 될 때까지 HTTP 요청이 완료 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-319">**Do this:** The following example returns a `Task` to the framework, so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="d7b6a-320">백그라운드 스레드에서 HttpContext를 캡처하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-320">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="d7b6a-321">**이 작업을 수행 하지 마십시오.** 다음 예제에서는 속성에서를 캡처하는 방법을 보여 줍니다 `HttpContext` `Controller` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-321">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="d7b6a-322">작업 항목에서 다음을 수행할 수 있기 때문에 잘못 된 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-322">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="d7b6a-323">요청 범위 외부에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-323">Run outside of the request scope.</span></span>
* <span data-ttu-id="d7b6a-324">잘못 된를 읽으려고 했습니다 `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-324">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="d7b6a-325">**다음 작업을 수행 합니다.** 다음 예제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-325">**Do this:** The following example:</span></span>

* <span data-ttu-id="d7b6a-326">요청 하는 동안 백그라운드 작업에 필요한 데이터를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-326">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="d7b6a-327">는 컨트롤러에서 아무것도 참조 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-327">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="d7b6a-328">백그라운드 작업은 호스팅된 서비스로 구현 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-328">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="d7b6a-329">자세한 내용은 [호스티드 서비스를 사용하는 백그라운드 작업](xref:fundamentals/host/hosted-services)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-329">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="d7b6a-330">백그라운드 스레드에서 컨트롤러에 삽입 된 서비스를 캡처하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-330">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="d7b6a-331">**이 작업을 수행 하지 마십시오.** 다음 예제에서는 `DbContext` 작업 매개 변수에서를 캡처하는 방법을 보여 줍니다 `Controller` .</span><span class="sxs-lookup"><span data-stu-id="d7b6a-331">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="d7b6a-332">이는 잘못 된 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-332">This is a bad practice.</span></span>  <span data-ttu-id="d7b6a-333">작업 항목은 요청 범위 외부에서 실행 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-333">The work item could run outside of the request scope.</span></span> <span data-ttu-id="d7b6a-334">`ContosoDbContext`이 요청으로 범위가 지정 되 면이 발생 `ObjectDisposedException` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-334">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="d7b6a-335">**다음 작업을 수행 합니다.** 다음 예제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-335">**Do this:** The following example:</span></span>

* <span data-ttu-id="d7b6a-336"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>백그라운드 작업 항목에서 범위를 만들기 위해를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-336">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="d7b6a-337">`IServiceScopeFactory` 는 단일 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-337">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="d7b6a-338">백그라운드 스레드에서 새 종속성 주입 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-338">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="d7b6a-339">는 컨트롤러에서 아무것도 참조 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-339">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="d7b6a-340">`ContosoDbContext`들어오는 요청에서를 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-340">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="d7b6a-341">강조 표시 된 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-341">The following highlighted code:</span></span>

* <span data-ttu-id="d7b6a-342">백그라운드 작업의 수명 범위를 만들고 해당 작업에서 서비스를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-342">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="d7b6a-343">`ContosoDbContext`는 올바른 범위에서를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-343">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="d7b6a-344">응답 본문이 시작 된 후 상태 코드 또는 헤더를 수정 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-344">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="d7b6a-345">ASP.NET Core는 HTTP 응답 본문을 버퍼링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-345">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="d7b6a-346">처음 응답을 쓸 때:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-346">The first time the response is written:</span></span>

* <span data-ttu-id="d7b6a-347">헤더는 해당 본문의 청크와 함께 클라이언트에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-347">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="d7b6a-348">더 이상 응답 헤더를 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-348">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="d7b6a-349">**이 작업을 수행 하지 마십시오.** 다음 코드는 응답이 이미 시작 된 후 응답 헤더를 추가 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-349">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="d7b6a-350">위의 코드에서 `context.Response.Headers["test"] = "test value";` 가 응답에 기록 되는 경우에서 예외를 throw `next()` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-350">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="d7b6a-351">**다음 작업을 수행 합니다.** 다음 예에서는 헤더를 수정 하기 전에 HTTP 응답이 시작 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-351">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="d7b6a-352">**다음 작업을 수행 합니다.** 다음 예제에서는를 사용 하 여 `HttpResponse.OnStarting` 응답 헤더가 클라이언트에 플러시될 때까지 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-352">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="d7b6a-353">응답을 시작 하지 않은 경우 응답 헤더를 쓰기 직전에 호출 되는 콜백을 등록할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-353">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="d7b6a-354">응답이 시작 되지 않은 경우를 확인 하는 중:</span><span class="sxs-lookup"><span data-stu-id="d7b6a-354">Checking if the response has not started:</span></span>

* <span data-ttu-id="d7b6a-355">헤더를 적시에 추가 하거나 재정의 하는 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-355">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="d7b6a-356">파이프라인의 다음 미들웨어에 대 한 지식이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-356">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="d7b6a-357">응답 본문에 대 한 쓰기를 이미 시작한 경우 next ()를 호출 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-357">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="d7b6a-358">구성 요소는 응답을 처리 하 고 조작할 수 있는 경우에만 호출 될 것으로 예상 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-358">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>

## <a name="use-in-process-hosting-with-iis"></a><span data-ttu-id="d7b6a-359">IIS를 사용 하 여 in-process 호스팅 사용</span><span class="sxs-lookup"><span data-stu-id="d7b6a-359">Use In-process hosting with IIS</span></span>

<span data-ttu-id="d7b6a-360">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-360">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="d7b6a-361">In-process 호스트는 루프백 어댑터를 통해 요청이 프록시 되지 않으므로 out-of-process 호스팅을 통해 향상 된 성능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-361">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="d7b6a-362">루프백 어댑터는 나가는 네트워크 트래픽을 동일한 컴퓨터로 다시 반환 하는 네트워크 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-362">The loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="d7b6a-363">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-363">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d7b6a-364">프로젝트는 ASP.NET Core 3.0 이상에서 in-process 호스팅 모델로 기본 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-364">Projects default to the in-process hosting model in ASP.NET Core 3.0 and later.</span></span>

<span data-ttu-id="d7b6a-365">자세한 내용은 [IIS를 사용 하 여 Windows의 호스트 ASP.NET Core](xref:host-and-deploy/iis/index) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d7b6a-365">For more information, see [Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index)</span></span>