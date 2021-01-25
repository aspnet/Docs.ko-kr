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
ms.openlocfilehash: acaa276efda9fb4d09a5c1b1ca59c6abde1b64ec
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252394"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="becbd-103">ASP.NET Core Blazor 수명 주기</span><span class="sxs-lookup"><span data-stu-id="becbd-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="becbd-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="becbd-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="becbd-105">Blazor 프레임워크는 동기 및 비동기 수명 주기 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="becbd-106">구성 요소 초기화 및 렌더링 중에 구성 요소에서 추가 작업을 수행하려면 수명 주기 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="becbd-107">다음 다이어그램은 Blazor 수명 주기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="becbd-108">수명 주기 메서드는 이 문서의 다음 섹션에 있는 예제를 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="becbd-109">구성 요소 수명 주기 이벤트:</span><span class="sxs-lookup"><span data-stu-id="becbd-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="becbd-110">구성 요소가 요청 시 처음 렌더링되는 경우:</span><span class="sxs-lookup"><span data-stu-id="becbd-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="becbd-111">구성 요소의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-111">Create the component's instance.</span></span>
   * <span data-ttu-id="becbd-112">속성 삽입을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-112">Perform property injection.</span></span> <span data-ttu-id="becbd-113">[`SetParametersAsync`](#before-parameters-are-set) 를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="becbd-114">[`OnInitialized{Async}`](#component-initialization-methods)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="becbd-115"><xref:System.Threading.Tasks.Task>가 반환되면 <xref:System.Threading.Tasks.Task>가 대기한 다음, 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="becbd-116"><xref:System.Threading.Tasks.Task>가 반환되지 않으면 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="becbd-117">[`OnParametersSet{Async}`](#after-parameters-are-set)를 호출하고 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="becbd-118"><xref:System.Threading.Tasks.Task>가 `OnParametersSetAsync`에서 반환되면 <xref:System.Threading.Tasks.Task>가 대기한 다음 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Blazor 내 Razor 구성 요소의 구성 요소 수명 주기 이벤트](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="becbd-120">DOM(문서 개체 모델) 이벤트 처리:</span><span class="sxs-lookup"><span data-stu-id="becbd-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="becbd-121">이벤트 처리기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-121">The event handler is run.</span></span>
