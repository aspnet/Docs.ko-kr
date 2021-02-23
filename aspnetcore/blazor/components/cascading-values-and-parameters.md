---
title: ASP.NET Core Blazor 연계 값 및 매개 변수
author: guardrex
description: 상위 구성 요소에서 하위 구성 요소로 데이터를 전달하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280244"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="31dba-103">ASP.NET Core Blazor 연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="31dba-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="31dba-104">‘연계 값 및 매개 변수’는 구성 요소 계층 구조의 상위 구성 요소에서 원하는 수의 하위 구성 요소로 데이터를 전달하는 편리한 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="31dba-105">[구성 요소 매개 변수](xref:blazor/components/index#component-parameters)와 달리 연계 값 및 매개 변수는 데이터가 사용되는 각 하위 구성 요소에 대해 특성을 할당할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="31dba-106">또한 연계 값 및 매개 변수를 사용하여 구성 요소는 구성 요소 계층 구조에서 서로 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="31dba-107">`CascadingValue` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="31dba-107">`CascadingValue` component</span></span>

<span data-ttu-id="31dba-108">상위 구성 요소는 Blazor 프레임워크의 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 구성 요소를 사용하여 연계 값을 제공합니다. 이 구성 요소는 구성 요소 계층 구조의 하위 트리를 래핑하고 하위 트리 내의 모든 구성 요소에 단일 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="31dba-109">다음 예제에서는 자식 구성 요소의 단추에 CSS 스타일 클래스를 제공하는 레이아웃 구성 요소의 구성 요소 계층 구조 아래로 테마 정보의 흐름을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="31dba-110">다음 `ThemeInfo` C# 클래스는 `UIThemeClasses`라는 폴더에 배치되고 테마 정보를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="31dba-111">이 섹션의 예제에서 앱의 네임스페이스는 `BlazorSample`입니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="31dba-112">사용자 고유의 샘플 앱에서 코드를 시험할 때 앱의 네임스페이스를 샘플 앱의 네임스페이스로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="31dba-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="31dba-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="31dba-114">다음 [레이아웃 구성 요소](xref:blazor/layouts)는 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 속성의 레이아웃 본문을 구성하는 모든 구성 요소에 대한 연계 값으로 테마 정보(`ThemeInfo`)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="31dba-115">`ButtonClass`에는 부트스트랩 단추 스타일인 [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) 값이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="31dba-116">구성 요소 계층 구조의 모든 하위 구성 요소는 `ThemeInfo` 연계 값을 통해 `ButtonClass` 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="31dba-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="31dba-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="31dba-118">`[CascadingParameter]` 특성</span><span class="sxs-lookup"><span data-stu-id="31dba-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="31dba-119">연계 값을 사용하기 위해 하위 구성 요소는 [`[CascadingParameter]` 특성](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)을 사용하여 연계 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="31dba-120">연계 값은 **형식별** 로 연계 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="31dba-121">같은 형식의 여러 값 연계는 이 문서의 뒷부분에 나오는 [여러 값 연계](#cascade-multiple-values) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="31dba-122">다음 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩하며, 필요에 따라 `ThemeInfo`의 동일한 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="31dba-123">매개 변수는 **`Increment Counter (Themed)`** 단추에 대해 CSS 클래스를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="31dba-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="31dba-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="31dba-125">여러 값 연계</span><span class="sxs-lookup"><span data-stu-id="31dba-125">Cascade multiple values</span></span>

<span data-ttu-id="31dba-126">같은 하위 트리 내에서 같은 형식의 여러 값을 연계하려면 각 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 구성 요소 및 해당 [`[CascadingParameter]` 특성](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)에 고유한 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="31dba-127">다음 예제에서는 두 개의 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 구성 요소가 `CascadingType`의 다른 인스턴스를 연계합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="31dba-128">하위 구성 요소에서 연계된 매개 변수는 상위 구성 요소에서 연계된 값을 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>별로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="31dba-129">구성 요소 계층 구조에서 데이터 전달</span><span class="sxs-lookup"><span data-stu-id="31dba-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="31dba-130">연계 매개 변수를 사용하면 구성 요소가 구성 요소 계층 구조에서 데이터를 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="31dba-131">다음 UI 탭 집합 예제를 살펴보면 탭 집합 구성 요소가 일련의 개별 탭을 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="31dba-132">이 섹션의 예제에서 앱의 네임스페이스는 `BlazorSample`입니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="31dba-133">사용자 고유의 샘플 앱에서 코드를 시험할 때 네임스페이스를 샘플 앱의 네임스페이스로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="31dba-134">`UIInterfaces`라는 폴더에 탭이 구현하는 `ITab` 인터페이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="31dba-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="31dba-135">`UIInterfaces/ITab.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

<span data-ttu-id="31dba-136">다음 `TabSet` 구성 요소는 탭 집합을 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="31dba-137">이 섹션의 뒷부분에서 만드는 탭 집합의 `Tab` 구성 요소는 목록(`<ul>...</ul>`)에 대해 목록 항목(`<li>...</li>`)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="31dba-138">자식 `Tab` 구성 요소는 매개 변수로 `TabSet`에 명시적으로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="31dba-139">대신, 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="31dba-140">그러나 `TabSet`에는 각 `Tab` 구성 요소에 대한 참조가 있어야 헤더 및 활성 탭을 렌더링할 수 있습니다. 추가 코드를 요구하지 않고 이러한 조정을 사용하도록 설정하기 위해 `TabSet` 구성 요소는 연계 값으로 제공된 다음, 하위 `Tab` 구성 요소에서 선택될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="31dba-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="31dba-141">`Shared/TabSet.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="31dba-142">하위 `Tab` 구성 요소는 포함하는 `TabSet`를 연계 매개 변수로 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="31dba-143">`Tab` 구성 요소는 `TabSet`에 자신을 추가하고 조정하여 활성 탭을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="31dba-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="31dba-144">`Shared/Tab.razor`:</span></span>

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

<span data-ttu-id="31dba-145">다음 `ExampleTabSet` 구성 요소는 세 개의 `Tab` 구성 요소를 포함하는 `TabSet` 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="31dba-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="31dba-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="31dba-146">`Pages/ExampleTabSet.razor`:</span></span>

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
