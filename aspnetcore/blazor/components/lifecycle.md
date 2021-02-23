---
title: ASP.NET Core Blazor 수명 주기
author: guardrex
description: ASP.NET Core Blazor 앱에서 Razor 구성 요소 수명 주기 메서드를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 03a49c827a1f70e6b721adf293857bb33475ed36
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107079"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="03eb2-103">ASP.NET Core Blazor 수명 주기</span><span class="sxs-lookup"><span data-stu-id="03eb2-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="03eb2-104">Blazor 프레임워크는 동기 및 비동기 수명 주기 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="03eb2-105">구성 요소 초기화 및 렌더링 중에 구성 요소에서 추가 작업을 수행하려면 수명 주기 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="03eb2-106">다음 다이어그램은 Blazor 수명 주기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="03eb2-107">수명 주기 메서드는 이 문서의 다음 섹션에 있는 예제를 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="03eb2-108">구성 요소 수명 주기 이벤트:</span><span class="sxs-lookup"><span data-stu-id="03eb2-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="03eb2-109">구성 요소가 요청 시 처음 렌더링되는 경우:</span><span class="sxs-lookup"><span data-stu-id="03eb2-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="03eb2-110">구성 요소의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-110">Create the component's instance.</span></span>
   * <span data-ttu-id="03eb2-111">속성 삽입을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-111">Perform property injection.</span></span> <span data-ttu-id="03eb2-112">[`SetParametersAsync`](#before-parameters-are-set) 를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="03eb2-113">[`OnInitialized{Async}`](#component-initialization-methods)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="03eb2-114"><xref:System.Threading.Tasks.Task>가 반환되면 <xref:System.Threading.Tasks.Task>가 대기한 다음, 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="03eb2-115"><xref:System.Threading.Tasks.Task>가 반환되지 않으면 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="03eb2-116">[`OnParametersSet{Async}`](#after-parameters-are-set)를 호출하고 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="03eb2-117"><xref:System.Threading.Tasks.Task>가 `OnParametersSetAsync`에서 반환되면 <xref:System.Threading.Tasks.Task>가 대기한 다음 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Blazor 내 Razor 구성 요소의 구성 요소 수명 주기 이벤트](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="03eb2-119">DOM(문서 개체 모델) 이벤트 처리:</span><span class="sxs-lookup"><span data-stu-id="03eb2-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="03eb2-120">이벤트 처리기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-120">The event handler is run.</span></span>