1. <span data-ttu-id="becbd-122"><xref:System.Threading.Tasks.Task>가 반환되면 <xref:System.Threading.Tasks.Task>가 대기한 다음, 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="becbd-123"><xref:System.Threading.Tasks.Task>가 반환되지 않으면 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![DOM(문서 개체 모델) 이벤트 처리](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="becbd-125">`Render` 수명 주기:</span><span class="sxs-lookup"><span data-stu-id="becbd-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="becbd-126">다음의 경우에 구성 요소에서 추가 렌더링 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="becbd-127">첫 번째 렌더링 이후</span><span class="sxs-lookup"><span data-stu-id="becbd-127">After the first render.</span></span>
   * <span data-ttu-id="becbd-128">[`ShouldRender`](#suppress-ui-refreshing)가 `false`인 경우</span><span class="sxs-lookup"><span data-stu-id="becbd-128">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="becbd-129">렌더링 트리 diff(차이)를 빌드하고 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="becbd-130">DOM이 업데이트될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="becbd-131">[`OnAfterRender{Async}`](#after-component-render)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-131">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![렌더링 수명 주기](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="becbd-133">개발자가 [`StateHasChanged`](#state-changes)를 호출하면 렌더러가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-133">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="becbd-134">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="becbd-135">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="becbd-135">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="becbd-136">매개 변수를 설정하기 전</span><span class="sxs-lookup"><span data-stu-id="becbd-136">Before parameters are set</span></span>

<span data-ttu-id="becbd-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>는 렌더링 트리에서 구성 요소의 부모가 제공하는 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="becbd-138"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>를 호출할 때마다 <xref:Microsoft.AspNetCore.Components.ParameterView>에 구성 요소의 매개 변수 값 세트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-138"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="becbd-139"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>의 기본 구현에서는 <xref:Microsoft.AspNetCore.Components.ParameterView>에 해당 값이 있는 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 또는 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 특성을 사용하여 각 속성의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-139">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="becbd-140"><xref:Microsoft.AspNetCore.Components.ParameterView>에 해당 값이 없는 매개 변수는 변경되지 않고 그대로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-140">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="becbd-141">[`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)를 호출하지 않은 경우, 사용자 지정 코드는 들어오는 매개 변수 값을 필요한 방식으로 해석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-141">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="becbd-142">예를 들어 들어오는 매개 변수를 클래스의 속성에 할당해야 하는 요구 사항이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-142">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="becbd-143">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-143">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="becbd-144">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-144">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="becbd-145">구성 요소 초기화 메서드</span><span class="sxs-lookup"><span data-stu-id="becbd-145">Component initialization methods</span></span>

<span data-ttu-id="becbd-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>는 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>에서 구성 요소가 부모 구성 요소에서 초기 매개 변수를 받은 후 초기화할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-146"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="becbd-147">구성 요소가 비동기 작업을 수행하며, 작업이 완료될 때 새로 고쳐야 하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-147">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="becbd-148">동기 작업의 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-148">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="becbd-149">비동기 작업을 수행하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>를 재정의하고 작업에 [`await`](/dotnet/csharp/language-reference/operators/await) 연산자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-149">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="becbd-150">[콘텐츠를 미리 렌더링](xref:blazor/fundamentals/additional-scenarios#render-mode)하는 Blazor Server 앱이 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>를 ‘두 번’ 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-150">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="becbd-151">첫 번째 호출: 구성 요소가 처음에 페이지 일부로 정적 렌더링될 때</span><span class="sxs-lookup"><span data-stu-id="becbd-151">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="becbd-152">두 번째 호출: 브라우저가 서버에 다시 연결할 때</span><span class="sxs-lookup"><span data-stu-id="becbd-152">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="becbd-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 개발자 코드가 두 번 실행되는 것을 방지하려면 [미리 렌더링한 후의 상태 저장 다시 연결](#stateful-reconnection-after-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-153">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="becbd-154">Blazor Server 앱을 미리 렌더링 중이면 브라우저에 연결되어 있지 않으므로 JavaScript 호출 등의 특정 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-154">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="becbd-155">미리 렌더링된 경우 구성 요소를 다르게 렌더링해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-155">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="becbd-156">자세한 내용은 [앱을 미리 렌더링 중인 경우 검색](#detect-when-the-app-is-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-156">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="becbd-157">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-157">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="becbd-158">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-158">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="becbd-159">매개 변수를 설정한 후</span><span class="sxs-lookup"><span data-stu-id="becbd-159">After parameters are set</span></span>

<span data-ttu-id="becbd-160">다음 두 가지 경우에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 또는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-160"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="becbd-161">구성 요소가 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 또는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>에서 초기화된 후</span><span class="sxs-lookup"><span data-stu-id="becbd-161">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="becbd-162">부모 구성 요소가 다시 렌더링되고 다음을 제공하는 경우</span><span class="sxs-lookup"><span data-stu-id="becbd-162">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="becbd-163">하나 이상의 매개 변수가 변경된, 알려진 변경 불가능 기본 형식만</span><span class="sxs-lookup"><span data-stu-id="becbd-163">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="becbd-164">복합 형식 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="becbd-164">Any complex-typed parameters.</span></span> <span data-ttu-id="becbd-165">프레임워크는 복합 형식 매개 변수의 값이 내부적으로 변경되었는지 아닌지를 알 수 없으므로 매개 변수 집합을 변경된 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-165">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="becbd-166">매개 변수와 속성 값을 적용하는 경우 비동기 작업은 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 수명 주기 이벤트 중에 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-166">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="becbd-167">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-167">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="becbd-168">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-168">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="becbd-169">구성 요소 렌더링 후</span><span class="sxs-lookup"><span data-stu-id="becbd-169">After component render</span></span>

<span data-ttu-id="becbd-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>는 구성 요소 렌더링을 완료한 후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="becbd-171">이때 요소 및 구성 요소 참조가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-171">Element and component references are populated at this point.</span></span> <span data-ttu-id="becbd-172">렌더링된 DOM 요소에서 작동하는 타사 JavaScript 라이브러리 활성화와 같은 추가 초기화 단계를 렌더링된 콘텐츠로 수행하려면 이 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-172">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="becbd-173"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>의 `firstRender` 매개 변수와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-173">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="becbd-174">구성 요소 인스턴스를 처음 렌더링할 때는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-174">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="becbd-175">초기화 작업이 한 번만 수행되도록 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-175">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="becbd-176">렌더링 직후의 비동기 작업은 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 수명 주기 이벤트 중에 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-176">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="becbd-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>에서 <xref:System.Threading.Tasks.Task>를 반환하는 경우에도, 해당 작업이 완료된 후에는 프레임워크에서 구성 요소에 대해 추가 렌더링 주기를 예약하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-177">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="becbd-178">이 동작은 무한 렌더링 루프를 방지하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-178">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="becbd-179">반환된 작업이 완료된 후 추가 렌더링 주기를 예약하는 기타 수명 주기 메서드와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-179">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="becbd-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>는 ‘서버에서 미리 렌더링 프로세스 중에는 호출되지 않습니다’.</span><span class="sxs-lookup"><span data-stu-id="becbd-180"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="becbd-181">이 메서드는 미리 렌더링이 완료된 후 구성 요소가 대화형으로 렌더링될 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-181">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="becbd-182">앱이 미리 렌더링되는 경우:</span><span class="sxs-lookup"><span data-stu-id="becbd-182">When the app prerenders:</span></span>

1. <span data-ttu-id="becbd-183">구성 요소가 서버에서 실행되어 HTTP 응답에 몇 가지 정적 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-183">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="becbd-184">이 단계에서는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>가 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-184">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="becbd-185">`blazor.server.js` 또는 `blazor.webassembly.js`가 브라우저에서 시작되면 구성 요소가 대화형 렌더링 모드로 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-185">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="becbd-186">구성 요소가 다시 시작되면 앱이 더 이상 미리 렌더링 단계에 포함되지 않기 때문에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>가 **호출됩니다**.</span><span class="sxs-lookup"><span data-stu-id="becbd-186">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="becbd-187">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-187">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="becbd-188">자세한 내용은 [`IDisposable`을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-188">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="becbd-189">UI 새로 고침 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="becbd-189">Suppress UI refreshing</span></span>

<span data-ttu-id="becbd-190">UI 새로 고침을 표시하지 않으려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-190">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="becbd-191">구현에서 `true`를 반환하는 경우 UI가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-191">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="becbd-192">구성 요소를 렌더링할 때마다 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-192"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="becbd-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 재정의한 경우에도 처음에는 구성 요소가 항상 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-193">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="becbd-194">자세한 내용은 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-194">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="becbd-195">상태 변경</span><span class="sxs-lookup"><span data-stu-id="becbd-195">State changes</span></span>

<span data-ttu-id="becbd-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 구성 요소에 상태가 변경되었음을 알립니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="becbd-197">해당하는 경우, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하면 구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-197">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="becbd-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 <xref:Microsoft.AspNetCore.Components.EventCallback> 메서드에 대해 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="becbd-199">자세한 내용은 <xref:blazor/components/event-handling#eventcallback>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-199">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="becbd-200">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-200">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="becbd-201">렌더링 시 불완전한 비동기 작업 처리</span><span class="sxs-lookup"><span data-stu-id="becbd-201">Handle incomplete async actions at render</span></span>

<span data-ttu-id="becbd-202">수명 주기 이벤트에서 수행한 비동기 작업이 구성 요소를 렌더링하기 전에 완료되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-202">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="becbd-203">수명 주기 메서드를 실행하는 동안 개체가 `null`이거나 불완전하게 데이터로 채워졌을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-203">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="becbd-204">개체가 초기화되었는지 확인하는 렌더링 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-204">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="becbd-205">개체가 `null`인 동안 자리 표시자 UI 요소(예: 로드 메시지)를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-205">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="becbd-206">Blazor 템플릿의 `FetchData` 구성 요소에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>는 예측 데이터(`forecasts`)를 비동기적으로 수신하도록 재정의되었습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-206">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="becbd-207">`forecasts`가 `null`인 경우 사용자에게 로드 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-207">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="becbd-208"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>에서 반환된 `Task`가 완료되면 구성 요소가 업데이트된 상태로 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-208">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="becbd-209">Blazor Server 템플릿의 `Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="becbd-209">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="becbd-210">오류 처리</span><span class="sxs-lookup"><span data-stu-id="becbd-210">Handle errors</span></span>

<span data-ttu-id="becbd-211">수명 주기 메서드 실행 중의 오류 처리 방법에 대한 자세한 내용은 <xref:blazor/fundamentals/handle-errors#lifecycle-methods>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-211">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="becbd-212">미리 렌더링 후의 상태 저장 다시 연결</span><span class="sxs-lookup"><span data-stu-id="becbd-212">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="becbd-213">Blazor Server 앱에서 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>가 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>인 경우 구성 요소는 처음에 페이지 일부로 정적 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-213">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="becbd-214">브라우저가 서버에 다시 연결하면 구성 요소가 ‘다시’ 렌더링되고, 이제 대화형 구성 요소가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-214">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="becbd-215">구성 요소를 초기화하기 위한 [`OnInitialized{Async}`](#component-initialization-methods) 수명 주기 메서드가 있는 경우 메서드가 다음과 같이 ‘두 번’ 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-215">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="becbd-216">구성 요소를 정적으로 미리 렌더링할 때</span><span class="sxs-lookup"><span data-stu-id="becbd-216">When the component is prerendered statically.</span></span>
* <span data-ttu-id="becbd-217">서버 연결이 설정된 후</span><span class="sxs-lookup"><span data-stu-id="becbd-217">After the server connection has been established.</span></span>

<span data-ttu-id="becbd-218">이 동작 때문에 구성 요소를 최종적으로 렌더링할 때는 UI에 표시되는 데이터가 상당히 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-218">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="becbd-219">Blazor Server 앱에서 이중 렌더링 시나리오를 방지하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-219">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="becbd-220">미리 렌더링 중에 상태를 캐시하고 앱이 다시 시작된 후 상태를 검색하는 데 사용할 수 있는 식별자를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-220">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="becbd-221">미리 렌더링 중에 식별자를 사용하여 구성 요소 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-221">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="becbd-222">미리 렌더링 후에 식별자를 사용하여 캐시된 상태를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-222">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="becbd-223">다음 코드는 템플릿 기반 Blazor Server 앱에서 이중 렌더링을 방지하도록 업데이트된 `WeatherForecastService`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-223">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="becbd-224"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>에 대한 자세한 내용은 <xref:blazor/fundamentals/additional-scenarios#render-mode>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-224">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="becbd-225">앱을 미리 렌더링 중인 경우 검색</span><span class="sxs-lookup"><span data-stu-id="becbd-225">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="becbd-226">IDisposable을 사용한 구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="becbd-226">Component disposal with IDisposable</span></span>

<span data-ttu-id="becbd-227">구성 요소가 <xref:System.IDisposable>을 구현하는 경우 UI에서 구성 요소가 제거될 때 [`Dispose` 메서드](/dotnet/standard/garbage-collection/implementing-dispose)가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-227">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="becbd-228">[구성 요소 초기화](#component-initialization-methods) 도중을 포함하여 언제든지 삭제가 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-228">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="becbd-229">다음 구성 요소는 `@implements IDisposable` 및 `Dispose` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-229">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

> [!NOTE]
> <span data-ttu-id="becbd-230">`Dispose`에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 호출은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-230">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="becbd-231">렌더러를 삭제하는 과정에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출될 수 있으므로, 해당 시점에 UI 업데이트를 요청할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-231"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="becbd-232">.NET 이벤트에서 이벤트 처리기의 구독을 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-232">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="becbd-233">다음 [Blazor 양식](xref:blazor/forms-validation) 예제에서는 `Dispose` 메서드에서 이벤트 처리기를 언후크하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-233">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="becbd-234">프라이빗 필드 및 람다 접근 방식</span><span class="sxs-lookup"><span data-stu-id="becbd-234">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="becbd-235">프라이빗 메서드 접근 방식</span><span class="sxs-lookup"><span data-stu-id="becbd-235">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="becbd-236">취소할 수 있는 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="becbd-236">Cancelable background work</span></span>

<span data-ttu-id="becbd-237">구성 요소는 네트워크 호출(<xref:System.Net.Http.HttpClient>) 및 데이터베이스와의 상호 작용 등과 같은 장기 실행 백그라운드 작업을 수행하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-237">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="becbd-238">여러 상황에서 시스템 리소스를 절약하기 위해 백그라운드 작업을 중지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-238">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="becbd-239">예를 들어 사용자가 구성 요소에서 이동할 때 백그라운드 비동기 작업이 자동으로 중지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-239">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="becbd-240">백그라운드 작업 항목에 취소가 필요할 수 있는 다른 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-240">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="becbd-241">잘못된 입력 데이터 또는 처리 매개 변수를 사용하여 실행 중인 백그라운드 작업이 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-241">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="becbd-242">현재 실행 중인 백그라운드 작업 항목 집합을 새 작업 항목 집합으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-242">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="becbd-243">현재 실행 중인 작업의 우선 순위를 변경해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-243">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="becbd-244">서버에 다시 배포하기 위해 앱을 종료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-244">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="becbd-245">서버 리소스가 제한되므로 백그라운드 작업 항목을 다시 예약해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-245">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="becbd-246">구성 요소에서 취소할 수 있는 백그라운드 작업 패턴을 구현하는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-246">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="becbd-247"><xref:System.Threading.CancellationTokenSource> 및 <xref:System.Threading.CancellationToken>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-247">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="becbd-248">[구성 요소 삭제](#component-disposal-with-idisposable) 시 및 토큰을 수동으로 취소하여 언제든지 취소가 필요할 때 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A)을 호출하여 백그라운드 작업을 취소해야 한다고 알립니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-248">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="becbd-249">비동기 호출이 반환된 후에는 토큰에서 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-249">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="becbd-250">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="becbd-250">In the following example:</span></span>

* <span data-ttu-id="becbd-251">`await Task.Delay(5000, cts.Token);`는 장기 실행 비동기 백그라운드 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-251">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="becbd-252">`BackgroundResourceMethod`는 호출되기 전에 `Resource`가 삭제된 경우 시작하지 않아야 하는 장기 실행 백그라운드 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-252">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="becbd-253">Blazor Server 재연결 이벤트</span><span class="sxs-lookup"><span data-stu-id="becbd-253">Blazor Server reconnection events</span></span>

<span data-ttu-id="becbd-254">이 문서에서 설명하는 구성 요소 수명 주기 이벤트는 [Blazor Server의 재연결 이벤트 처리기](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui)와 별도로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-254">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="becbd-255">Blazor Server 앱의 클라이언트에 대한 SignalR 연결이 끊어지는 경우 UI 업데이트만 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-255">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="becbd-256">UI 업데이트는 연결이 다시 설정될 때 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="becbd-256">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="becbd-257">회로 처리기 이벤트 및 구성에 대한 자세한 내용은 <xref:blazor/fundamentals/additional-scenarios> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="becbd-257">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
