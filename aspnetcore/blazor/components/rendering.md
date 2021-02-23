---
title: ASP.NET Core Blazor 구성 요소 렌더링
author: guardrex
description: StateHasChanged를 호출하는 경우를 포함하여 ASP.NET Core Blazor 앱의 Razor 구성 요소 렌더링에 관해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: e1222981d4af3f4e233cdc0c57bb96a71972af15
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280044"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="2bbcf-103">ASP.NET Core Blazor 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="2bbcf-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="2bbcf-104">구성 요소는 부모 구성 요소에 의해 구성 요소 계층 구조에 처음 추가될 때 렌더링‘되어야’ 합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-104">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="2bbcf-105">구성 요소는 이 시점에만 엄격하게 렌더링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-105">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="2bbcf-106">구성 요소는 자체 논리 및 규칙에 따라 다른 시간에 렌더링하도록 선택‘할 수 있습니다’.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-106">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="2bbcf-107">`ComponentBase`의 규칙</span><span class="sxs-lookup"><span data-stu-id="2bbcf-107">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="2bbcf-108">기본적으로 Razor 구성 요소(`.razor`)는 다음 시간에 다시 렌더링을 트리거하는 논리를 포함하는 <xref:Microsoft.AspNetCore.Components.ComponentBase> 기본 클래스에서 상속됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-108">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="2bbcf-109">부모 구성 요소에서 업데이트된 매개 변수 세트를 적용한 후.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-109">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="2bbcf-110">계단식 배열 매개 변수의 업데이트된 값을 적용한 후.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-110">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="2bbcf-111">이벤트 알림 및 자체 이벤트 처리기 중 하나를 호출한 후.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-111">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="2bbcf-112">자체 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 메서드를 호출한 후.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="2bbcf-113"><xref:Microsoft.AspNetCore.Components.ComponentBase>에서 상속된 구성 요소는 다음 중 하나에 해당하는 경우 매개 변수 업데이트로 인해 다시 렌더링을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="2bbcf-114">모든 매개 변수 값은 알려진 변경 불가능 기본 형식(예: `int`, `string`, `DateTime`)이며 이전 매개 변수 세트가 설정된 이후 변경되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-114">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="2bbcf-115">구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 메서드가 `false`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="2bbcf-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>에 대한 자세한 내용은 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="2bbcf-117">렌더링 흐름 제어</span><span class="sxs-lookup"><span data-stu-id="2bbcf-117">Control the rendering flow</span></span>

<span data-ttu-id="2bbcf-118">대부분의 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase> 규칙은 이벤트가 발생한 후 구성 요소 다시 렌더링의 올바른 하위 세트를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="2bbcf-119">개발자는 일반적으로 프레임워크에 다시 렌더링할 구성 요소와 이 구성 요소를 다시 렌더링할 시기를 알리는 수동 논리를 제공할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="2bbcf-120">프레임워크 규칙의 전반적인 효과는 이벤트를 수신하는 구성 요소가 스스로 다시 렌더링되어 매개 변수 값이 변경되었을 수 있는 하위 구성 요소의 다시 렌더링을 재귀적으로 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="2bbcf-121">프레임워크 규칙의 성능 영향과 앱의 구성 요소 계층 구조를 최적화하는 방법에 관한 자세한 내용은 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="2bbcf-122">`StateHasChanged`를 호출하는 경우</span><span class="sxs-lookup"><span data-stu-id="2bbcf-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="2bbcf-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> 메서드를 사용하여 언제든지 렌더링을 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-123">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="2bbcf-124">그러나 불필요하게 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하지 않도록 주의해야 합니다. 해당 호출은 불필요한 렌더링 비용을 발생시키므로 일반적인 실수입니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="2bbcf-125">다음과 같은 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출할 필요가 ‘없습니다’.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-125">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="2bbcf-126"><xref:Microsoft.AspNetCore.Components.ComponentBase>가 대부분 루틴 이벤트 처리기의 렌더링을 트리거한 후 동기적 또는 비동기적으로 이벤트를 정기적으로 처리하는 경우.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="2bbcf-127"><xref:Microsoft.AspNetCore.Components.ComponentBase>가 일반적인 수명 주기 이벤트의 렌더링을 트리거한 후 동기적 또는 비동기적 여부와 관계없이 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 또는 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set)와 같은 일반적인 수명 주기 논리를 구현하는 경우.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="2bbcf-128">그러나 다음 섹션에서 설명하는 사례에서는 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하는 것이 적합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="2bbcf-129">비동기 처리기는 여러 비동기 단계를 포함함</span><span class="sxs-lookup"><span data-stu-id="2bbcf-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="2bbcf-130">Blazor 렌더링 및 이벤트 처리 시스템 외부에서 호출 수신</span><span class="sxs-lookup"><span data-stu-id="2bbcf-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="2bbcf-131">특정 이벤트가 다시 렌더링하는 하위 트리 외부에서 구성 요소를 렌더링하려면</span><span class="sxs-lookup"><span data-stu-id="2bbcf-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="2bbcf-132">비동기 처리기는 여러 비동기 단계를 포함함</span><span class="sxs-lookup"><span data-stu-id="2bbcf-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="2bbcf-133">클릭할 때마다 개수를 4번 업데이트하는 다음 `Counter` 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-133">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="2bbcf-134">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="2bbcf-134">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="2bbcf-135">.NET에서 작업이 정의되는 방식으로 인해 <xref:System.Threading.Tasks.Task>의 수신자는 중간 비동기 상태가 아니라 최종 완료만 관찰할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-135">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="2bbcf-136">따라서 <xref:Microsoft.AspNetCore.Components.ComponentBase>는 <xref:System.Threading.Tasks.Task>가 처음 반환될 경우와 <xref:System.Threading.Tasks.Task>가 마지막으로 완료될 경우에만 다시 렌더링을 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-136">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="2bbcf-137">다른 중간 지점에 다시 렌더링하는 것은 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-137">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="2bbcf-138">중간 지점에서 다시 렌더링하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-138">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="2bbcf-139">Blazor 렌더링 및 이벤트 처리 시스템 외부에서 호출 수신</span><span class="sxs-lookup"><span data-stu-id="2bbcf-139">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="2bbcf-140"><xref:Microsoft.AspNetCore.Components.ComponentBase>는 자체 수명 주기 메서드 및 Blazor 트리거 이벤트만 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="2bbcf-141"><xref:Microsoft.AspNetCore.Components.ComponentBase>는 코드에서 발생할 수 있는 다른 이벤트를 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="2bbcf-142">예를 들어, 사용자 지정 데이터 저장소에서 발생한 C# 이벤트는 Blazor에서 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-142">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="2bbcf-143">해당 이벤트가 다시 렌더링을 트리거하여 UI에 업데이트된 값을 표시하게 하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-143">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="2bbcf-144">다른 사용 사례에서는 <xref:System.Timers.Timer?displayProperty=fullName>를 사용하여 정기적으로 개수를 업데이트하고 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하여 UI를 업데이트하는 다음 `Counter` 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-144">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="2bbcf-145">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="2bbcf-145">`Pages/CounterWithTimerDisposal.razor`:</span></span>

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

