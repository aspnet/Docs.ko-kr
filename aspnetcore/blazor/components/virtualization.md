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
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753098"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="2bbce-103">ASP.NET Core Blazor 구성 요소 가상화</span><span class="sxs-lookup"><span data-stu-id="2bbce-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="2bbce-104">작성자: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2bbce-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2bbce-105">Blazor 프레임워크의 기본 제공 가상화 지원을 사용하여 구성 요소 렌더링의 인식된 성능을 향상합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="2bbce-106">가상화는 UI 렌더링을 현재 표시되는 부분으로만 제한하는 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="2bbce-107">예를 들어 가상화는 앱에서 긴 항목 목록을 렌더링해야 하고 지정된 시간에 항목의 하위 집합만 표시해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="2bbce-108">Blazor는 앱의 구성 요소에 가상화를 추가하는 데 사용할 수 있는 `Virtualize` 구성 요소를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="2bbce-109">가상화를 사용하지 않는 경우 일반적인 목록은 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 사용하여 목록의 각 항목을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="2bbce-110">목록에 수천 개의 항목이 포함된 경우 목록을 렌더링하는 데 시간이 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="2bbce-111">따라서 사용자가 눈에 띄는 UI 지연을 경험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="2bbce-112">목록의 각 항목을 한 번에 모두 렌더링하는 대신 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 `Virtualize` 구성 요소로 바꾸고 `Items`로 고정된 항목 원본을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="2bbce-113">현재 표시되는 항목만 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="2bbce-114">`Context`로 구성 요소에 대한 컨텍스트를 지정하지 않은 경우 항목 콘텐츠 템플릿에서 `context` 값(`@context.{PROPERTY}`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="2bbce-115">`Virtualize` 구성 요소는 컨테이너의 높이와 렌더링된 항목의 크기에 따라 렌더링할 항목의 수를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="2bbce-116">`Virtualize` 구성 요소에 대한 항목 콘텐츠에는 다음이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="2bbce-117">이전 예제처럼 일반 HTML 및 Razor 코드</span><span class="sxs-lookup"><span data-stu-id="2bbce-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="2bbce-118">하나 이상의 Razor 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2bbce-118">One or more Razor components.</span></span>
* <span data-ttu-id="2bbce-119">HTML/Razor 및 Razor 구성 요소의 혼합</span><span class="sxs-lookup"><span data-stu-id="2bbce-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="2bbce-120">항목 공급자 대리자</span><span class="sxs-lookup"><span data-stu-id="2bbce-120">Item provider delegate</span></span>

<span data-ttu-id="2bbce-121">일부 항목을 메모리로 로드하지 않으려는 경우 항목 공급자 대리자 메서드를 요청 시 요청된 항목을 비동기적으로 검색하는 구성 요소의 `ItemsProvider` 매개 변수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="2bbce-122">항목 공급자는 `ItemsProviderRequest`를 수신하고 필요한 항목 수를 특정 시작 인덱스에서 시작하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="2bbce-123">그런 다음 항목 공급자는 데이터베이스나 다른 서비스에서 요청된 항목을 검색하여 총 항목 수와 함께 `ItemsProviderResult<TItem>`으로 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="2bbce-124">항목 공급자는 각 요청으로 항목을 검색하거나 캐시하여 쉽게 사용 가능하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="2bbce-125">`Virtualize` 구성 요소는 매개 변수에서 **한 항목 원본** 만 허용할 수 있으므로 항목 공급자를 동시에 사용하여 컬렉션을 `Items`에 할당하려 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2bbce-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="2bbce-126">둘 다 할당되면 런타임에 구성 요소의 매개 변수가 설정될 때 <xref:System.InvalidOperationException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="2bbce-127">다음 예제에서는 `EmployeeService`에서 직원을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-127">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="2bbce-128">자리표시자</span><span class="sxs-lookup"><span data-stu-id="2bbce-128">Placeholder</span></span>

<span data-ttu-id="2bbce-129">원격 데이터 원본에서 항목을 요청하면 시간이 걸릴 수 있으므로 항목 데이터를 사용할 수 있을 때까지 자리 표시자(`<Placeholder>...</Placeholder>`)를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-129">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="2bbce-130">항목 크기</span><span class="sxs-lookup"><span data-stu-id="2bbce-130">Item size</span></span>

<span data-ttu-id="2bbce-131">각 항목의 크기(픽셀 단위)를 `ItemSize`(기본값: 50px)로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-131">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="2bbce-132">오버스캔 개수</span><span class="sxs-lookup"><span data-stu-id="2bbce-132">Overscan count</span></span>

<span data-ttu-id="2bbce-133">`OverscanCount`는 표시되는 지역 앞뒤에 렌더링되는 추가 항목 수를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-133">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="2bbce-134">이 설정은 스크롤 중 렌더링 빈도를 줄이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-134">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="2bbce-135">그러나 값이 높을수록 페이지에서 더 많은 요소가 렌더링됩니다(기본값: 3).</span><span class="sxs-lookup"><span data-stu-id="2bbce-135">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="2bbce-136">상태 변경</span><span class="sxs-lookup"><span data-stu-id="2bbce-136">State changes</span></span>

<span data-ttu-id="2bbce-137">`Virtualize` 구성 요소에 의해 렌더링된 항목을 변경하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하여 강제로 구성 요소를 재평가하고 다시 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbce-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
