---
title: ASP.NET Core Blazor WebAssembly 성능 모범 사례
author: pranavkm
description: ASP.NET Core Blazor WebAssembly 앱의 성능을 높이고 일반적인 성능 문제를 방지하기 위한 팁입니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: ea3f197e5bab82f4fb40238fe31cd5ce29ab62ad
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900975"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="e8368-103">ASP.NET Core Blazor WebAssembly 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="e8368-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="e8368-104">작성자: [Pranav Krishnamoorthy](https://github.com/pranavkm) 및 [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="e8368-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="e8368-105">Blazor WebAssembly는 세심하게 디자인되고 최적화되어 가장 현실적인 애플리케이션 UI 시나리오에서 고성능을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="e8368-106">하지만 개발자가 적합한 패턴과 기능을 사용해야만 최적의 결과를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="e8368-107">다음 측면을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-107">Consider the following aspects:</span></span>

* <span data-ttu-id="e8368-108">**런타임 처리량**: .NET 코드는 WebAssembly 런타임 내의 인터프리터에서 실행되므로 CPU 처리량이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="e8368-109">까다로운 시나리오에서 앱은 [렌더링 속도 최적화](#optimize-rendering-speed)를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="e8368-110">**시작 시간**: 앱은 브라우저에 .NET 런타임을 전송하므로 [애플리케이션 다운로드 크기를 최소화](#minimize-app-download-size)하는 기능을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="e8368-111">렌더링 속도 최적화</span><span class="sxs-lookup"><span data-stu-id="e8368-111">Optimize rendering speed</span></span>

<span data-ttu-id="e8368-112">다음 섹션에서는 렌더링 워크로드를 최소화하고 UI 응답성을 개선하기 위한 권장 사항을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="e8368-113">이 지침에 따르면 손쉽게 UI 렌더링 속도를 ‘10배 이상 개선’할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="e8368-114">불필요한 구성 요소 하위 트리 렌더링 방지</span><span class="sxs-lookup"><span data-stu-id="e8368-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="e8368-115">런타임에 구성 요소는 계층 구조로 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="e8368-116">루트 구성 요소에는 자식 구성 요소가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-116">A root component has child components.</span></span> <span data-ttu-id="e8368-117">차례대로 루트의 자식에는 자체 자식 구성 요소가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="e8368-118">사용자의 단추 선택과 같은 이벤트가 발생하는 경우 Blazor가 다시 렌더링할 구성 요소를 결정하는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

 1. <span data-ttu-id="e8368-119">이벤트 자체는 어느 것이든 이벤트 처리기를 렌더링한 구성 요소에 디스패치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="e8368-120">이벤트 처리기를 실행한 후 해당 구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="e8368-121">구성 요소가 다시 렌더링될 때마다 새 매개 변수 값 복사본을 각 자식 구성 요소에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="e8368-122">새 매개 변수 값 세트를 수신하면 각 구성 요소는 다시 렌더링할지를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="e8368-123">기본적으로 매개 변수 값이 변경되었을 수 있는 경우(예: 변경 가능한 개체인 경우) 구성 요소는 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="e8368-124">이 시퀀스의 마지막 두 단계는 재귀적으로 구성 요소 계층 구조 아래로 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="e8368-125">대부분의 경우 전체 하위 트리는 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="e8368-126">즉, 상위 수준 구성 요소를 대상으로 하는 이벤트는 해당 지점 아래 모든 항목이 다시 렌더링되어야 하므로 비용이 많이 드는 다시 렌더링 프로세스를 발생시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="e8368-127">이 프로세스를 중단하고 특정 하위 트리에 대한 렌더링 재귀를 방지하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="e8368-128">특정 구성 요소에 대한 모든 매개 변수가 변경할 수 없는 기본 형식(예: `string`, `int`, `bool`, `DateTime` 등)인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="e8368-129">관련 매개 변수 값이 변경되지 않은 경우 변경 내용을 검색하는 기본 제공 논리가 자동으로 다시 렌더링을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="e8368-130">`<Customer CustomerId="@item.CustomerId" />`를 사용하여 자식 구성 요소를 렌더링하는 경우(여기서 `CustomerId`는 `int` 값임) 자식 구성 요소는 `item.CustomerId`가 변경되는 경우에만 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="e8368-131">사용자 지정 모델 형식, 이벤트 콜백 또는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 값과 같은 기본이 아닌 형식 매개 변수 값을 허용해야 하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 재정의하여 렌더링 여부에 관한 결정을 제어할 수 있습니다. 관련 내용은 [`ShouldRender` 사용](#use-of-shouldrender) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="e8368-132">전체 하위 트리의 다시 렌더링을 건너뛰면 이벤트가 발생할 때 대부분의 렌더링 비용을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="e8368-133">특히 특정 UI 부분의 다시 렌더링을 건너뛸 수 있도록 자식 구성 요소를 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="e8368-134">이 방법은 부모 구성 요소의 렌더링 비용을 줄이는 유효한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="e8368-135">`ShouldRender` 사용</span><span class="sxs-lookup"><span data-stu-id="e8368-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="e8368-136">매개 변수 값과 관계없이 초기 렌더링 이후 변경되지 않는 UI 전용 구성 요소를 작성하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 `false`를 반환하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="e8368-137">매개 변수 값이 특정 방식으로 변경될 때만 구성 요소에 다시 렌더링이 필요한 경우에는 프라이빗 필드를 사용하여 변경 내용을 검색하는 데 필요한 정보를 추적할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="e8368-138">다음 예제에서 `shouldRender`는 렌더러를 발생시키는 모든 종류의 변경을 확인함에 따라 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="e8368-139">`prevOutboundFlightId` 및 `prevInboundFlightId`는 잠재적인 다음 업데이트에 관한 정보를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="e8368-140">앞의 코드에서 이벤트 처리기는 이벤트 후에 구성 요소가 다시 렌더링되도록 `shouldRender`를 `true`로 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="e8368-141">대부분 구성 요소의 경우 이 수준의 수동 제어가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="e8368-142">하위 트리가 특히 렌더링 비용이 많이 들고 UI 지연을 발생시키는 경우 해당 하위 트리의 다시 렌더링을 건너뛰는 경우만 신경을 쓰면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="e8368-143">자세한 내용은 <xref:blazor/components/lifecycle>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="e8368-144">가상화</span><span class="sxs-lookup"><span data-stu-id="e8368-144">Virtualization</span></span>

<span data-ttu-id="e8368-145">수천 개 항목을 포함하는 목록 또는 그리드와 같은 많은 양의 UI를 루프 내에서 렌더링할 때 렌더링 작업량이 많으면 UI 렌더링이 지연되므로 사용자 환경의 성능이 저하될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="e8368-146">사용자가 스크롤 없이 한 번에 적은 수의 요소만 볼 수 있다면 현재 표시되지 않는 요소를 렌더링하는 데 불필요하게 너무 많은 시간이 소요될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="e8368-147">이 문제를 해결하기 위해 Blazor는 임의로 커질 수 있는 목록의 모양 및 스크롤 동작을 만들지만 실제로는 현재 스크롤 뷰포트에 있는 목록 항목만 렌더링하는 기본 제공 [`<Virtualize>` 구성 요소](xref:blazor/components/virtualization)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-147">To address this, Blazor provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="e8368-148">예를 들어 이는 앱은 100,000개 항목을 포함하는 목록이 있지만 한 번에 표시되는 20개 항목의 렌더링 비용만 지급할 수 있음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="e8368-149">`<Virtualize>` 구성 요소를 사용하여 크기 순서별로 UI 성능을 스케일 업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="e8368-150">`<Virtualize>`를 사용할 수 있는 경우는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="e8368-151">루프에서 데이터 항목 세트를 렌더링하는 경우.</span><span class="sxs-lookup"><span data-stu-id="e8368-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="e8368-152">스크롤로 인해 대부분 항목이 표시되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="e8368-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="e8368-153">렌더링된 항목의 크기가 똑같은 경우.</span><span class="sxs-lookup"><span data-stu-id="e8368-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="e8368-154">사용자가 임의 지점으로 스크롤하면 구성 요소는 표시할 표시되는 항목을 계산할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="e8368-155">가상화되지 않은 목록의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="e8368-156">`allFlights` 컬렉션에 10,000개 항목이 포함된 경우 이 컬렉션은 10,000개 `<FlightSummary>` 구성 요소 인스턴스를 인스턴스화하고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="e8368-157">한편, 가상화된 목록의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="e8368-158">결과 UI가 사용자에게 동일하게 표시되더라도 백그라운드에서 구성 요소는 스크롤 가능한 영역을 채우는 데 필요한 만큼만 `<FlightSummary>` 인스턴스를 인스턴스화하고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="e8368-159">표시되는 `<FlightSummary>` 인스턴스 세트는 사용자가 스크롤할 때 다시 계산되고 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="e8368-160">`<Virtualize>`에는 다른 이점도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="e8368-161">예를 들어 구성 요소가 외부 API의 데이터를 요청하는 경우 `<Virtualize>`는 구성 요소가 컬렉션에서 모든 데이터를 다운로드하는 대신 현재 표시되는 영역에 해당하는 레코드 조각만 페치하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="e8368-162">자세한 내용은 <xref:blazor/components/virtualization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="e8368-163">간단하고 최적화된 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="e8368-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="e8368-164">대부분 Blazor 구성 요소는 강제로 최적화할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-164">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="e8368-165">대부분의 구성 요소가 UI에서 자주 반복되지 않고 빈번하게 다시 렌더링되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="e8368-166">예를 들어 `@page` 구성 요소와 대화 상자 또는 양식과 같은 상위 수준 UI 조각을 나타내는 구성 요소는 한 번에 하나만 표시되고 사용자 제스처에 대응해서만 다시 렌더링될 가능성이 큽니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="e8368-167">관련 구성 요소의 렌더링 워크로드는 많지 않으므로 렌더링 성능을 걱정하지 않고도 원하는 프레임워크 기능의 조합을 자유롭게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="e8368-168">그러나 대규모로 반복해야 하는 구성 요소를 빌드하는 일반적인 시나리오도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="e8368-169">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-169">For example:</span></span>

 * <span data-ttu-id="e8368-170">대규모 중첩된 양식에는 수백 개의 개별 입력, 레이블 및 기타 요소가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="e8368-171">그리드에는 수천 개의 셀이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="e8368-172">산점도에는 수백만 개의 데이터 요소가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="e8368-173">각 단위를 개별 구성 요소 인스턴스로 모델링하는 경우 많은 인스턴스의 렌더링 성능이 중요해집니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="e8368-174">이 섹션에서는 UI가 빠르고 뛰어난 응답성을 유지하도록 해당 구성 요소를 간단하게 만드는 방법에 관한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="e8368-175">수천 개의 구성 요소 인스턴스 방지</span><span class="sxs-lookup"><span data-stu-id="e8368-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="e8368-176">각 구성 요소는 부모 및 자식과 독립적으로 렌더링할 수 있는 분리된 섬입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="e8368-177">UI를 구성 요소 계층 구조로 분할하는 방법을 선택하면 UI 렌더링의 세분성을 제어하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="e8368-178">이 기능은 성능에 좋거나 나쁠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="e8368-179">UI를 더 많은 구성 요소로 분할하면 이벤트가 발생할 때 UI 렌더러 부분을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="e8368-180">예를 들어 사용자가 테이블 행의 단추를 클릭할 때 전체 페이지나 테이블 대신 단일 해당 행 렌더러만 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="e8368-181">그러나 각 추가 구성 요소에는 독립적인 상태 및 렌더링 수명 주기를 처리하기 위한 약간의 추가 메모리 및 CPU 오버헤드가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="e8368-182">.NET 5에서 Blazor WebAssembly의 성능을 튜닝할 때 구성 요소 인스턴스당 0.06ms 정도의 렌더링 오버헤드를 측정했습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-182">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="e8368-183">이는 일반적인 노트북에서 실행되는 세 개의 매개 변수를 허용하는 간단한 구성 요소를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="e8368-184">내부적으로 대부분 오버헤드는 사전에서 구성 요소별 상태를 검색하고 매개 변수를 전달 및 수신하기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="e8368-185">곱하기를 통해 2,000개 추가 구성 요소 인스턴스를 추가하면 렌더링 시간이 0.12초 늘어나고 사용자에게 UI가 느리게 느껴지는 것을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="e8368-186">더 많은 구성 요소를 포함할 수 있도록 구성 요소를 더 간단하게 만들 수 있지만, 너무 많은 구성 요소를 포함하지 않는 것이 보다 효과적인 기법입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="e8368-187">다음 섹션에서는 두 가지 접근 방식을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="e8368-188">자식 구성 요소를 부모에 인라인</span><span class="sxs-lookup"><span data-stu-id="e8368-188">Inline child components into their parents</span></span>

<span data-ttu-id="e8368-189">자식 구성 요소의 시퀀스를 렌더링하는 다음 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="e8368-190">앞의 예제 코드에서 `<ChatMessageDisplay>` 구성 요소는 다음을 포함하는 `ChatMessageDisplay.razor` 파일에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="e8368-191">앞의 예제는 수천 개의 메시지가 한 번에 표시되지 않는 한 제대로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="e8368-192">한 번에 수천 개의 메시지를 표시하려면 별도의 `ChatMessageDisplay` 구성 요소를 분리하지 ‘않는’ 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="e8368-193">대신, 렌더링을 부모에 직접 인라인합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="e8368-194">이렇게 하면 각 자식 구성 요소를 다시 렌더링할 수 없는 비용으로 너무 많은 자식 구성 요소를 렌더링하는 구성 요소별 오버헤드를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="e8368-195">코드에서 재사용 가능한 `RenderFragments` 정의</span><span class="sxs-lookup"><span data-stu-id="e8368-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="e8368-196">렌더링 논리를 다시 사용하는 방법으로 순수하게 자식 구성 요소를 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="e8368-197">이 경우 실제 구성 요소를 선언하지 않고도 렌더링 논리를 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="e8368-198">구성 요소의 `@code` 블록에서 UI를 내보내고 어디서나 호출할 수 있는 <xref:Microsoft.AspNetCore.Components.RenderFragment>를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="e8368-199">앞의 예제에서 설명한 대로 구성 요소는 `@code` 블록 내부 및 외부의 코드에서 태그를 내보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="e8368-200">이 접근 방식은 필요한 경우 여러 위치에서 구성 요소의 일반 렌더링 출력 내부에 렌더링할 수 있는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 대리자를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="e8368-201">Razor 컴파일러가 대리자의 렌더링 지침을 생성할 수 있도록 대리자는 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 형식의 `__builder`라는 매개 변수를 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="e8368-202">여러 구성 요소에서 대리자를 다시 사용하려면 `public static` 멤버로 선언하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="e8368-203">이제 관련이 없는 구성 요소에서 대리자를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="e8368-204">이 기법은 구성 요소별 오버헤드 없이 렌더링되는 재사용 가능한 태그 코드 조각의 라이브러리를 빌드하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="e8368-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> 대리자는 매개 변수를 허용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="e8368-206">이전 예제에서 `ChatMessageDisplay` 구성 요소에 해당하는 항목을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="e8368-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="e8368-207">이 접근 방식은 구성 요소별 오버헤드 없이 렌더링 논리를 다시 사용하는 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="e8368-208">그러나 UI의 하위 트리를 독립적으로 새로 고칠 수 있는 이점이 없으며 구성 요소 경계가 없기 때문에 부모를 렌더링할 때 UI의 해당 하위 트리 렌더링을 건너뛰는 기능도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="e8368-209">너무 많은 매개 변수를 수신하지 않음</span><span class="sxs-lookup"><span data-stu-id="e8368-209">Don't receive too many parameters</span></span>

<span data-ttu-id="e8368-210">구성 요소가 수백 또는 수천 번과 같이 너무 자주 반복되는 경우 각 매개 변수를 전달 및 수신하는 오버헤드가 누적된다는 점에 유의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="e8368-211">너무 많은 매개 변수가 성능을 크게 제한하는 경우는 드물지만 한 가지 요인이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="e8368-212">그리드 내에서 1,000번 렌더링되는 `<TableCell>` 구성 요소에 전달되는 각 추가 매개 변수는 총 렌더링 비용에 15ms 정도를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="e8368-213">각 셀이 10개의 매개 변수를 허용한 경우 매개 변수 전달에는 구성 요소 렌더링당 150ms 정도가 걸리므로 150,000ms(150초)의 매개 변수 전달만으로 인해 UI 지연이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="e8368-214">이 부하를 줄이기 위해 사용자 지정 클래스를 통해 여러 매개 변수를 묶을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="e8368-215">예를 들어 `<TableCell>` 구성 요소는 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="e8368-216">앞의 예제에서 `Data`는 모든 셀에서 서로 다르지만 `Options`는 모든 셀에서 공통적입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="e8368-217">물론 `<TableCell>` 구성 요소를 포함하지 않고 대신 논리를 부모 구성 요소에 인라인하도록 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="e8368-218">연계 매개 변수가 고정되어 있는지 확인</span><span class="sxs-lookup"><span data-stu-id="e8368-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="e8368-219">`<CascadingValue>` 구성 요소에는 `IsFixed`라는 선택적 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="e8368-220">`IsFixed` 값이 `false`(기본값)이면 연계된 값의 모든 수신자가 변경 알림을 받도록 구독을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="e8368-221">이 경우 각 `[CascadingParameter]`는 구독 추적으로 인해 일반 `[Parameter]`보다 **비용이 훨씬 더 많이 듭니다**.</span><span class="sxs-lookup"><span data-stu-id="e8368-221">In this case, each each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="e8368-222">`IsFixed` 값이 `true`이면(예: `<CascadingValue Value="@someValue" IsFixed="true">`) 수신자는 초기 값을 수신하지만 구독이 업데이트를 수신하도록 설정하지 ‘않습니다’.</span><span class="sxs-lookup"><span data-stu-id="e8368-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="e8368-223">이 경우 각 `[CascadingParameter]`는 간단하며 일반 `[Parameter]`보다 **비용이 더 많이 들지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="e8368-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="e8368-224">따라서 가능하면 항상 연계된 값에서 `IsFixed="true"`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="e8368-225">제공되는 값이 시간 경과에 따라 변경되지 않을 때마다 이 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="e8368-226">구성 요소가 연계된 값으로 `this`를 전달하는 일반적인 패턴에서는 `IsFixed="true"`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="e8368-227">이렇게 하면 연계된 값을 수신하는 많은 다른 구성 요소가 있는 경우 상당한 차이가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="e8368-228">자세한 내용은 <xref:blazor/components/cascading-values-and-parameters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="e8368-229">`CaptureUnmatchedValues`를 사용하여 특성 스플래팅 방지</span><span class="sxs-lookup"><span data-stu-id="e8368-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="e8368-230">구성 요소는 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 플래그를 사용하여 “일치하지 않는” 매개 변수 값을 수신하도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="e8368-231">이 접근 방식을 사용하면 임의 추가 특성을 통해 요소에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="e8368-232">그러나 렌더러는 다음을 충족해야 하므로 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="e8368-233">제공된 모든 매개 변수를 알려진 매개 변수 세트에 대조하여 사전을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="e8368-234">동일한 특성의 얼마나 많은 복사본이 서로 덮어쓰는지 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="e8368-235">자주 반복되지 않는 구성 요소와 같이 성능에 중요하지 않은 구성 요소에서 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>를 자유롭게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="e8368-236">그러나 큰 목록의 각 항목이나 그리드의 셀과 같이 대규모로 렌더링되는 구성 요소의 경우 특성 스플래팅을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="e8368-237">자세한 내용은 <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="e8368-238">수동으로 `SetParametersAsync` 구현</span><span class="sxs-lookup"><span data-stu-id="e8368-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="e8368-239">구성 요소별 렌더링 오버헤드의 주요 측면 중 하나는 들어오는 매개 변수 값을 `[Parameter]` 속성에 기록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="e8368-240">렌더러는 이 작업을 수행하는 데 리플렉션을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="e8368-241">이 기능은 어느 정도 최적화되어 있지만 WebAssembly 런타임에 대한 JIT 지원이 없으면 제한이 생깁니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="e8368-242">일부 극단적인 경우에는 리플렉션을 피하고 자체 매개 변수 설정 논리를 수동으로 구현하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="e8368-243">여기에 해당할 수 있는 경우는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-243">This may be applicable when:</span></span>

 * <span data-ttu-id="e8368-244">너무 자주 렌더링되는 구성 요소가 있는 경우(예: UI에는 수백 또는 수천 개의 복사본이 있는 경우).</span><span class="sxs-lookup"><span data-stu-id="e8368-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="e8368-245">많은 매개 변수를 허용하는 경우.</span><span class="sxs-lookup"><span data-stu-id="e8368-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="e8368-246">매개 변수를 수신하는 오버헤드가 UI 응답성에 관찰 가능한 영향을 미치는 것을 알게 된 경우.</span><span class="sxs-lookup"><span data-stu-id="e8368-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="e8368-247">해당하는 경우 구성 요소의 가상 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 메서드를 재정의하고 자체 구성 요소별 논리를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="e8368-248">다음 예제에서는 의도적으로 사전 조회를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="e8368-249">앞의 코드에서 기본 클래스 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>를 반환하면 매개 변수를 다시 할당하지 않고도 일반 수명 주기 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="e8368-250">앞의 코드에서 볼 수 있듯이 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>를 재정의하고 사용자 지정 논리를 제공하는 것은 복잡하고 힘들기 때문에 일반적으로 이 접근 방식을 권장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="e8368-251">극단적인 경우에는 렌더링 성능을 20~25%까지 개선할 수 있지만 이 접근 방식은 이전에 나열된 시나리오에서만 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="e8368-252">이벤트를 너무 빨리 트리거하지 않음</span><span class="sxs-lookup"><span data-stu-id="e8368-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="e8368-253">일부 브라우저 이벤트는 너무 자주 실행됩니다. 예를 들어 `onmousemove` 및 `onscroll`은 초당 수십 또는 수백 번 실행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="e8368-254">대부분의 경우 UI 업데이트는 이렇게 자주 수행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="e8368-255">이를 시도하면 UI 응답성이 손상되거나 과도한 CPU 시간이 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="e8368-256">네이티브 `@onmousemove` 또는 `@onscroll` 이벤트를 사용하는 대신 JS interop을 사용하여 덜 자주 실행되는 콜백을 등록하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="e8368-257">예를 들어 다음 구성 요소(`MyComponent.razor`)는 마우스의 위치를 표시하지만 500ms마다 한 번만 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="e8368-258">`index.html` 페이지에 배치되거나 ES6 모듈로 로드될 수 있는 해당 JavaScript 코드는 실제 DOM 이벤트 수신기를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="e8368-259">이 예제에서 이벤트 수신기는 [Lodash의 `throttle` 함수](https://lodash.com/docs/4.17.15#throttle)를 사용하여 호출 빈도를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="e8368-260">이벤트를 호출할 때마다 네트워크를 통해 메시지가 전달되므로 이 기법은 Blazor Server에 훨씬 더 중요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-260">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="e8368-261">렌더링 작업량을 크게 줄일 수 있으므로 Blazor WebAssembly에 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-261">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="e8368-262">JavaScript interop 속도 최적화</span><span class="sxs-lookup"><span data-stu-id="e8368-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="e8368-263">.NET과 JavaScript 간 호출에는 다음과 같은 이유로 일부 오버헤드가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="e8368-264">기본적으로 호출은 비동기적입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="e8368-265">기본적으로 매개 변수 및 반환 값은 JSON 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="e8368-266">이 기능은 .NET 형식과 JavaScript 형식 간에 이해하기 쉬운 변환 메커니즘을 제공하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="e8368-267">또한 Blazor Server에서는 관련 호출이 네트워크를 통해 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-267">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="e8368-268">과도하게 세분화된 호출 방지</span><span class="sxs-lookup"><span data-stu-id="e8368-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="e8368-269">각 호출에는 약간의 오버헤드가 포함되므로 호출 수를 줄이는 것이 중요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="e8368-270">브라우저의 `localStorage` 저장소에 항목 컬렉션을 저장하는 다음 코드를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="e8368-271">앞의 예제에서는 각 항목에 대해 별도의 JS interop 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="e8368-272">대신, 다음 접근 방식은 단일 호출에 대한 JS interop을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="e8368-273">해당 JavaScript 함수는 다음과 같이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="e8368-274">Blazor WebAssembly의 경우 이 함수는 일반적으로 많은 JS interop 호출을 수행하는 경우에만 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-274">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="e8368-275">동기 호출 수행 고려</span><span class="sxs-lookup"><span data-stu-id="e8368-275">Consider making synchronous calls</span></span>

<span data-ttu-id="e8368-276">호출되는 코드가 동기 또는 비동기인지와 관계없이 JavaScript interop 호출은 기본적으로 비동기적입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="e8368-277">이 기능은 구성 요소가 Blazor WebAssembly 및 Blazor Server와 둘 다 호환되는지 확인하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-277">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="e8368-278">Blazor Server에서 모든 JavaScript interop 호출은 네트워크 연결을 통해 전송되므로 비동기적이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-278">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="e8368-279">앱이 Blazor WebAssembly에서만 실행되는 것을 확실히 알고 있으면 동기 JavaScript interop 호출을 수행하도록 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-279">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="e8368-280">이렇게 하면 비동기 호출을 수행하는 것보다 오버헤드가 약간 감소하며 결과를 기다리는 동안 중간 상태가 없기 때문에 렌더링 주기가 감소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="e8368-281">.NET에서 JavaScript로 동기 호출을 수행하려면 <xref:Microsoft.JSInterop.IJSRuntime>을 <xref:Microsoft.JSInterop.IJSInProcessRuntime>으로 캐스팅합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e8368-282">`IJSObjectReference`를 사용하는 경우 `IJSInProcessObjectReference`로 캐스팅하여 동기 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="e8368-283">JavaScript에서 .NET으로 동기 호출을 수행하려면 `DotNet.invokeMethodAsync` 대신 `DotNet.invokeMethod`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="e8368-284">동기 호출이 작동하는 경우는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="e8368-285">앱이 Blazor Server가 아닌 Blazor WebAssembly에서 실행되는 경우.</span><span class="sxs-lookup"><span data-stu-id="e8368-285">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="e8368-286">호출된 함수가 동기적으로 값을 반환하는 경우(`async` 메서드가 아니며 .NET <xref:System.Threading.Tasks.Task> 또는 JavaScript `Promise`를 반환하지 않음).</span><span class="sxs-lookup"><span data-stu-id="e8368-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="e8368-287">자세한 내용은 <xref:blazor/call-javascript-from-dotnet>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="e8368-288">역 마샬링된 호출 수행 고려</span><span class="sxs-lookup"><span data-stu-id="e8368-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="e8368-289">Blazor WebAssembly에서 실행되는 경우 .NET에서 JavaScript로 역 마샬링된 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-289">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="e8368-290">해당 동기 호출은 인수 또는 반환 값의 JSON serialization을 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="e8368-291">.NET 표현과 JavaScript 표현 간 메모리 관리 및 변환의 모든 측면은 개발자가 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="e8368-292">`IJSUnmarshalledRuntime`을 사용하는 것이 JS interop 접근 방식 중에서 오버헤드가 가장 덜 발생하지만, 이러한 API와 상호 작용하는 데 필요한 JavaScript API는 현재 문서화되지 않은 상태이며 이후 릴리스에서 호환성이 손상되는 변경이 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="e8368-293">앱 다운로드 크기 최소화</span><span class="sxs-lookup"><span data-stu-id="e8368-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="e8368-294">IL(중간 언어) 트리밍</span><span class="sxs-lookup"><span data-stu-id="e8368-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="e8368-295">[Blazor WebAssembly 앱에서 사용되지 않는 어셈블리를 트리밍](xref:blazor/host-and-deploy/configure-trimmer)하면 앱의 이진 파일에서 사용되지 않는 코드를 제거하여 앱 크기를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-295">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="e8368-296">기본적으로 트리머는 애플리케이션을 게시할 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="e8368-297">트리밍의 장점을 활용하려면 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령에서 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 옵션을 `Release`로 설정하여 배포용 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="e8368-298">IL(중간 언어) 연결</span><span class="sxs-lookup"><span data-stu-id="e8368-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="e8368-299">[Blazor WebAssembly 앱을 연결](xref:blazor/host-and-deploy/configure-linker)하면 앱의 이진 파일에서 사용되지 않는 코드를 잘라내어 앱 크기를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-299">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="e8368-300">기본적으로 IL(중간 언어) 링커는 `Release` 구성으로 빌드할 때만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="e8368-301">이 기능의 이점을 활용하려면 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령에서 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 옵션을 `Release`로 설정하여 배포를 위해 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="e8368-302">System.Text.Json 사용</span><span class="sxs-lookup"><span data-stu-id="e8368-302">Use System.Text.Json</span></span>

<span data-ttu-id="e8368-303">Blazor의 JS interop 구현은 메모리 할당이 작은 고성능 JSON serialization 라이브러리인 <xref:System.Text.Json>에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-303">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="e8368-304"><xref:System.Text.Json>을 사용해도 하나 이상의 대체 JSON 라이브러리를 추가하는 것에 비해 추가 앱 페이로드 크기가 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="e8368-305">마이그레이션 지침은 [`Newtonsoft.Json`에서 `System.Text.Json`으로 마이그레이션하는 방법](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="e8368-306">어셈블리 지연 로드</span><span class="sxs-lookup"><span data-stu-id="e8368-306">Lazy load assemblies</span></span>

<span data-ttu-id="e8368-307">어셈블리가 경로에 필요한 경우 런타임에 어셈블리를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="e8368-308">자세한 내용은 <xref:blazor/webassembly-lazy-load-assemblies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="e8368-309">압축</span><span class="sxs-lookup"><span data-stu-id="e8368-309">Compression</span></span>

<span data-ttu-id="e8368-310">Blazor WebAssembly 앱이 게시될 때 게시하는 도중에 출력을 정적으로 압축하여 앱의 크기를 줄이고 런타임 압축의 오버헤드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-310">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="e8368-311">Blazor는 콘텐츠 협상을 수행하고 정적으로 압축된 파일을 처리하기 위해 서버에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-311">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="e8368-312">앱이 배포된 후에는 앱이 압축된 파일을 처리하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="e8368-313">브라우저의 개발자 도구에서 네트워크 탭을 조사하여 파일이 `Content-Encoding: br` 또는 `Content-Encoding: gz`로 처리되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="e8368-314">호스트가 압축된 파일을 처리하지 않는 경우 <xref:blazor/host-and-deploy/webassembly#compression>의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="e8368-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="e8368-315">사용되지 않는 기능을 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="e8368-315">Disable unused features</span></span>

<span data-ttu-id="e8368-316">Blazor WebAssembly의 런타임에는 앱에서 더 작은 페이로드 크기를 위해 해당 기능이 필요하지 않은 경우 사용하지 않도록 설정할 수 있는 다음과 같은 .NET 기능이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-316">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="e8368-317">정확한 표준 시간대 정보를 위해 데이터 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="e8368-318">앱에서 이 기능이 필요하지 않은 경우 앱의 프로젝트 파일에서 `BlazorEnableTimeZoneSupport` MSBuild 속성을 `false`로 설정하여 사용하지 않도록 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-318">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e8368-319">기본적으로 Blazor WebAssembly는 날짜 및 통화와 같은 값을 사용자의 문화권에 표시하는 데 필요한 세계화 리소스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-319">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="e8368-320">앱에 지역화가 필요하지 않은 경우 `en-US` 문화권을 기반으로 하는 [고정 문화권을 지원하도록 앱을 구성](xref:blazor/globalization-localization)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e8368-321"><xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>와 같은 API의 올바른 작동을 위해 데이터 정렬 정보가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="e8368-322">앱에서 데이터 정렬 데이터가 필요하지 않은 경우 앱의 프로젝트 파일에서 `BlazorWebAssemblyPreserveCollationData` MSBuild 속성을 `false`로 설정하여 사용하지 않도록 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e8368-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
