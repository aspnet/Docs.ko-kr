---
title: ASP.NET Core Blazor 구성 요소 가상화
author: guardrex
description: ASP.NET Core Blazor 앱에서 구성 요소 가상화를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 72b33bc3c2861380551915b1e8caab49122e8fab
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529919"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="9ade9-103">ASP.NET Core Blazor 구성 요소 가상화</span><span class="sxs-lookup"><span data-stu-id="9ade9-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="9ade9-104">작성자: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9ade9-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9ade9-105">Blazor 프레임워크의 기본 제공 가상화 지원을 사용하여 구성 요소 렌더링의 인식된 성능을 향상합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="9ade9-106">가상화는 UI 렌더링을 현재 표시되는 부분으로만 제한하는 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="9ade9-107">예를 들어 가상화는 앱에서 긴 항목 목록을 렌더링해야 하고 지정된 시간에 항목의 하위 집합만 표시해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="9ade9-108">Blazor는 앱의 구성 요소에 가상화를 추가하는 데 사용할 수 있는 [`Virtualize` 구성 요소](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="9ade9-109">`Virtualize` 구성 요소는 다음과 같은 경우에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-109">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="9ade9-110">루프에서 데이터 항목 세트를 렌더링하는 경우.</span><span class="sxs-lookup"><span data-stu-id="9ade9-110">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="9ade9-111">스크롤로 인해 대부분 항목이 표시되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="9ade9-111">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="9ade9-112">렌더링된 항목의 크기가 똑같은 경우.</span><span class="sxs-lookup"><span data-stu-id="9ade9-112">The rendered items are exactly the same size.</span></span> <span data-ttu-id="9ade9-113">사용자가 임의 지점으로 스크롤하면 구성 요소는 표시할 표시되는 항목을 계산할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-113">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="9ade9-114">가상화를 사용하지 않는 경우 일반적인 목록은 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 사용하여 목록의 각 항목을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-114">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="9ade9-115">목록에 수천 개의 항목이 포함된 경우 목록을 렌더링하는 데 시간이 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-115">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="9ade9-116">따라서 사용자가 눈에 띄는 UI 지연을 경험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-116">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="9ade9-117">목록의 각 항목을 한 번에 모두 렌더링하는 대신 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 `Virtualize` 구성 요소로 바꾸고 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>로 고정된 항목 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-117">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9ade9-118">현재 표시되는 항목만 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-118">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="9ade9-119">`Context`로 구성 요소에 대한 컨텍스트를 지정하지 않은 경우 항목 콘텐츠 템플릿에서 `context` 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-119">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="9ade9-120">요소 및 구성 요소에 모델 개체를 매핑하는 프로세스는 [`@key`](xref:mvc/views/razor#key) 지시문 특성을 사용하여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-120">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="9ade9-121">`@key`를 사용하면 diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소가 유지되도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-121">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="9ade9-122">자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9ade9-122">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="9ade9-123">ASP.NET Core용 Razor 구문 참조</span><span class="sxs-lookup"><span data-stu-id="9ade9-123">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="9ade9-124">`Virtualize` 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-124">The `Virtualize` component:</span></span>

* <span data-ttu-id="9ade9-125">컨테이너의 높이와 렌더링된 항목의 크기에 따라 렌더링할 항목의 수를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-125">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="9ade9-126">사용자가 스크롤하면 항목을 다시 계산하고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-126">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="9ade9-127">컬렉션에서 모든 데이터를 다운로드하는 대신 현재 표시되는 영역에 해당하는 외부 API의 레코드 조각만 페치합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-127">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="9ade9-128">`Virtualize` 구성 요소에 대한 항목 콘텐츠에는 다음이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-128">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="9ade9-129">이전 예제처럼 일반 HTML 및 Razor 코드</span><span class="sxs-lookup"><span data-stu-id="9ade9-129">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="9ade9-130">하나 이상의 Razor 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9ade9-130">One or more Razor components.</span></span>
* <span data-ttu-id="9ade9-131">HTML/Razor 및 Razor 구성 요소의 혼합</span><span class="sxs-lookup"><span data-stu-id="9ade9-131">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="9ade9-132">항목 공급자 대리자</span><span class="sxs-lookup"><span data-stu-id="9ade9-132">Item provider delegate</span></span>

<span data-ttu-id="9ade9-133">일부 항목을 메모리로 로드하지 않으려는 경우 항목 공급자 대리자 메서드를 요청 시 요청된 항목을 비동기적으로 검색하는 구성 요소의 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 매개 변수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-133">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="9ade9-134">다음 예제에서 `LoadEmployees` 메서드는 `Virtualize` 구성 요소에 항목을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-134">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="9ade9-135">항목 공급자는 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>를 수신하고 필요한 항목 수를 특정 시작 인덱스에서 시작하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-135">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="9ade9-136">그런 다음 항목 공급자는 데이터베이스나 다른 서비스에서 요청된 항목을 검색하여 총 항목 수와 함께 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601>으로 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-136">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="9ade9-137">항목 공급자는 각 요청으로 항목을 검색하거나 캐시하여 쉽게 사용 가능하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-137">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="9ade9-138">`Virtualize` 구성 요소는 매개 변수에서 **한 항목 원본** 만 허용할 수 있으므로 항목 공급자를 동시에 사용하여 컬렉션을 `Items`에 할당하려 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9ade9-138">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="9ade9-139">둘 다 할당되면 런타임에 구성 요소의 매개 변수가 설정될 때 <xref:System.InvalidOperationException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-139">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="9ade9-140">다음 `LoadEmployees` 메서드 예제는 `EmployeeService`(표시되지 않음)에서 직원을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-140">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="9ade9-141"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType>는 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>에서 데이터를 다시 요청하도록 구성 요소에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-141"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="9ade9-142">이는 외부 데이터가 변경되는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-142">This is useful when external data changes.</span></span> <span data-ttu-id="9ade9-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>를 사용하는 경우 이를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-143">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="9ade9-144">자리표시자</span><span class="sxs-lookup"><span data-stu-id="9ade9-144">Placeholder</span></span>

<span data-ttu-id="9ade9-145">원격 데이터 소스에서 항목을 요청하면 시간이 걸릴 수 있으므로 항목 콘텐츠를 사용하여 자리 표시자를 렌더링하는 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-145">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="9ade9-146"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A>(`<Placeholder>...</Placeholder>`)를 사용하여 항목 데이터를 사용할 수 있을 때까지 콘텐츠를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-146">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="9ade9-147"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType>를 사용하여 목록의 항목 템플릿을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-147">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="9ade9-148">항목 크기</span><span class="sxs-lookup"><span data-stu-id="9ade9-148">Item size</span></span>

<span data-ttu-id="9ade9-149">각 항목의 높이(픽셀 단위)를 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType>(기본값: 50):</span><span class="sxs-lookup"><span data-stu-id="9ade9-149">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="9ade9-150">오버스캔 개수</span><span class="sxs-lookup"><span data-stu-id="9ade9-150">Overscan count</span></span>

<span data-ttu-id="9ade9-151"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType>는 표시되는 지역 앞뒤에 렌더링되는 추가 항목 수를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-151"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="9ade9-152">이 설정은 스크롤 중 렌더링 빈도를 줄이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-152">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="9ade9-153">그러나 값이 높을수록 페이지에서 더 많은 요소가 렌더링됩니다(기본값: 3).</span><span class="sxs-lookup"><span data-stu-id="9ade9-153">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="9ade9-154">상태 변경</span><span class="sxs-lookup"><span data-stu-id="9ade9-154">State changes</span></span>

<span data-ttu-id="9ade9-155">`Virtualize` 구성 요소에 의해 렌더링된 항목을 변경하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하여 강제로 구성 요소를 재평가하고 다시 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="9ade9-155">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="9ade9-156">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9ade9-156">For more information, see <xref:blazor/components/rendering>.</span></span>
