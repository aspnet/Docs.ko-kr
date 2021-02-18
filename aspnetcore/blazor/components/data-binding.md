---
title: ASP.NET Core Blazor 데이터 바인딩
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 76cc0ddc46dd08a5b8b88cf6045b84beab57c295
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280134"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="6a151-103">ASP.NET Core Blazor 데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="6a151-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="6a151-104">Razor 구성 요소는 필드, 속성 또는 Razor 식 값을 사용하여 [`@bind`](xref:mvc/views/razor#bind)라는 HTML 요소 특성을 통해 데이터 바인딩 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-104">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="6a151-105">다음 예제에서는 `<input>` 요소를 `currentValue` 필드에 바인딩하고 `<input>` 요소를 `CurrentValue` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-105">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="6a151-106">요소 중 하나가 포커스를 잃으면 바인딩된 필드 또는 속성이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-106">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="6a151-107">텍스트 상자는 필드 또는 속성 값 변경에 대한 대응이 아니라, 구성 요소가 렌더링되는 경우에만 UI에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-107">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="6a151-108">이벤트 처리기 코드를 실행하면 구성 요소가 자체적으로 렌더링되므로 필드 및 속성 업데이트는 ‘일반적으로’ 이벤트 처리기가 트리거되는 즉시 UI에 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-108">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="6a151-109">`CurrentValue` 속성에 [`@bind`](xref:mvc/views/razor#bind)를 사용하는 것(`<input @bind="CurrentValue" />`)은 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-109">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="6a151-110">구성 요소가 렌더링되면 input 요소의 `value`를 `CurrentValue` 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-110">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="6a151-111">사용자가 텍스트 상자에 입력을 하고 요소 포커스를 변경하면 `onchange` 이벤트가 발생하고 `CurrentValue` 속성이 변경된 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-111">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="6a151-112">실제로는 [`@bind`](xref:mvc/views/razor#bind)에서 형식 변환이 수행되는 경우를 처리하므로 코드 생성은 더 복잡해집니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-112">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="6a151-113">원칙적으로 [`@bind`](xref:mvc/views/razor#bind)는 식의 현재 값을 `value` 특성과 연결하고 등록된 처리기를 사용하여 변경 내용을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-113">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="6a151-114">또한 `event` 매개 변수에 `@bind:event` 특성을 포함하여 다른 이벤트에 속성 또는 필드를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-114">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="6a151-115">다음 예에서는 `oninput` 이벤트에서 `CurrentValue` 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-115">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="6a151-116">요소가 포커스를 잃을 때 발생하는 `onchange`와는 달리 텍스트 상자의 값이 변경될 때 `oninput`이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-116">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="6a151-117">특성 바인딩은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-117">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="6a151-118">`@bind`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-118">`@bind` is valid.</span></span>
* <span data-ttu-id="6a151-119">`@Bind` 및 `@BIND`는 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-119">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="6a151-120">구문 분석할 수 없는 값</span><span class="sxs-lookup"><span data-stu-id="6a151-120">Unparsable values</span></span>

<span data-ttu-id="6a151-121">사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공하면 바인딩 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-121">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="6a151-122">다음 시나리오를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="6a151-122">Consider the following scenario:</span></span>

* <span data-ttu-id="6a151-123">`<input>` 요소는 초기 값 `123`을 사용하여 `int` 형식에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-123">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="6a151-124">사용자는 페이지에서 요소의 값을 `123.45`로 업데이트하고 요소 포커스를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-124">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="6a151-125">위의 시나리오에서 요소의 값은 `123`으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-125">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="6a151-126">값 `123.45`가 원래 값 `123`에 따라 거부되면 사용자는 해당 값이 수용되지 않았다는 것을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-126">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="6a151-127">기본적으로 바인딩은 요소의 `onchange` 이벤트(`@bind="{PROPERTY OR FIELD}"`)에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-127">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="6a151-128">`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`를 사용하여 다른 이벤트에 대한 바인딩을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-128">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="6a151-129">`oninput` 이벤트(`@bind:event="oninput"`)의 경우 구문 분석할 수 있는 값을 도입하는 키 입력 후에 되돌려집니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-129">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="6a151-130">`oninput` 이벤트의 대상을 `int` 바인딩 형식으로 지정하는 경우 사용자는 `.` 문자를 입력할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-130">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="6a151-131">`.` 문자는 즉시 제거되므로 사용자는 정수만 허용된다는 즉각적인 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-131">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="6a151-132">사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합하지 않은 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-132">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="6a151-133">대안은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-133">Alternatives include:</span></span>

* <span data-ttu-id="6a151-134">`oninput` 이벤트를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6a151-134">Don't use the `oninput` event.</span></span> <span data-ttu-id="6a151-135">기본 `onchange` 이벤트를 사용합니다(`@bind="{PROPERTY OR FIELD}"`만 지정). 이 경우 요소가 포커스를 잃을 때까지 잘못된 값은 복귀되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-135">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="6a151-136">`int?` 또는 `string`과 같은 nullable 형식에 바인딩하고 잘못된 항목을 처리하기 위한 사용자 지정 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-136">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="6a151-137"><xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 또는 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>와 같은 [양식 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-137">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="6a151-138">양식 유효성 검사 구성 요소에는 잘못된 입력을 관리하기 위한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-138">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="6a151-139">자세한 내용은 <xref:blazor/forms-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6a151-139">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="6a151-140">양식 유효성 검사 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="6a151-140">Form validation components:</span></span>
  * <span data-ttu-id="6a151-141">사용자가 연결된 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 잘못된 입력을 제공하고 유효성 검사 오류를 수신할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-141">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="6a151-142">사용자가 추가 Webform 데이터를 입력하는 것을 방해하지 않고 UI에서 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-142">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="6a151-143">형식 문자열</span><span class="sxs-lookup"><span data-stu-id="6a151-143">Format strings</span></span>

<span data-ttu-id="6a151-144">데이터 바인딩은 `@bind:format`을 사용하여 <xref:System.DateTime> 형식 문자열에 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-144">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="6a151-145">통화 또는 숫자 형식 등의 다른 형식 식은 현재 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-145">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="6a151-146">위의 코드에서 `<input>` 요소의 필드 형식(`type`)은 기본적으로 `text`입니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-146">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="6a151-147">`@bind:format`은 다음 .NET 형식을 바인딩하는 데 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-147">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="6a151-148"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="6a151-148"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="6a151-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="6a151-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="6a151-150">`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-150">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="6a151-151">이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석하는 데도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-151">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="6a151-152">Blazor에서는 기본적으로 날짜 형식을 지정할 수 있도록 지원하므로 `date` 필드의 형식을 지정하는 것은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-152">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="6a151-153">권장 사항에도 불구하고 `date` 필드의 형식이 제공된 경우 바인딩이 올바르게 작동하려면 `yyyy-MM-dd` 날짜 형식만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-153">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="6a151-154">구성 요소 매개 변수를 사용하여 바인딩</span><span class="sxs-lookup"><span data-stu-id="6a151-154">Binding with component parameters</span></span>

<span data-ttu-id="6a151-155">일반적인 시나리오는 자식 구성 요소의 속성을 부모의 속성에 바인딩하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-155">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="6a151-156">여러 수준의 바인딩이 동시에 발생하기 때문에 이 시나리오를 *체인 바인딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-156">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="6a151-157">[구성 요소 매개 변수](xref:blazor/components/index#component-parameters)는 `@bind-{PROPERTY}` 구문을 사용하여 부모 구성 요소의 속성 바인딩을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-157">[Component parameters](xref:blazor/components/index#component-parameters) permit binding properties of a parent component with `@bind-{PROPERTY}` syntax.</span></span>

<span data-ttu-id="6a151-158">자식 구성 요소에 [`@bind`](xref:mvc/views/razor#bind) 구문을 사용하여 체인 바인딩을 구현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-158">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="6a151-159">자식 구성 요소에서 부모의 속성 업데이트를 지원하려면 이벤트 처리기와 값을 별도로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-159">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="6a151-160">부모 구성 요소는 계속 [`@bind`](xref:mvc/views/razor#bind) 구문을 활용하여 자식 구성 요소와 데이터 바인딩을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-160">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="6a151-161">다음 `Child` 구성 요소(`Shared/Child.razor`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-161">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="6a151-162">콜백(<xref:Microsoft.AspNetCore.Components.EventCallback%601>)의 이름은 구성 요소 매개 변수 이름 뒤에 “`Changed`” 접미사(`{PARAMETER NAME}Changed`)를 붙여 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-162">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="6a151-163">이전 예제에서 콜백의 이름은 `YearChanged`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-163">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="6a151-164"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType>는 제공된 인수를 사용하여 바인딩과 연결된 대리자를 호출하고 변경된 속성에 대한 이벤트 알림을 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-164"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="6a151-165">다음 `Parent` 구성 요소(`Parent.razor`)에서 `year` 필드는 자식 구성 요소의 `Year` 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-165">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="6a151-166">`Year` 매개 변수는 `Year` 매개 변수 형식과 일치하는 도우미 `YearChanged` 이벤트를 포함하기 때문에 바인딩 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-166">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="6a151-167">일반적으로 속성은 처리기에 할당된 `@bind-{PROPERTY}:event` 특성을 포함하여 해당 이벤트 처리기에 바인딩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-167">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="6a151-168">`<Child @bind-Year="year" />`는 다음과 같이 작성하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-168">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="6a151-169">더욱 정교한 실제 예제에서 다음 `PasswordField` 구성 요소(`PasswordField.razor`)는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-169">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="6a151-170">`<input>` 요소의 값을 `password` 필드로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-170">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="6a151-171">자식의 `password` 필드의 현재 값을 인수로 전달하는 [`EventCallback`](xref:blazor/components/event-handling#eventcallback)을 사용하여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-171">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="6a151-172">`ToggleShowPassword` 메서드를 트리거하는 데 `onclick` 이벤트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-172">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="6a151-173">자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6a151-173">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="6a151-174">`PasswordField` 구성 요소는 다른 구성 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-174">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="6a151-175">바인딩의 대리자를 호출하는 메서드에서 검사를 수행하거나 오류를 트래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-175">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="6a151-176">다음 예에서는 암호 값에 공백을 사용하는 경우 사용자에게 즉각적인 피드백을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-176">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="6a151-177"><xref:Microsoft.AspNetCore.Components.EventCallback%601>에 대한 자세한 내용은 <xref:blazor/components/event-handling#eventcallback>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6a151-177">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="6a151-178">셋 이상의 구성 요소에서 바인딩</span><span class="sxs-lookup"><span data-stu-id="6a151-178">Bind across more than two components</span></span>

<span data-ttu-id="6a151-179">중첩된 구성 요소는 개수에 관계없이 바인딩할 수 있지만 데이터의 단방향 흐름을 준수해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-179">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="6a151-180">변경 알림은 *계층 구조를 따라 올라갑니다*.</span><span class="sxs-lookup"><span data-stu-id="6a151-180">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="6a151-181">새 매개 변수 값은 *계층 구조를 따라 내려옵니다*.</span><span class="sxs-lookup"><span data-stu-id="6a151-181">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="6a151-182">일반적이고 권장되는 방법은 업데이트할 상태에 대한 혼동을 방지할 수 있게, 부모 구성 요소에 기본 데이터만 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-182">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="6a151-183">다음 구성 요소는 이 개념을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6a151-183">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="6a151-184">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="6a151-184">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="6a151-185">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="6a151-185">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="6a151-186">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="6a151-186">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="6a151-187">중첩되지 않을 수도 있는 구성 요소 간에 메모리 내 데이터를 공유하는 데 적합한 다른 방법은 <xref:blazor/state-management> 문서의 메모리 내 상태 컨테이너 서비스 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6a151-187">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see the *In-memory state container service* section of the <xref:blazor/state-management> article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a151-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6a151-188">Additional resources</span></span>

* [<span data-ttu-id="6a151-189">양식의 라디오 단추에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6a151-189">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="6a151-190">양식의 C# 개체 `null` 값에 `<select>` 요소 옵션 바인딩</span><span class="sxs-lookup"><span data-stu-id="6a151-190">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
