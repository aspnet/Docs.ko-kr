---
title: ASP.NET Core Blazor 구성 요소 가상화
author: guardrex
description: ASP.NET Core Blazor 앱에서 구성 요소 가상화를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2021
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
ms.openlocfilehash: c81732c29b262e9134a4ff7dab077a4f31db96af
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109821"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="aa433-103">ASP.NET Core Blazor 구성 요소 가상화</span><span class="sxs-lookup"><span data-stu-id="aa433-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="aa433-104">[`Virtualize` 구성 요소](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)와 함께 Blazor 프레임워크의 기본 제공 가상화 지원을 사용하여 구성 요소 렌더링의 인식된 성능을 향상합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support with the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span></span> <span data-ttu-id="aa433-105">가상화는 UI 렌더링을 현재 표시되는 부분으로만 제한하는 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="aa433-106">예를 들어 가상화는 앱에서 긴 항목 목록을 렌더링해야 하고 지정된 시간에 항목의 하위 집합만 표시해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span>

<span data-ttu-id="aa433-107">`Virtualize` 구성 요소를 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="aa433-107">Use the `Virtualize` component when:</span></span>

* <span data-ttu-id="aa433-108">루프에서 데이터 항목 세트를 렌더링하는 경우.</span><span class="sxs-lookup"><span data-stu-id="aa433-108">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="aa433-109">스크롤로 인해 대부분 항목이 표시되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="aa433-109">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="aa433-110">렌더링된 항목의 크기가 동일한 경우.</span><span class="sxs-lookup"><span data-stu-id="aa433-110">The rendered items are the same size.</span></span>

<span data-ttu-id="aa433-111">사용자가 `Virtualize` 구성 요소의 항목 목록에 있는 임의의 지점으로 스크롤하면, 구성 요소는 어떤 항목을 표시할지를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-111">When the user scrolls to an arbitrary point in the `Virtualize` component's list of items, the component calculates the visible items to show.</span></span> <span data-ttu-id="aa433-112">보이지 않는 항목은 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-112">Unseen items aren't rendered.</span></span>

<span data-ttu-id="aa433-113">가상화를 사용하지 않는 경우 일반적인 목록은 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 사용하여 목록의 각 항목을 렌더링할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in a list.</span></span> <span data-ttu-id="aa433-114">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="aa433-114">In the following example:</span></span>

* <span data-ttu-id="aa433-115">`allFlights`는 비행기 항공편의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-115">`allFlights` is a collection of airplane flights.</span></span>
* <span data-ttu-id="aa433-116">`FlightSummary` 구성 요소는 각 항공편에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-116">The `FlightSummary` component displays details about each flight.</span></span>
* <span data-ttu-id="aa433-117">[`@key` 지시문 특성](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)은 각 `FlightSummary` 구성 요소와 항공편의 `FlightId`에 의해 렌더링된 항공편의 관계를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-117">The [`@key` directive attribute](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) preserves the relationship of each `FlightSummary` component to its rendered flight by the flight's `FlightId`.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="aa433-118">컬렉션에 수천 개의 항공편이 포함되어 있으면 항공편을 렌더링하는 데 시간이 오래 걸리고 사용자는 UI에서 큰 지연을 경험하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-118">If the collection contains thousands of flights, rendering the flights takes a long time and users experience a noticeable UI lag.</span></span> <span data-ttu-id="aa433-119">대부분의 항공편은 `<div>` 요소의 높이를 벗어나므로 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-119">Most of the flights aren't rendered because they fall outside of the height of the `<div>` element.</span></span>

<span data-ttu-id="aa433-120">항공편의 전체 목록을 동시에 렌더링하는 대신 앞의 예제에 있는 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 `Virtualize` 구성 요소로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-120">Instead of rendering the entire list of flights at once, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop in the preceding example with the `Virtualize` component:</span></span>

* <span data-ttu-id="aa433-121">`allFlights`를 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>에 대한 고정 항목 원본으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-121">Specify `allFlights` as a fixed item source to <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="aa433-122">현재 표시되는 항공편만 `Virtualize` 구성 요소에 의해 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-122">Only the currently visible flights are rendered by the `Virtualize` component.</span></span>
* <span data-ttu-id="aa433-123">`Context` 매개 변수를 사용하여 각 항공편에 대한 컨텍스트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-123">Specify a context for each flight with the `Context` parameter.</span></span> <span data-ttu-id="aa433-124">다음 예제에서 `flight`는 각 항공편 멤버에 대한 액세스를 제공하는 컨텍스트로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-124">In the following example, `flight` is used as the context, which provides access to each flight's members.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="aa433-125">`Context` 매개 변수로 컨텍스트를 지정하지 않은 경우, 각 항공편의 멤버에 액세스하려면 항목 콘텐츠 템플릿에서 `context` 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-125">If a context isn't specified with the `Context` parameter, use the value of `context` in the item content template to access each flight's members:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="aa433-126">`Virtualize` 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-126">The `Virtualize` component:</span></span>

* <span data-ttu-id="aa433-127">컨테이너의 높이와 렌더링된 항목의 크기에 따라 렌더링할 항목의 수를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-127">Calculates the number of items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="aa433-128">사용자가 스크롤하면 항목을 다시 계산하고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-128">Recalculates and rerenders the items as the user scrolls.</span></span>
* <span data-ttu-id="aa433-129">컬렉션에서 모든 데이터를 다운로드하는 대신 현재 표시되는 영역에 해당하는 외부 API의 레코드 조각만 페치합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-129">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="aa433-130">`Virtualize` 구성 요소에 대한 항목 콘텐츠에는 다음이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-130">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="aa433-131">이전 예제처럼 일반 HTML 및 Razor 코드</span><span class="sxs-lookup"><span data-stu-id="aa433-131">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="aa433-132">하나 이상의 Razor 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aa433-132">One or more Razor components.</span></span>
* <span data-ttu-id="aa433-133">HTML/Razor 및 Razor 구성 요소의 혼합</span><span class="sxs-lookup"><span data-stu-id="aa433-133">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="aa433-134">항목 공급자 대리자</span><span class="sxs-lookup"><span data-stu-id="aa433-134">Item provider delegate</span></span>