<span data-ttu-id="2bbcf-146">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="2bbcf-146">In the preceding example:</span></span>

* <span data-ttu-id="2bbcf-147">Blazor가 콜백에서 `currentCount`의 변경 내용을 인식하지 못하기 때문에 `OnTimerCallback`은 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-147">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="2bbcf-148">`OnTimerCallback`은 Blazor 관리형 렌더링 흐름 또는 이벤트 알림 외부에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="2bbcf-149">구성 요소는 <xref:System.IDisposable>을 구현합니다. 여기서 <xref:System.Timers.Timer>는 프레임워크가 `Dispose` 메서드를 호출할 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="2bbcf-150">자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="2bbcf-151">마찬가지로, 콜백이 Blazor의 동기화 컨텍스트 외부에서 호출되기 때문에 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType>로 논리를 래핑하여 렌더러의 동기화 컨텍스트로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-151">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="2bbcf-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 렌더러의 동기화 컨텍스트에서만 호출할 수 있으며 이외의 경우에는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="2bbcf-153">이는 다른 UI 프레임워크의 UI 스레드로 마샬링하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="2bbcf-154">특정 이벤트가 다시 렌더링하는 하위 트리 외부에서 구성 요소를 렌더링하려면</span><span class="sxs-lookup"><span data-stu-id="2bbcf-154">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="2bbcf-155">UI에는 이벤트를 한 구성 요소에 디스패치하고, 일부 상태를 변경하고, 이벤트를 수신하는 항목의 하위 항목이 아닌 완전히 다른 구성 요소를 다시 렌더링해야 하는 작업이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-155">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="2bbcf-156">해당 시나리오를 처리하는 한 가지 방법은 DI 서비스와 같은 ‘상태 관리’ 클래스를 여러 구성 요소에 삽입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-156">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="2bbcf-157">한 구성 요소가 상태 관리자에서 메서드를 호출하면 상태 관리자가 C# 이벤트를 발생시킨 다음, 독립 구성 요소가 이를 수신할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-157">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="2bbcf-158">해당 C# 이벤트는 Blazor 렌더링 파이프라인 외부에 있으므로 상태 관리자의 이벤트에 대한 응답으로 렌더링하려는 다른 구성 요소에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-158">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="2bbcf-159">해당 사례는 [이전 섹션](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) 섹션에서 <xref:System.Timers.Timer?displayProperty=fullName>를 사용하는 이전 사례와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-159">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="2bbcf-160">실행 호출 스택은 일반적으로 렌더러의 동기화 컨텍스트에 남아 있으므로 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>는 일반적으로 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-160">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="2bbcf-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>는 논리가 <xref:System.Threading.Tasks.Task>에서 <xref:System.Threading.Tasks.Task.ContinueWith%2A>를 호출하거나 [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A)를 사용하여 <xref:System.Threading.Tasks.Task>를 기다리는 것과 같은 동기화 컨텍스트를 이스케이프하는 경우에만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2bbcf-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
