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
# <a name="aspnet-core-blazor-component-rendering"></a>ASP.NET Core Blazor 구성 요소 렌더링

구성 요소는 부모 구성 요소에 의해 구성 요소 계층 구조에 처음 추가될 때 렌더링‘되어야’ 합니다. 구성 요소는 이 시점에만 엄격하게 렌더링해야 합니다.

구성 요소는 자체 논리 및 규칙에 따라 다른 시간에 렌더링하도록 선택‘할 수 있습니다’.

## <a name="conventions-for-componentbase"></a>`ComponentBase`의 규칙

기본적으로 Razor 구성 요소(`.razor`)는 다음 시간에 다시 렌더링을 트리거하는 논리를 포함하는 <xref:Microsoft.AspNetCore.Components.ComponentBase> 기본 클래스에서 상속됩니다.

* 부모 구성 요소에서 업데이트된 매개 변수 세트를 적용한 후.
* 계단식 배열 매개 변수의 업데이트된 값을 적용한 후.
* 이벤트 알림 및 자체 이벤트 처리기 중 하나를 호출한 후.
* 자체 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 메서드를 호출한 후.

<xref:Microsoft.AspNetCore.Components.ComponentBase>에서 상속된 구성 요소는 다음 중 하나에 해당하는 경우 매개 변수 업데이트로 인해 다시 렌더링을 건너뜁니다.

* 모든 매개 변수 값은 알려진 변경 불가능 기본 형식(예: `int`, `string`, `DateTime`)이며 이전 매개 변수 세트가 설정된 이후 변경되지 않았습니다.
* 구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 메서드가 `false`를 반환합니다.

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>에 대한 자세한 내용은 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>를 참조하세요.

## <a name="control-the-rendering-flow"></a>렌더링 흐름 제어

대부분의 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase> 규칙은 이벤트가 발생한 후 구성 요소 다시 렌더링의 올바른 하위 세트를 생성합니다. 개발자는 일반적으로 프레임워크에 다시 렌더링할 구성 요소와 이 구성 요소를 다시 렌더링할 시기를 알리는 수동 논리를 제공할 필요가 없습니다. 프레임워크 규칙의 전반적인 효과는 이벤트를 수신하는 구성 요소가 스스로 다시 렌더링되어 매개 변수 값이 변경되었을 수 있는 하위 구성 요소의 다시 렌더링을 재귀적으로 트리거합니다.

프레임워크 규칙의 성능 영향과 앱의 구성 요소 계층 구조를 최적화하는 방법에 관한 자세한 내용은 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>를 참조하세요.

## <a name="when-to-call-statehaschanged"></a>`StateHasChanged`를 호출하는 경우

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> 메서드를 사용하여 언제든지 렌더링을 트리거할 수 있습니다. 그러나 불필요하게 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하지 않도록 주의해야 합니다. 해당 호출은 불필요한 렌더링 비용을 발생시키므로 일반적인 실수입니다.

다음과 같은 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출할 필요가 ‘없습니다’.

* <xref:Microsoft.AspNetCore.Components.ComponentBase>가 대부분 루틴 이벤트 처리기의 렌더링을 트리거한 후 동기적 또는 비동기적으로 이벤트를 정기적으로 처리하는 경우.
* <xref:Microsoft.AspNetCore.Components.ComponentBase>가 일반적인 수명 주기 이벤트의 렌더링을 트리거한 후 동기적 또는 비동기적 여부와 관계없이 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 또는 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set)와 같은 일반적인 수명 주기 논리를 구현하는 경우.

그러나 다음 섹션에서 설명하는 사례에서는 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하는 것이 적합할 수 있습니다.