<span data-ttu-id="aa433-135">일부 항목을 메모리로 로드하지 않으려는 경우 항목 공급자 대리자 메서드를 요청 시 요청된 항목을 비동기적으로 검색하는 구성 요소의 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 매개 변수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-135">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="aa433-136">다음 예제에서 `LoadEmployees` 메서드는 `Virtualize` 구성 요소에 항목을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-136">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="aa433-137">항목 공급자는 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>를 수신하고 필요한 항목 수를 특정 시작 인덱스에서 시작하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-137">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="aa433-138">그런 다음 항목 공급자는 데이터베이스나 다른 서비스에서 요청된 항목을 검색하여 총 항목 수와 함께 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601>으로 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-138">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="aa433-139">항목 공급자는 각 요청으로 항목을 검색하거나 캐시하여 쉽게 사용 가능하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-139">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="aa433-140">`Virtualize` 구성 요소는 매개 변수에서 **한 항목 원본** 만 허용할 수 있으므로 항목 공급자를 동시에 사용하여 컬렉션을 `Items`에 할당하려 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="aa433-140">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="aa433-141">둘 다 할당되면 런타임에 구성 요소의 매개 변수가 설정될 때 <xref:System.InvalidOperationException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-141">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="aa433-142">다음 `LoadEmployees` 메서드 예제는 `EmployeeService`(표시되지 않음)에서 직원을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-142">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="aa433-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType>는 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>에서 데이터를 다시 요청하도록 구성 요소에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="aa433-144">이는 외부 데이터가 변경되는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-144">This is useful when external data changes.</span></span> <span data-ttu-id="aa433-145"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>를 사용하는 경우 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A>를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-145">There's no need to call <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="aa433-146">자리표시자</span><span class="sxs-lookup"><span data-stu-id="aa433-146">Placeholder</span></span>

<span data-ttu-id="aa433-147">원격 데이터 소스에서 항목을 요청하면 시간이 걸릴 수 있으므로 항목 콘텐츠를 사용하여 자리 표시자를 렌더링하는 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-147">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="aa433-148"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A>(`<Placeholder>...</Placeholder>`)를 사용하여 항목 데이터를 사용할 수 있을 때까지 콘텐츠를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-148">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="aa433-149"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType>를 사용하여 목록의 항목 템플릿을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-149">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="aa433-150">항목 크기</span><span class="sxs-lookup"><span data-stu-id="aa433-150">Item size</span></span>

<span data-ttu-id="aa433-151">각 항목의 높이(픽셀 단위)를 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType>(기본값: 50)으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-151">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50).</span></span> <span data-ttu-id="aa433-152">다음 예에서는 각 항목의 높이를 기본값인 50픽셀에서 25픽셀로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-152">The following example changes the height of each item from the default of 50 pixels to 25 pixels:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="aa433-153">기본적으로 `Virtualize` 구성 요소는 초기 렌더링이 수행된 ‘후’에 개별 항목의 렌더링 크기(높이)를 측정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-153">By default, the `Virtualize` component measures the rendering size (height) of individual items *after* the initial render occurs.</span></span> <span data-ttu-id="aa433-154">정확한 초기 렌더링 성능을 지원하고 페이지 다시 로드에 적합한 스크롤 위치를 보장하려면 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A>를 사용하여 정확한 항목 크기를 미리 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-154">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="aa433-155">기본 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A>를 사용하여 일부 항목이 현재 표시된 뷰 외부에서 렌더링되는 경우 두 번째 다시 렌더링이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-155">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="aa433-156">가상화된 목록에서 브라우저의 스크롤 위치를 올바르게 유지 관리하려면 초기 렌더링이 정확해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-156">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="aa433-157">그렇지 않으면 사용자에게 잘못된 항목이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-157">If not, users might view the wrong items.</span></span>

## <a name="overscan-count"></a><span data-ttu-id="aa433-158">오버스캔 개수</span><span class="sxs-lookup"><span data-stu-id="aa433-158">Overscan count</span></span>

<span data-ttu-id="aa433-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType>는 표시되는 지역 앞뒤에 렌더링되는 추가 항목 수를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="aa433-160">이 설정은 스크롤 중 렌더링 빈도를 줄이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-160">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="aa433-161">그러나 값이 클수록 페이지에서 더 많은 요소가 렌더링됩니다(기본값: 3).</span><span class="sxs-lookup"><span data-stu-id="aa433-161">However, higher values result in more elements rendered in the page (default: 3).</span></span> <span data-ttu-id="aa433-162">다음 예제에서는 overscan count를 기본값인 3개 항목에서 4개 항목으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-162">The following example changes the overscan count from the default of three items to four items:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="aa433-163">상태 변경</span><span class="sxs-lookup"><span data-stu-id="aa433-163">State changes</span></span>

<span data-ttu-id="aa433-164">`Virtualize` 구성 요소에 의해 렌더링된 항목을 변경하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하여 강제로 구성 요소를 재평가하고 다시 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="aa433-164">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="aa433-165">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa433-165">For more information, see <xref:blazor/components/rendering>.</span></span>