1. <span data-ttu-id="03eb2-121"><xref:System.Threading.Tasks.Task>가 반환되면 <xref:System.Threading.Tasks.Task>가 대기한 다음, 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="03eb2-122"><xref:System.Threading.Tasks.Task>가 반환되지 않으면 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![DOM(문서 개체 모델) 이벤트 처리](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="03eb2-124">`Render` 수명 주기:</span><span class="sxs-lookup"><span data-stu-id="03eb2-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="03eb2-125">다음의 경우에 구성 요소에서 추가 렌더링 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="03eb2-126">첫 번째 렌더링 이후</span><span class="sxs-lookup"><span data-stu-id="03eb2-126">After the first render.</span></span>
   * <span data-ttu-id="03eb2-127">[`ShouldRender`](#suppress-ui-refreshing)가 `false`인 경우</span><span class="sxs-lookup"><span data-stu-id="03eb2-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="03eb2-128">렌더링 트리 diff(차이)를 빌드하고 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="03eb2-129">DOM이 업데이트될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="03eb2-130">[`OnAfterRender{Async}`](#after-component-render)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![렌더링 수명 주기](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="03eb2-132">개발자가 [`StateHasChanged`](#state-changes)를 호출하면 렌더러가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="03eb2-133">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="03eb2-134">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="03eb2-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="03eb2-135">매개 변수를 설정하기 전</span><span class="sxs-lookup"><span data-stu-id="03eb2-135">Before parameters are set</span></span>

<span data-ttu-id="03eb2-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>는 렌더링 트리 또는 경로 매개 변수에서 구성 요소의 부모가 제공하는 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="03eb2-137">메서드를 재정의하면 개발자 코드가 <xref:Microsoft.AspNetCore.Components.ParameterView>의 매개 변수와 직접 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="03eb2-138">다음 예제에서 `Param`의 경로 매개 변수 구문 분석이 성공하는 경우 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType>는 `Param` 매개 변수의 값을 `value`에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="03eb2-139">`value`가 `null`이 아니면 값이 구성 요소에 의해 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="03eb2-140">[경로 매개 변수 일치는 대/소문자를 구분하지 않지만](xref:blazor/fundamentals/routing#route-parameters) <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A>는 경로 템플릿에서 대/소문자를 구분하는 매개 변수 이름만 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="03eb2-141">다음 예제에서 값을 가져오려면 `/{param?}`가 아닌 `/{Param?}`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="03eb2-142">이 시나리오에서 `/{param?}`를 사용하면 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A>는 `false`를 반환하고 `message`는 문자열 중 하나로 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="03eb2-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="03eb2-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="03eb2-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>를 호출할 때마다 <xref:Microsoft.AspNetCore.Components.ParameterView>에 구성 요소의 매개 변수 값 세트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="03eb2-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>의 기본 구현에서는 <xref:Microsoft.AspNetCore.Components.ParameterView>에 해당 값이 있는 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 또는 [`[CascadingParameter]` 특성](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)으로 각 속성의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="03eb2-146"><xref:Microsoft.AspNetCore.Components.ParameterView>에 해당 값이 없는 매개 변수는 변경되지 않고 그대로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="03eb2-147">[`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)를 호출하지 않은 경우, 사용자 지정 코드는 들어오는 매개 변수 값을 필요한 방식으로 해석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="03eb2-148">예를 들어 들어오는 매개 변수를 클래스의 속성에 할당해야 하는 요구 사항이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="03eb2-149">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="03eb2-150">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="03eb2-151">구성 요소 초기화 메서드</span><span class="sxs-lookup"><span data-stu-id="03eb2-151">Component initialization methods</span></span>

<span data-ttu-id="03eb2-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>는 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>에서 구성 요소가 부모 구성 요소에서 초기 매개 변수를 받은 후 초기화할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="03eb2-153">구성 요소가 비동기 작업을 수행하며, 작업이 완료될 때 새로 고쳐야 하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="03eb2-154">동기 작업의 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="03eb2-155">비동기 작업을 수행하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>를 재정의하고 작업에 [`await`](/dotnet/csharp/language-reference/operators/await) 연산자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="03eb2-156">[콘텐츠를 미리 렌더링](xref:blazor/fundamentals/signalr#render-mode)하는 Blazor Server 앱이 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>를 ‘두 번’ 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="03eb2-157">첫 번째 호출: 구성 요소가 처음에 페이지 일부로 정적 렌더링될 때</span><span class="sxs-lookup"><span data-stu-id="03eb2-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="03eb2-158">두 번째 호출: 브라우저가 서버에 다시 연결할 때</span><span class="sxs-lookup"><span data-stu-id="03eb2-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="03eb2-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 개발자 코드가 두 번 실행되는 것을 방지하려면 [미리 렌더링한 후의 상태 저장 다시 연결](#stateful-reconnection-after-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="03eb2-160">Blazor Server 앱을 미리 렌더링 중이면 브라우저에 연결되어 있지 않으므로 JavaScript 호출 등의 특정 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="03eb2-161">미리 렌더링된 경우 구성 요소를 다르게 렌더링해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="03eb2-162">자세한 내용은 [앱을 미리 렌더링 중인 경우 검색](#detect-when-the-app-is-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="03eb2-163">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="03eb2-164">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="03eb2-165">매개 변수를 설정한 후</span><span class="sxs-lookup"><span data-stu-id="03eb2-165">After parameters are set</span></span>

<span data-ttu-id="03eb2-166">다음 두 가지 경우에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 또는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="03eb2-167">구성 요소가 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 또는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>에서 초기화된 후</span><span class="sxs-lookup"><span data-stu-id="03eb2-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="03eb2-168">부모 구성 요소가 다시 렌더링되고 다음을 제공하는 경우</span><span class="sxs-lookup"><span data-stu-id="03eb2-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="03eb2-169">하나 이상의 매개 변수가 변경된, 알려진 변경 불가능 기본 형식만</span><span class="sxs-lookup"><span data-stu-id="03eb2-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="03eb2-170">복합 형식 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="03eb2-170">Any complex-typed parameters.</span></span> <span data-ttu-id="03eb2-171">프레임워크는 복합 형식 매개 변수의 값이 내부적으로 변경되었는지 아닌지를 알 수 없으므로 매개 변수 집합을 변경된 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="03eb2-172">매개 변수와 속성 값을 적용하는 경우 비동기 작업은 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 수명 주기 이벤트 중에 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="03eb2-173">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="03eb2-174">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="03eb2-175">구성 요소 렌더링 후</span><span class="sxs-lookup"><span data-stu-id="03eb2-175">After component render</span></span>

<span data-ttu-id="03eb2-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>는 구성 요소 렌더링을 완료한 후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="03eb2-177">이때 요소 및 구성 요소 참조가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="03eb2-178">렌더링된 DOM 요소에서 작동하는 타사 JavaScript 라이브러리 활성화와 같은 추가 초기화 단계를 렌더링된 콘텐츠로 수행하려면 이 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="03eb2-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>의 `firstRender` 매개 변수와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="03eb2-180">구성 요소 인스턴스를 처음 렌더링할 때는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="03eb2-181">초기화 작업이 한 번만 수행되도록 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-181">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="03eb2-182">렌더링 직후의 비동기 작업은 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 수명 주기 이벤트 중에 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="03eb2-183"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>에서 <xref:System.Threading.Tasks.Task>를 반환하는 경우에도, 해당 작업이 완료된 후에는 프레임워크에서 구성 요소에 대해 추가 렌더링 주기를 예약하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="03eb2-184">이 동작은 무한 렌더링 루프를 방지하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="03eb2-185">반환된 작업이 완료된 후 추가 렌더링 주기를 예약하는 기타 수명 주기 메서드와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="03eb2-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>는 ‘서버에서 미리 렌더링 프로세스 중에는 호출되지 않습니다’.</span><span class="sxs-lookup"><span data-stu-id="03eb2-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="03eb2-187">이 메서드는 미리 렌더링이 완료된 후 구성 요소가 대화형으로 렌더링될 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="03eb2-188">앱이 미리 렌더링되는 경우:</span><span class="sxs-lookup"><span data-stu-id="03eb2-188">When the app prerenders:</span></span>

1. <span data-ttu-id="03eb2-189">구성 요소가 서버에서 실행되어 HTTP 응답에 몇 가지 정적 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="03eb2-190">이 단계에서는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>가 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="03eb2-191">`blazor.server.js` 또는 `blazor.webassembly.js`가 브라우저에서 시작되면 구성 요소가 대화형 렌더링 모드로 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="03eb2-192">구성 요소가 다시 시작되면 앱이 더 이상 미리 렌더링 단계에 포함되지 않기 때문에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>가 **호출됩니다**.</span><span class="sxs-lookup"><span data-stu-id="03eb2-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="03eb2-193">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="03eb2-194">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="03eb2-195">UI 새로 고침 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="03eb2-195">Suppress UI refreshing</span></span>

<span data-ttu-id="03eb2-196">UI 새로 고침을 표시하지 않으려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="03eb2-197">구현에서 `true`를 반환하는 경우 UI가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="03eb2-198">구성 요소를 렌더링할 때마다 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="03eb2-199"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 재정의한 경우에도 처음에는 구성 요소가 항상 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="03eb2-200">자세한 내용은 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="03eb2-201">상태 변경</span><span class="sxs-lookup"><span data-stu-id="03eb2-201">State changes</span></span>

<span data-ttu-id="03eb2-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 구성 요소에 상태가 변경되었음을 알립니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="03eb2-203">해당하는 경우, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하면 구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="03eb2-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 <xref:Microsoft.AspNetCore.Components.EventCallback> 메서드에 대해 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="03eb2-205">자세한 내용은 <xref:blazor/components/event-handling#eventcallback>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="03eb2-206">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="03eb2-207">렌더링 시 불완전한 비동기 작업 처리</span><span class="sxs-lookup"><span data-stu-id="03eb2-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="03eb2-208">수명 주기 이벤트에서 수행한 비동기 작업이 구성 요소를 렌더링하기 전에 완료되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="03eb2-209">수명 주기 메서드를 실행하는 동안 개체가 `null`이거나 불완전하게 데이터로 채워졌을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="03eb2-210">개체가 초기화되었는지 확인하는 렌더링 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="03eb2-211">개체가 `null`인 동안 자리 표시자 UI 요소(예: 로드 메시지)를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="03eb2-212">Blazor 템플릿의 `FetchData` 구성 요소에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>는 예측 데이터(`forecasts`)를 비동기적으로 수신하도록 재정의되었습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="03eb2-213">`forecasts`가 `null`인 경우 사용자에게 로드 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="03eb2-214"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>에서 반환된 `Task`가 완료되면 구성 요소가 업데이트된 상태로 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="03eb2-215">Blazor Server 템플릿의 `Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="03eb2-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="03eb2-216">오류 처리</span><span class="sxs-lookup"><span data-stu-id="03eb2-216">Handle errors</span></span>

<span data-ttu-id="03eb2-217">수명 주기 메서드 실행 중의 오류 처리 방법에 대한 자세한 내용은 <xref:blazor/fundamentals/handle-errors#lifecycle-methods>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="03eb2-218">미리 렌더링 후의 상태 저장 다시 연결</span><span class="sxs-lookup"><span data-stu-id="03eb2-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="03eb2-219">Blazor Server 앱에서 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>가 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>인 경우 구성 요소는 처음에 페이지 일부로 정적 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="03eb2-220">브라우저가 서버에 다시 연결하면 구성 요소가 ‘다시’ 렌더링되고, 이제 대화형 구성 요소가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="03eb2-221">구성 요소를 초기화하기 위한 [`OnInitialized{Async}`](#component-initialization-methods) 수명 주기 메서드가 있는 경우 메서드가 다음과 같이 ‘두 번’ 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="03eb2-222">구성 요소를 정적으로 미리 렌더링할 때</span><span class="sxs-lookup"><span data-stu-id="03eb2-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="03eb2-223">서버 연결이 설정된 후</span><span class="sxs-lookup"><span data-stu-id="03eb2-223">After the server connection has been established.</span></span>

<span data-ttu-id="03eb2-224">이 동작 때문에 구성 요소를 최종적으로 렌더링할 때는 UI에 표시되는 데이터가 상당히 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="03eb2-225">Blazor Server 앱에서 이중 렌더링 시나리오를 방지하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="03eb2-226">미리 렌더링 중에 상태를 캐시하고 앱이 다시 시작된 후 상태를 검색하는 데 사용할 수 있는 식별자를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="03eb2-227">미리 렌더링 중에 식별자를 사용하여 구성 요소 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="03eb2-228">미리 렌더링 후에 식별자를 사용하여 캐시된 상태를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="03eb2-229">다음 코드는 템플릿 기반 Blazor Server 앱에서 이중 렌더링을 방지하도록 업데이트된 `WeatherForecastService`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="03eb2-230"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>에 대한 자세한 내용은 <xref:blazor/fundamentals/signalr#render-mode>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="03eb2-231">앱을 미리 렌더링 중인 경우 검색</span><span class="sxs-lookup"><span data-stu-id="03eb2-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="03eb2-232">`IDisposable`로 구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="03eb2-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="03eb2-233">구성 요소가 <xref:System.IDisposable>을 구현하는 경우 프레임워크는 관리되지 않는 리소스를 해제할 수 있는 UI에서 해당 구성 요소가 제거될 때 [삭제 메서드](/dotnet/standard/garbage-collection/implementing-dispose)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="03eb2-234">[구성 요소 초기화](#component-initialization-methods) 도중을 포함하여 언제든지 삭제가 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="03eb2-235">다음 구성 요소는 [`@implements`](xref:mvc/views/razor#implements) Razor 지시문을 사용하여 <xref:System.IDisposable>을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

<span data-ttu-id="03eb2-236">개체를 삭제해야 하는 경우 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType>를 호출할 때 람다를 사용하여 개체를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="03eb2-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="03eb2-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="03eb2-238">위 예제는 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="03eb2-239">비동기 삭제 작업에는 <xref:System.IDisposable.Dispose> 대신 `DisposeAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="03eb2-240">`Dispose`에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 호출은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="03eb2-241">렌더러를 삭제하는 과정에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출될 수 있으므로, 해당 시점에 UI 업데이트를 요청할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="03eb2-242">.NET 이벤트에서 이벤트 처리기의 구독을 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="03eb2-243">다음 [Blazor 양식](xref:blazor/forms-validation) 예제에서는 `Dispose` 메서드에서 이벤트 처리기를 구독 취소하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="03eb2-244">프라이빗 필드 및 람다 접근 방식</span><span class="sxs-lookup"><span data-stu-id="03eb2-244">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="03eb2-245">프라이빗 메서드 접근 방식</span><span class="sxs-lookup"><span data-stu-id="03eb2-245">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

<span data-ttu-id="03eb2-246">[익명 함수](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), 메서드 또는 식이 사용되는 경우에는 <xref:System.IDisposable>을 구현하고 대리자를 구독 취소할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="03eb2-247">그러나 대리자를 구독 취소하지 못하면 **이벤트를 노출하는 개체가 대리자를 등록하는 구성 요소보다 수명이 긴 경우** 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="03eb2-248">이 경우 등록된 대리자가 원래 개체를 활성 상태로 유지하기 때문에 메모리 누수가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="03eb2-249">따라서 이벤트 대리자가 신속하게 삭제됨을 알고 있는 경우에만 다음 접근 방식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="03eb2-250">삭제해야 하는 개체의 수명이 확실하지 않으면 대리자 메서드를 구독하고 위 예제에 표시된 대로 대리자를 적절하게 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="03eb2-251">익명 람다 메서드 접근 방식(명시적 삭제 필요하지 않음)</span><span class="sxs-lookup"><span data-stu-id="03eb2-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="03eb2-252">익명 람다 식 접근 방식(명시적 삭제 필요하지 않음)</span><span class="sxs-lookup"><span data-stu-id="03eb2-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="03eb2-253">익명 람다 식이 포함된 위 코드의 전체 예제는 <xref:blazor/forms-validation#validator-components>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="03eb2-254">자세한 내용은 [관리되지 않는 리소스 정리](/dotnet/standard/garbage-collection/unmanaged)와 `Dispose` 및 `DisposeAsync` 메서드 구현에 이어지는 토픽을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="03eb2-255">취소할 수 있는 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="03eb2-255">Cancelable background work</span></span>

<span data-ttu-id="03eb2-256">구성 요소는 네트워크 호출(<xref:System.Net.Http.HttpClient>) 및 데이터베이스와의 상호 작용 등과 같은 장기 실행 백그라운드 작업을 수행하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="03eb2-257">여러 상황에서 시스템 리소스를 절약하기 위해 백그라운드 작업을 중지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="03eb2-258">예를 들어 사용자가 구성 요소에서 이동할 때 백그라운드 비동기 작업이 자동으로 중지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="03eb2-259">백그라운드 작업 항목에 취소가 필요할 수 있는 다른 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="03eb2-260">잘못된 입력 데이터 또는 처리 매개 변수를 사용하여 실행 중인 백그라운드 작업이 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="03eb2-261">현재 실행 중인 백그라운드 작업 항목 집합을 새 작업 항목 집합으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="03eb2-262">현재 실행 중인 작업의 우선 순위를 변경해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="03eb2-263">서버에 다시 배포하기 위해 앱을 종료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="03eb2-264">서버 리소스가 제한되므로 백그라운드 작업 항목을 다시 예약해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="03eb2-265">구성 요소에서 취소할 수 있는 백그라운드 작업 패턴을 구현하는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="03eb2-266"><xref:System.Threading.CancellationTokenSource> 및 <xref:System.Threading.CancellationToken>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="03eb2-267">[구성 요소 삭제](#component-disposal-with-idisposable) 시 및 토큰을 수동으로 취소하여 언제든지 취소가 필요할 때 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A)을 호출하여 백그라운드 작업을 취소해야 한다고 알립니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="03eb2-268">비동기 호출이 반환된 후에는 토큰에서 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="03eb2-269">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="03eb2-269">In the following example:</span></span>

* <span data-ttu-id="03eb2-270">`await Task.Delay(5000, cts.Token);`는 장기 실행 비동기 백그라운드 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="03eb2-271">`BackgroundResourceMethod`는 호출되기 전에 `Resource`가 삭제된 경우 시작하지 않아야 하는 장기 실행 백그라운드 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="03eb2-272">Blazor Server 재연결 이벤트</span><span class="sxs-lookup"><span data-stu-id="03eb2-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="03eb2-273">이 문서에서 설명하는 구성 요소 수명 주기 이벤트는 [Blazor Server의 재연결 이벤트 처리기](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)와 별도로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="03eb2-274">Blazor Server 앱의 클라이언트에 대한 SignalR 연결이 끊어지는 경우 UI 업데이트만 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="03eb2-275">UI 업데이트는 연결이 다시 설정될 때 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="03eb2-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="03eb2-276">회로 처리기 이벤트 및 구성에 대한 자세한 내용은 <xref:blazor/fundamentals/signalr> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="03eb2-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