* [비동기 처리기는 여러 비동기 단계를 포함함](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Blazor 렌더링 및 이벤트 처리 시스템 외부에서 호출 수신](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [특정 이벤트가 다시 렌더링하는 하위 트리 외부에서 구성 요소를 렌더링하려면](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>비동기 처리기는 여러 비동기 단계를 포함함

클릭할 때마다 개수를 4번 업데이트하는 다음 `Counter` 구성 요소를 고려합니다.

`Pages/Counter.razor`:

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

.NET에서 작업이 정의되는 방식으로 인해 <xref:System.Threading.Tasks.Task>의 수신자는 중간 비동기 상태가 아니라 최종 완료만 관찰할 수 있습니다. 따라서 <xref:Microsoft.AspNetCore.Components.ComponentBase>는 <xref:System.Threading.Tasks.Task>가 처음 반환될 경우와 <xref:System.Threading.Tasks.Task>가 마지막으로 완료될 경우에만 다시 렌더링을 트리거할 수 있습니다. 다른 중간 지점에 다시 렌더링하는 것은 알 수 없습니다. 중간 지점에서 다시 렌더링하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 사용합니다.

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a>Blazor 렌더링 및 이벤트 처리 시스템 외부에서 호출 수신

<xref:Microsoft.AspNetCore.Components.ComponentBase>는 자체 수명 주기 메서드 및 Blazor 트리거 이벤트만 알 수 있습니다. <xref:Microsoft.AspNetCore.Components.ComponentBase>는 코드에서 발생할 수 있는 다른 이벤트를 알 수 없습니다. 예를 들어, 사용자 지정 데이터 저장소에서 발생한 C# 이벤트는 Blazor에서 알 수 없습니다. 해당 이벤트가 다시 렌더링을 트리거하여 UI에 업데이트된 값을 표시하게 하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 사용합니다.

다른 사용 사례에서는 <xref:System.Timers.Timer?displayProperty=fullName>를 사용하여 정기적으로 개수를 업데이트하고 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하여 UI를 업데이트하는 다음 `Counter` 구성 요소를 고려합니다.

`Pages/CounterWithTimerDisposal.razor`:

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

앞의 예제에서:

* Blazor가 콜백에서 `currentCount`의 변경 내용을 인식하지 못하기 때문에 `OnTimerCallback`은 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출해야 합니다. `OnTimerCallback`은 Blazor 관리형 렌더링 흐름 또는 이벤트 알림 외부에서 실행됩니다.
* 구성 요소는 <xref:System.IDisposable>을 구현합니다. 여기서 <xref:System.Timers.Timer>는 프레임워크가 `Dispose` 메서드를 호출할 때 삭제됩니다. 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>를 참조하세요.

마찬가지로, 콜백이 Blazor의 동기화 컨텍스트 외부에서 호출되기 때문에 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType>로 논리를 래핑하여 렌더러의 동기화 컨텍스트로 이동해야 합니다. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 렌더러의 동기화 컨텍스트에서만 호출할 수 있으며 이외의 경우에는 예외를 throw합니다. 이는 다른 UI 프레임워크의 UI 스레드로 마샬링하는 것과 같습니다.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>특정 이벤트가 다시 렌더링하는 하위 트리 외부에서 구성 요소를 렌더링하려면

UI에는 이벤트를 한 구성 요소에 디스패치하고, 일부 상태를 변경하고, 이벤트를 수신하는 항목의 하위 항목이 아닌 완전히 다른 구성 요소를 다시 렌더링해야 하는 작업이 포함될 수 있습니다.

해당 시나리오를 처리하는 한 가지 방법은 DI 서비스와 같은 ‘상태 관리’ 클래스를 여러 구성 요소에 삽입하는 것입니다. 한 구성 요소가 상태 관리자에서 메서드를 호출하면 상태 관리자가 C# 이벤트를 발생시킨 다음, 독립 구성 요소가 이를 수신할 수 있습니다.

해당 C# 이벤트는 Blazor 렌더링 파이프라인 외부에 있으므로 상태 관리자의 이벤트에 대한 응답으로 렌더링하려는 다른 구성 요소에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출합니다.

해당 사례는 [이전 섹션](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) 섹션에서 <xref:System.Timers.Timer?displayProperty=fullName>를 사용하는 이전 사례와 비슷합니다. 실행 호출 스택은 일반적으로 렌더러의 동기화 컨텍스트에 남아 있으므로 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>는 일반적으로 필요하지 않습니다. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>는 논리가 <xref:System.Threading.Tasks.Task>에서 <xref:System.Threading.Tasks.Task.ContinueWith%2A>를 호출하거나 [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A)를 사용하여 <xref:System.Threading.Tasks.Task>를 기다리는 것과 같은 동기화 컨텍스트를 이스케이프하는 경우에만 필요합니다.
