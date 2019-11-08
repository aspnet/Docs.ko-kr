---
title: ASP.NET Core의 메모리 관리 및 패턴
author: rick-anderson
description: ASP.NET Core에서 메모리를 관리 하는 방법 및 GC (가비지 수집기)의 작동 방식에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2019
uid: performance/memory
ms.openlocfilehash: 8f6b47ecde6f265bfb9437234b89f11f7d235869
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660009"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="5bcf7-103">ASP.NET Core의 메모리 관리 및 GC (가비지 수집)</span><span class="sxs-lookup"><span data-stu-id="5bcf7-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="5bcf7-104">[Sébastien Ros](https://github.com/sebastienros) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5bcf7-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5bcf7-105">.NET 등의 관리 되는 프레임 워크 에서도 메모리 관리가 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="5bcf7-106">메모리 문제를 분석 하 고 이해 하는 것은 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="5bcf7-107">이 문서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-107">This article:</span></span>

* <span data-ttu-id="5bcf7-108">는 많은 *메모리 누수로* 인해 발생 했으며 *GC가 작동 하지 않습니다* .</span><span class="sxs-lookup"><span data-stu-id="5bcf7-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="5bcf7-109">이러한 문제의 대부분은 .NET Core에서 메모리 소비가 어떻게 작동 하는지 이해 하거나 측정 방법을 이해 하지 못하는 경우에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="5bcf7-110">문제가 있는 메모리 사용을 보여 주고 대체 방법을 제안 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="5bcf7-111">.NET Core에서 GC (가비지 수집)가 작동 하는 방식</span><span class="sxs-lookup"><span data-stu-id="5bcf7-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="5bcf7-112">GC는 각 세그먼트가 연속 되는 메모리 범위인 힙 세그먼트를 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="5bcf7-113">힙에 배치 된 개체는 3 세대 (0, 1 또는 2 개)로 분류 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="5bcf7-114">세대는 GC가 앱에서 더 이상 참조 하지 않는 관리 되는 개체의 메모리를 해제 하려고 시도 하는 빈도를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="5bcf7-115">번호가 낮은 세대는 GC가 더 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="5bcf7-116">개체는 수명에 따라 한 세대에서 다른 세대로 이동 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="5bcf7-117">개체가 더 오래 지속 되 면 더 높은 생성으로 이동 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="5bcf7-118">앞에서 설명한 것 처럼 더 높은 세대는 GC가 그다지 자주 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="5bcf7-119">수명이 짧은 개체는 항상 0 세대에 남아 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="5bcf7-120">예를 들어 웹 요청 수명 중에 참조 되는 개체는 수명이 짧습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="5bcf7-121">응용 프로그램 수준 [단일 항목](xref:fundamentals/dependency-injection#service-lifetimes) 일반적으로 2 세대로 마이그레이션됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="5bcf7-122">ASP.NET Core 앱이 시작 되 면 GC는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="5bcf7-123">초기 힙 세그먼트에 대 한 일부 메모리를 예약 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="5bcf7-124">런타임이 로드 될 때 작은 메모리 부분을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="5bcf7-125">위의 메모리 할당은 성능상의 이유로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="5bcf7-126">성능 혜택은 연속 메모리의 힙 세그먼트에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="5bcf7-127">GC를 호출 합니다. 모으려면</span><span class="sxs-lookup"><span data-stu-id="5bcf7-127">Call GC.Collect</span></span>

<span data-ttu-id="5bcf7-128">[GC를 호출 합니다. ](xref:System.GC.Collect*)명시적으로 수집:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="5bcf7-129">프로덕션 ASP.NET Core 앱에서 수행 **하면 안 됩니다** .</span><span class="sxs-lookup"><span data-stu-id="5bcf7-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="5bcf7-130">는 메모리 누수를 조사할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="5bcf7-131">조사할 때, GC가 메모리에서 모든 현 수 개체를 제거 하 여 메모리를 측정할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="5bcf7-132">앱의 메모리 사용 분석</span><span class="sxs-lookup"><span data-stu-id="5bcf7-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="5bcf7-133">전용 도구는 메모리 사용을 분석 하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="5bcf7-134">개체 참조 계산</span><span class="sxs-lookup"><span data-stu-id="5bcf7-134">Counting object references</span></span>
- <span data-ttu-id="5bcf7-135">GC가 CPU 사용량에 미치는 영향 측정</span><span class="sxs-lookup"><span data-stu-id="5bcf7-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="5bcf7-136">각 세대에 사용 되는 메모리 공간 측정</span><span class="sxs-lookup"><span data-stu-id="5bcf7-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="5bcf7-137">다음 도구를 사용 하 여 메모리 사용을 분석 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="5bcf7-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): 프로덕션 컴퓨터에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="5bcf7-139">Visual Studio 디버거를 사용 하지 않고 메모리 사용 분석</span><span class="sxs-lookup"><span data-stu-id="5bcf7-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="5bcf7-140">Visual Studio에서 프로필 메모리 사용량</span><span class="sxs-lookup"><span data-stu-id="5bcf7-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="5bcf7-141">메모리 문제 검색</span><span class="sxs-lookup"><span data-stu-id="5bcf7-141">Detecting memory issues</span></span>

<span data-ttu-id="5bcf7-142">작업 관리자를 사용 하 여 ASP.NET 앱에서 사용 하는 메모리의 양을 파악할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="5bcf7-143">작업 관리자 메모리 값:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="5bcf7-144">ASP.NET 프로세스에서 사용 하는 메모리의 양을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="5bcf7-145">응용 프로그램의 살아 있는 개체와 네이티브 메모리 사용량과 같은 기타 메모리 소비자를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="5bcf7-146">작업 관리자 메모리 값이 무기한으로 늘어나고 평면화 되지 않으면 앱에 메모리 누수가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="5bcf7-147">다음 섹션에서는 몇 가지 메모리 사용 패턴을 설명 하 고 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="5bcf7-148">샘플 메모리 사용량 앱 표시</span><span class="sxs-lookup"><span data-stu-id="5bcf7-148">Sample display memory usage app</span></span>

<span data-ttu-id="5bcf7-149">[Memoryleak 샘플 앱](https://github.com/sebastienros/memoryleak) 은 GitHub에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="5bcf7-150">MemoryLeak 앱:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="5bcf7-151">앱에 대 한 실시간 메모리 및 GC 데이터를 수집 하는 진단 컨트롤러를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="5bcf7-152">에는 메모리 및 GC 데이터를 표시 하는 인덱스 페이지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="5bcf7-153">인덱스 페이지는 매 초 마다 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="5bcf7-154">다양 한 메모리 로드 패턴을 제공 하는 API 컨트롤러를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="5bcf7-155">는 지원 되는 도구가 아닙니다. 그러나 ASP.NET Core 앱의 메모리 사용 패턴을 표시 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="5bcf7-156">MemoryLeak를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-156">Run MemoryLeak.</span></span> <span data-ttu-id="5bcf7-157">할당 된 메모리는 GC가 발생할 때까지 천천히 늘어납니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="5bcf7-158">도구에서 사용자 지정 개체를 할당 하 여 데이터를 캡처하도록 하므로 메모리가 늘어납니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="5bcf7-159">다음 이미지는 Gen 0 GC가 발생할 때 MemoryLeak 인덱스 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="5bcf7-160">API 컨트롤러의 API 끝점이 호출 되지 않았기 때문에 차트에 0 개 RPS (초당 요청 수)가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![이전 차트](memory/_static/0RPS.png)

<span data-ttu-id="5bcf7-162">차트에는 메모리 사용량에 대 한 두 개의 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="5bcf7-163">할당 됨: 관리 되는 개체가 차지 하는 메모리 양</span><span class="sxs-lookup"><span data-stu-id="5bcf7-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="5bcf7-164">작업 집합: 프로세스에서 사용 하는 총 실제 메모리 (RAM)입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-164">Working set: the total physical memory (RAM) used by the process.</span></span> <span data-ttu-id="5bcf7-165">표시 되는 작업 집합은 작업 관리자가 표시할 수 있는 값과 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-165">The working set shown is the same value Task Manager can display.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="5bcf7-166">임시 개체</span><span class="sxs-lookup"><span data-stu-id="5bcf7-166">Transient objects</span></span>

<span data-ttu-id="5bcf7-167">다음 API는 10kb 문자열 인스턴스를 만들고이를 클라이언트에 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="5bcf7-168">각 요청에서 새 개체는 메모리에 할당 되 고 응답에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="5bcf7-169">문자열은 .NET에서 u t f-16 자로 저장 되므로 각 문자는 메모리에서 2 바이트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="5bcf7-170">다음 그래프는에서 상대적으로 적은 로드를 사용 하 여 메모리 할당이 GC의 영향을 받는 방법을 보여 주기 위해 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![이전 차트](memory/_static/bigstring.png)

<span data-ttu-id="5bcf7-172">위의 차트는 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-172">The preceding chart shows:</span></span>

* <span data-ttu-id="5bcf7-173">4K RPS (초당 요청 수)</span><span class="sxs-lookup"><span data-stu-id="5bcf7-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="5bcf7-174">0 세대 GC 컬렉션은 2 초 마다 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="5bcf7-175">작업 집합은 약 500 MB의 상수입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="5bcf7-176">CPU는 12%입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-176">CPU is 12%.</span></span>
* <span data-ttu-id="5bcf7-177">메모리 사용량과 릴리스 (GC를 통해)는 안정적입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="5bcf7-178">다음 차트는 컴퓨터에서 처리할 수 있는 최대 처리량으로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![이전 차트](memory/_static/bigstring2.png)

<span data-ttu-id="5bcf7-180">위의 차트는 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-180">The preceding chart shows:</span></span>

* <span data-ttu-id="5bcf7-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="5bcf7-181">22K RPS</span></span>
* <span data-ttu-id="5bcf7-182">0 세대 GC 컬렉션은 초당 여러 번 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="5bcf7-183">앱이 초당 더 많은 메모리를 할당 했기 때문에 1 세대 수집이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="5bcf7-184">작업 집합은 약 500 MB의 상수입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="5bcf7-185">CPU는 33%입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-185">CPU is 33%.</span></span>
* <span data-ttu-id="5bcf7-186">메모리 사용량과 릴리스 (GC를 통해)는 안정적입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="5bcf7-187">CPU (33%) 과도 하 게 사용 되지 않으므로 가비지 수집은 많은 수의 할당을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="5bcf7-188">워크스테이션 GC와 서버 GC 비교</span><span class="sxs-lookup"><span data-stu-id="5bcf7-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="5bcf7-189">.NET 가비지 수집기에는 다음과 같은 두 가지 모드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="5bcf7-190">**워크스테이션 GC**: 데스크톱에 최적화 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="5bcf7-191">**서버 GC**.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-191">**Server GC**.</span></span> <span data-ttu-id="5bcf7-192">ASP.NET Core 앱에 대 한 기본 GC입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="5bcf7-193">서버에 최적화 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-193">Optimized for the server.</span></span>

<span data-ttu-id="5bcf7-194">Json 모드는 프로젝트 파일이 나 게시 된 앱의 *runtimeconfig.template.json* 파일에서 명시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="5bcf7-195">다음 태그는 프로젝트 파일의 `ServerGarbageCollection` 설정을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="5bcf7-196">프로젝트 파일에서 `ServerGarbageCollection`를 변경 하려면 앱을 다시 빌드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="5bcf7-197">**참고:** 단일 코어가 있는 컴퓨터에서는 서버 가비지 수집을 사용할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="5bcf7-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="5bcf7-198">자세한 내용은 <xref:System.Runtime.GCSettings.IsServerGC>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="5bcf7-199">다음 이미지는 워크스테이션 GC를 사용 하는 5K RPS 아래의 메모리 프로필을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![이전 차트](memory/_static/workstation.png)

<span data-ttu-id="5bcf7-201">이 차트와 서버 버전의 차이점은 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="5bcf7-202">작업 집합이 500 MB에서 70 MB로 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="5bcf7-203">GC는 2 초 마다 초당 0 세대 컬렉션을 여러 번 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="5bcf7-204">GC는 300 m b에서 10mb로 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="5bcf7-205">일반적인 웹 서버 환경에서 CPU 사용량은 메모리 보다 더 중요 하므로 서버 GC가 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="5bcf7-206">메모리 사용률이 높고 CPU 사용량이 상대적으로 낮으면 워크스테이션 GC가 더 성능이 향상 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="5bcf7-207">예를 들어 메모리가 부족 한 여러 웹 앱을 호스트 하는 고밀도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-207">For example, high density hosting several web apps where memory is scarce.</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="5bcf7-208">영구 개체 참조</span><span class="sxs-lookup"><span data-stu-id="5bcf7-208">Persistent object references</span></span>

<span data-ttu-id="5bcf7-209">GC는 참조 되는 개체를 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-209">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="5bcf7-210">참조 되지만 더 이상 필요 하지 않은 개체는 메모리 누수가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-210">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="5bcf7-211">앱에서 자주 개체를 할당 하 고 더 이상 필요 하지 않은 개체를 해제 하지 못한 경우 시간이 지남에 따라 메모리 사용이 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-211">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="5bcf7-212">다음 API는 10kb 문자열 인스턴스를 만들고이를 클라이언트에 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-212">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="5bcf7-213">이전 예제와의 차이점은이 인스턴스가 정적 멤버에 의해 참조 되는 것입니다. 즉, 컬렉션에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-213">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="5bcf7-214">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="5bcf7-214">The preceding code:</span></span>

* <span data-ttu-id="5bcf7-215">는 일반적인 메모리 누수의 한 예입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-215">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="5bcf7-216">를 자주 호출 하면 프로세스가 `OutOfMemory` 예외로 인해 충돌 될 때까지 응용 프로그램 메모리가 늘어납니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-216">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![이전 차트](memory/_static/eternal.png)

<span data-ttu-id="5bcf7-218">이전 이미지에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-218">In the preceding image:</span></span>

* <span data-ttu-id="5bcf7-219">`/api/staticstring` 끝점을 로드 테스트 하면 메모리의 선형 증가가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-219">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="5bcf7-220">GC는 2 세대 컬렉션을 호출 하 여 메모리 압력이 증가 함에 따라 메모리를 해제 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-220">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="5bcf7-221">GC에서 누수 된 메모리를 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-221">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="5bcf7-222">할당 된 작업과 작업 집합이 시간에 의해 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-222">Allocated and working set increase with time.</span></span>

<span data-ttu-id="5bcf7-223">캐싱과 같은 일부 시나리오에서는 메모리가 중이 강제로 해제 될 때까지 개체 참조를 유지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-223">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="5bcf7-224">이 유형의 캐싱 코드에는 <xref:System.WeakReference> 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-224">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="5bcf7-225">`WeakReference` 개체는 memory pressures에서 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-225">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="5bcf7-226"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>의 기본 구현에서는 `WeakReference`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-226">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="5bcf7-227">기본 메모리</span><span class="sxs-lookup"><span data-stu-id="5bcf7-227">Native memory</span></span>

<span data-ttu-id="5bcf7-228">일부 .NET Core 개체는 네이티브 메모리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-228">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="5bcf7-229">Native memory는 GC에서 수집할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="5bcf7-229">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="5bcf7-230">네이티브 메모리를 사용 하는 .NET 개체는 네이티브 코드를 사용 하 여 해제 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-230">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="5bcf7-231">.NET에서는 개발자가 네이티브 메모리를 릴리스할 수 있는 <xref:System.IDisposable> 인터페이스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-231">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="5bcf7-232"><xref:System.IDisposable.Dispose*>를 호출 하지 않는 경우에도, 올바르게 구현 된 클래스는 [종료 자가](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 실행 될 때 `Dispose`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-232">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="5bcf7-233">다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-233">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="5bcf7-234">[PhysicaFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) 는 관리 되는 클래스 이므로 인스턴스가 요청 끝에서 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-234">[PhysicaFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="5bcf7-235">다음 이미지는 `fileprovider` API를 계속 호출 하는 동안 메모리 프로필을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-235">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![이전 차트](memory/_static/fileprovider.png)

<span data-ttu-id="5bcf7-237">위의 차트는 메모리 사용이 늘어나는 것을 유지 하므로이 클래스의 구현과 관련 된 명확한 문제를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-237">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="5bcf7-238">이 문제는 [이 문제](https://github.com/aspnet/Home/issues/3110)에서 추적 되는 알려진 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-238">This is a known problem that is being tracked in [this issue](https://github.com/aspnet/Home/issues/3110).</span></span>

<span data-ttu-id="5bcf7-239">사용자 코드에서 다음 중 하나를 사용 하 여 동일한 누수를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-239">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="5bcf7-240">클래스를 올바르게 해제 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-240">Not releasing the class correctly.</span></span>
* <span data-ttu-id="5bcf7-241">삭제 해야 하는 종속 개체의 `Dispose`메서드를 호출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-241">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="5bcf7-242">Large objects 힙</span><span class="sxs-lookup"><span data-stu-id="5bcf7-242">Large objects heap</span></span>

<span data-ttu-id="5bcf7-243">메모리를 자주 할당/해제 하는 주기는 메모리를 조각화 할 수 있습니다. 특히 대량의 메모리를 할당 하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-243">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="5bcf7-244">개체는 인접 한 메모리 블록에 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-244">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="5bcf7-245">조각화를 완화 하기 위해 GC에서 메모리를 해제 하는 경우 조각 모음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-245">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="5bcf7-246">이 프로세스를 **압축**이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-246">This process is called **compaction**.</span></span> <span data-ttu-id="5bcf7-247">압축에는 개체 이동이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-247">Compaction involves moving objects.</span></span> <span data-ttu-id="5bcf7-248">개체를 이동 하면 성능이 저하 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-248">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="5bcf7-249">이러한 이유로, GC는 LOH ( [large object heap](/dotnet/standard/garbage-collection/large-object-heap) ) 라는 _커다란_ 개체에 대 한 특수 메모리 영역을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-249">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="5bcf7-250">85000 바이트 (약 83 KB) 보다 큰 개체는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-250">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="5bcf7-251">LOH에 배치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-251">Placed on the LOH.</span></span>
* <span data-ttu-id="5bcf7-252">압축 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-252">Not compacted.</span></span>
* <span data-ttu-id="5bcf7-253">2 세대 Gc 중 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-253">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="5bcf7-254">LOH가 가득 차면 GC가 2 세대 수집을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-254">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="5bcf7-255">2 세대 컬렉션:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-255">Generation 2 collections:</span></span>

* <span data-ttu-id="5bcf7-256">기본적으로 속도가 느립니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-256">Are inherently slow.</span></span>
* <span data-ttu-id="5bcf7-257">다른 모든 세대에서 수집을 트리거하는 비용도 추가로 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-257">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="5bcf7-258">다음 코드는 LOH를 즉시 압축 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-258">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="5bcf7-259">LOH를 압축 하는 방법에 대 한 자세한 내용은 <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-259">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="5bcf7-260">.NET Core 3.0 이상 버전을 사용 하는 컨테이너에서는 LOH가 자동으로 압축 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-260">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="5bcf7-261">이 동작을 설명 하는 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-261">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="5bcf7-262">다음 차트는 최대 부하 상태에서 `/api/loh/84975` 끝점을 호출 하는 메모리 프로필을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-262">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![이전 차트](memory/_static/loh1.png)

<span data-ttu-id="5bcf7-264">다음 차트에서는 `/api/loh/84976` 끝점을 호출 하는 메모리 프로필을 보여 주며 *바이트를 하나 더*할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-264">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![이전 차트](memory/_static/loh2.png)

<span data-ttu-id="5bcf7-266">참고: `byte[]` 구조에 오버 헤드 바이트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-266">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="5bcf7-267">84976 바이트가 85000 제한을 트리거하는 이유입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-267">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="5bcf7-268">위의 두 차트 비교:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-268">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="5bcf7-269">작업 집합은 두 시나리오 (약 450 MB)에 대해 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-269">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="5bcf7-270">LOH 요청 (84975 바이트)은 대부분 0 세대 컬렉션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-270">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="5bcf7-271">LOH 초과 요청은 상수 2 세대 컬렉션을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-271">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="5bcf7-272">2 세대 컬렉션은 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-272">Generation 2 collections are expensive.</span></span> <span data-ttu-id="5bcf7-273">더 많은 CPU가 필요 하 고 처리량이 거의 50% 감소 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-273">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="5bcf7-274">임시 큰 개체는 gen2 Gc를 야기 하므로 특히 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-274">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="5bcf7-275">성능을 최대화 하려면 많은 개체 사용을 최소화 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-275">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="5bcf7-276">가능 하면 크게 개체를 분할 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-276">If possible, split up large objects.</span></span> <span data-ttu-id="5bcf7-277">예를 들어 ASP.NET Core의 [응답 캐싱](xref:performance/caching/response) 미들웨어는 캐시 항목을 85000 바이트 미만 블록으로 분할 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-277">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="5bcf7-278">다음 링크에서는 개체를 LOH 제한으로 유지 하는 ASP.NET Core 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-278">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>
- [<span data-ttu-id="5bcf7-279">ResponseCaching/스트림/StreamUtilities .cs</span><span class="sxs-lookup"><span data-stu-id="5bcf7-279">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/aspnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
- [<span data-ttu-id="5bcf7-280">ResponseCaching/MemoryResponseCache</span><span class="sxs-lookup"><span data-stu-id="5bcf7-280">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="5bcf7-281">자세한 내용은 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-281">For more information, see:</span></span>

* [<span data-ttu-id="5bcf7-282">대량 개체 힙 검사</span><span class="sxs-lookup"><span data-stu-id="5bcf7-282">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="5bcf7-283">Large object 힙</span><span class="sxs-lookup"><span data-stu-id="5bcf7-283">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="5bcf7-284">HttpClient</span><span class="sxs-lookup"><span data-stu-id="5bcf7-284">HttpClient</span></span>

<span data-ttu-id="5bcf7-285"><xref:System.Net.Http.HttpClient> 잘못 사용 하면 리소스 누수가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-285">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="5bcf7-286">데이터베이스 연결, 소켓, 파일 핸들 등의 시스템 리소스:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-286">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="5bcf7-287">메모리 보다 부족 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-287">Are more scarce than memory.</span></span>
* <span data-ttu-id="5bcf7-288">는 메모리 보다 누수 될 때 더 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-288">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="5bcf7-289">숙련 된 .NET 개발자는 <xref:System.IDisposable>를 구현 하는 개체에서 <xref:System.IDisposable.Dispose*>를 호출 하는 것을 알고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-289">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="5bcf7-290">일반적으로 `IDisposable`를 구현 하는 개체를 삭제 하지 않으면 메모리 또는 누수 된 시스템 리소스가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-290">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="5bcf7-291">`HttpClient`는 `IDisposable`를 구현 하지만 모든 호출에서 **삭제 되어서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="5bcf7-291">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="5bcf7-292">대신 `HttpClient`를 다시 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-292">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="5bcf7-293">다음 끝점은 모든 요청에 새 `HttpClient` 인스턴스를 만들고 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-293">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="5bcf7-294">로드 아래에 다음 오류 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-294">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="5bcf7-295">`HttpClient` 인스턴스가 삭제 된 경우에도 실제 네트워크 연결은 운영 체제에서 해제 하는 데 약간의 시간이 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-295">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="5bcf7-296">계속 해 서 새 연결을 만들면 _포트 고갈_ 가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-296">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="5bcf7-297">각 클라이언트 연결에는 자체 클라이언트 포트가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-297">Each client connection requires its own client port.</span></span>

<span data-ttu-id="5bcf7-298">포트 소모를 방지 하는 한 가지 방법은 동일한 `HttpClient` 인스턴스를 다시 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-298">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="5bcf7-299">앱이 중지 되 면 `HttpClient` 인스턴스가 해제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-299">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="5bcf7-300">이 예제에서는 각 사용 후 삭제 가능한 모든 리소스를 삭제 해야 함을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-300">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="5bcf7-301">`HttpClient` 인스턴스의 수명을 처리 하는 더 좋은 방법은 다음을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-301">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="5bcf7-302">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="5bcf7-302">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="5bcf7-303">HTTPClient 팩터리 블로그</span><span class="sxs-lookup"><span data-stu-id="5bcf7-303">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="5bcf7-304">개체 풀링</span><span class="sxs-lookup"><span data-stu-id="5bcf7-304">Object pooling</span></span>

<span data-ttu-id="5bcf7-305">이전 예제에서는 `HttpClient` 인스턴스를 정적으로 설정 하 고 모든 요청에서 다시 사용할 수 있는 방법을 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-305">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="5bcf7-306">다시 사용 하면 리소스가 부족 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-306">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="5bcf7-307">개체 풀링:</span><span class="sxs-lookup"><span data-stu-id="5bcf7-307">Object pooling:</span></span>

* <span data-ttu-id="5bcf7-308">에서는 재사용 패턴을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-308">Uses the reuse pattern.</span></span>
* <span data-ttu-id="5bcf7-309">는 만드는 데 비용이 많이 드는 개체를 위해 설계 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-309">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="5bcf7-310">풀은 스레드 간에 예약 및 해제할 수 있는 미리 초기화 된 개체의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-310">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="5bcf7-311">풀은 제한, 미리 정의 된 크기 또는 증가율과 같은 할당 규칙을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-311">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="5bcf7-312">NuGet 패키지의 경우 이러한 풀을 관리 하는 데 도움이 되는 클래스가 포함 되어 [있습니다.](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)</span><span class="sxs-lookup"><span data-stu-id="5bcf7-312">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="5bcf7-313">다음 API 끝점은 각 요청에 대해 임의의 숫자로 채워진 `byte` 버퍼를 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-313">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="5bcf7-314">다음 차트는 중간 부하로 이전 API를 호출 하는 것을 보여 주는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-314">The following chart display calling the preceding API with moderate load:</span></span>

![이전 차트](memory/_static/array.png)

<span data-ttu-id="5bcf7-316">위의 차트에서 0 세대 수집은 초당 한 번 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-316">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="5bcf7-317">위의 코드는 [`ArrayPool<T>`](xref:System.Buffers.ArrayPool`1)를 사용 하 여 `byte` 버퍼를 풀링 하 여 최적화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-317">The preceding code can be optimized by pooling the `byte` buffer by using [`ArrayPool<T>`](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="5bcf7-318">정적 인스턴스는 요청에서 재사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-318">A static instance is reused across requests.</span></span>

<span data-ttu-id="5bcf7-319">이 방법의 다른 기능은 풀링된 개체가 API에서 반환 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-319">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="5bcf7-320">즉, 다음을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-320">That means:</span></span>

* <span data-ttu-id="5bcf7-321">메서드에서 반환 하는 즉시 개체는 컨트롤에서 제외 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-321">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="5bcf7-322">개체를 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-322">You can't release the object.</span></span>

<span data-ttu-id="5bcf7-323">개체의 삭제를 설정 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-323">To set up disposal of the object:</span></span>

* <span data-ttu-id="5bcf7-324">삭제 가능한 개체에 풀링된 배열을 캡슐화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-324">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="5bcf7-325">풀링된 개체를 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-325">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="5bcf7-326">`RegisterForDispose`는 HTTP 요청이 완료 된 경우에만 해제 되도록 대상 개체에 대 한 `Dispose`호출을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-326">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="5bcf7-327">풀링된 버전이 아닌 버전과 동일한 부하를 적용 하면 다음 차트가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-327">Applying the same load as the non-pooled version results in the following chart:</span></span>

![이전 차트](memory/_static/pooledarray.png)

<span data-ttu-id="5bcf7-329">주요 차이점은 바이트를 할당 하는 것이 고, 결과적으로 0 세대 수집이 훨씬 더 적기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="5bcf7-329">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bcf7-330">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5bcf7-330">Additional resources</span></span>

* [<span data-ttu-id="5bcf7-331">가비지 수집</span><span class="sxs-lookup"><span data-stu-id="5bcf7-331">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="5bcf7-332">동시성 시각화 도우미를 사용 하 여 다양 한 GC 모드 이해</span><span class="sxs-lookup"><span data-stu-id="5bcf7-332">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="5bcf7-333">대량 개체 힙 검사</span><span class="sxs-lookup"><span data-stu-id="5bcf7-333">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="5bcf7-334">Large object 힙</span><span class="sxs-lookup"><span data-stu-id="5bcf7-334">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)