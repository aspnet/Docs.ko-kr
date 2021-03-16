---
title: ASP.NET Core Blazor 이벤트 처리
author: guardrex
description: 이벤트 인수 형식, 이벤트 콜백, 기본 브라우저 이벤트 관리를 비롯한 Blazor의 이벤트 처리 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 4c574a630176010ab30006b53c42e5cad7f93a54
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395190"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor 이벤트 처리

Razor 구성 요소는 이벤트 처리 기능을 제공합니다. 대리자 형식 값을 사용하는 [`@on{EVENT}`](xref:mvc/views/razor#onevent)(예: `@onclick`)라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성 값을 이벤트 처리기로 취급합니다.

다음 코드는 UI에서 단추를 선택할 때 `UpdateHeading` 메서드를 호출합니다.

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

다음 코드는 UI에서 확인란을 변경할 때 `CheckChanged` 메서드를 호출합니다.

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

이벤트 처리기는 비동기로, <xref:System.Threading.Tasks.Task>를 반환할 수도 있습니다. [StateHasChanged](xref:blazor/components/lifecycle#state-changes)를 수동으로 호출할 필요가 없습니다. 예외가 발생하면 기록됩니다.

다음 예제에서는 단추를 선택할 때 `UpdateHeading`이 비동기적으로 호출됩니다.

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a>이벤트 인수 형식

일부 이벤트의 경우 이벤트 인수 형식이 허용됩니다. 이벤트 메서드 정의에서 이벤트 매개 변수를 지정하는 것은 선택 사항이며 이벤트 유형이 메서드에서 사용되는 경우에만 필요합니다. 다음 예제에서는 `ShowMessage` 메서드에서 `MouseEventArgs` 이벤트 인수를 사용하여 메시지 텍스트를 설정합니다.

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

지원되는 <xref:System.EventArgs>는 다음 표에 나와 있습니다.

::: moniker range=">= aspnetcore-5.0"

| 이벤트            | 클래스  | DOM 이벤트 및 참고 사항 |
| ---------------- | ------ | -------------------- |
| 클립보드        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| 끌기             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 및 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem>은 끌어온 항목 데이터를 포함합니다.<br><br>[JS interop](xref:blazor/call-javascript-from-dotnet)을 [HTML 끌어서 놓기 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)와 함께 사용하여 Blazor 앱에서 끌어서 놓기를 구현합니다. |
| Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| 이벤트            | <xref:System.EventArgs> | *일반*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*클립보드*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*입력*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*미디어*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>에는 이벤트 이름과 이벤트 인수 형식 간의 매핑을 구성하는 특성이 있습니다. |
| 포커스            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>`relatedTarget` 지원을 포함하지 않습니다. |
| 입력            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| 키보드         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| 마우스            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 마우스 포인터    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 마우스 휠      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| 진행률         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 터치            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>는 터치 인식 디바이스에서 단일 접촉 지점을 나타냅니다. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| 이벤트            | 클래스 | DOM 이벤트 및 참고 사항 |
| ---------------- | ----- | -------------------- |
| 클립보드        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| 끌기             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 및 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem>은 끌어온 항목 데이터를 포함합니다.<br><br>[JS interop](xref:blazor/call-javascript-from-dotnet)을 [HTML 끌어서 놓기 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)와 함께 사용하여 Blazor 앱에서 끌어서 놓기를 구현합니다. |
| Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| 이벤트            | <xref:System.EventArgs> | *일반*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*클립보드*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*입력*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*미디어*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>에는 이벤트 이름과 이벤트 인수 형식 간의 매핑을 구성하는 특성이 있습니다. |
| 포커스            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>`relatedTarget` 지원을 포함하지 않습니다. |
| 입력            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| 키보드         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| 마우스            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 마우스 포인터    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 마우스 휠      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| 진행률         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 터치            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>는 터치 인식 디바이스에서 단일 접촉 지점을 나타냅니다. |

::: moniker-end

자세한 내용은 다음 자료를 참조하세요.

* [ASP.NET Core 참조 원본(dotnet/aspnetcore `main` 분기)의 `EventArgs` 클래스](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* [MDN 웹 문서: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): 각 DOM 이벤트를 지원하는 HTML 요소에 대한 정보를 포함합니다.

## <a name="lambda-expressions"></a>람다 식

[람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 사용할 수도 있습니다.

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

요소 집합을 반복하는 경우와 같이 추가 값을 둘러싸는 것이 편리한 경우가 많습니다. 다음 예제에서는 UI에서 선택할 경우 각각 이벤트 인수(<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>)와 해당 단추 번호(`buttonNumber`)를 전달하여 `UpdateHeading`을 호출하는 세 개의 단추를 만듭니다.

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 위 `for` 루프 예제의 `i`와 같이 람다 식에서 직접 루프 변수를 사용하지 **않습니다**. 직접 사용하는 경우 모든 람다 식에서 동일한 변수가 사용되어 모든 람다에서 동일한 값이 사용됩니다. 변수 값을 항상 지역 변수에 캡처한 다음에 사용합니다. 위의 예제에서는 루프 변수 `i`가 `buttonNumber`에 할당됩니다.

## <a name="eventcallback"></a>EventCallback

중첩된 구성 요소를 사용하는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생할 때 부모 구성 요소의 메서드를 실행하는 것입니다. 자식 구성 요소에서 발생하는 `onclick` 이벤트가 일반적인 사용 사례입니다. 구성 요소 간에 이벤트를 공개하려면 <xref:Microsoft.AspNetCore.Components.EventCallback>을 사용합니다. 부모 구성 요소는 자식 구성 요소의 <xref:Microsoft.AspNetCore.Components.EventCallback>에 콜백 메서드를 할당할 수 있습니다.

샘플 앱(`Components/ChildComponent.razor`)의 `ChildComponent`는 단추의 `onclick` 처리기가 샘플의 `ParentComponent`에서 <xref:Microsoft.AspNetCore.Components.EventCallback> 대리자를 수신하도록 설정된 방법을 보여 줍니다. <xref:Microsoft.AspNetCore.Components.EventCallback>은 주변 디바이스의 `onclick` 이벤트에 적합한 `MouseEventArgs` 형식입니다.

[!code-razor[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent`는 자식의 <xref:Microsoft.AspNetCore.Components.EventCallback%601>(`OnClickCallback`)를 해당 `ShowMessage` 메서드에 설정합니다.

`Pages/ParentComponent.razor`:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

`ChildComponent`에서 단추를 선택한 경우

* `ParentComponent`의 `ShowMessage` 메서드가 호출됩니다. `messageText`가 업데이트되고 `ParentComponent`에 표시됩니다.
* [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) 호출은 콜백의 메서드(`ShowMessage`)에 필요하지 않습니다. 자식 이벤트가 자식 내에서 실행되는 이벤트 처리기에서 다시 렌더링되는 구성 요소를 트리거하는 것처럼 `ParentComponent`를 다시 렌더링하기 위해 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 자동으로 호출됩니다. 자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.

<xref:Microsoft.AspNetCore.Components.EventCallback> 및 <xref:Microsoft.AspNetCore.Components.EventCallback%601>는 비동기 대리자를 허용합니다. <xref:Microsoft.AspNetCore.Components.EventCallback>은 약한 형식이며 `InvokeAsync(Object)`에서 모든 형식 인수를 전달할 수 있습니다. <xref:Microsoft.AspNetCore.Components.EventCallback%601>은 강력한 형식이며 `InvokeAsync(T)`에서 `TValue`에 할당할 수 있는 `T` 인수를 전달해야 합니다.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A>를 사용하여 <xref:Microsoft.AspNetCore.Components.EventCallback> 또는 <xref:Microsoft.AspNetCore.Components.EventCallback%601>를 호출하고 <xref:System.Threading.Tasks.Task>를 기다립니다.

```csharp
await OnClickCallback.InvokeAsync(arg);
```

이벤트 처리 및 바인딩 구성 요소 매개 변수에 <xref:Microsoft.AspNetCore.Components.EventCallback> 및 <xref:Microsoft.AspNetCore.Components.EventCallback%601>을 사용합니다.

<xref:Microsoft.AspNetCore.Components.EventCallback>보다 강력한 형식의 <xref:Microsoft.AspNetCore.Components.EventCallback%601>를 사용하는 것이 좋습니다. <xref:Microsoft.AspNetCore.Components.EventCallback%601>는 구성 요소 사용자에게 더 나은 오류 피드백을 제공합니다. 다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수 지정은 선택 사항입니다. 콜백에 전달되는 값이 없는 경우 <xref:Microsoft.AspNetCore.Components.EventCallback>을 사용합니다.

## <a name="prevent-default-actions"></a>기본 작업 방지

[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 지시문 특성을 사용하여 이벤트의 기본 작업을 방지할 수 있습니다.

입력 디바이스에서 키를 선택하고 요소 포커스가 텍스트 상자에 놓이면 일반적으로 브라우저의 텍스트 상자에 키 문자가 표시됩니다. 다음 예제에서는 `@onkeypress:preventDefault` 지시문 특성을 지정하여 기본 동작을 방지합니다. 카운터가 증가하고 **+** 키가 `<input>` 요소의 값에 캡처되지 않습니다.

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

값 없이 `@on{EVENT}:preventDefault` 특성을 지정하는 것은 `@on{EVENT}:preventDefault="true"`와 동일합니다.

특성 값으로 식을 사용할 수도 있습니다. 다음 예제에서 `shouldPreventDefault`는 `true` 또는 `false`로 설정되는 `bool` 필드입니다.

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a>이벤트 전파 중지

[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 지시문 특성을 사용하여 이벤트 전파를 중지할 수 있습니다.

다음 예제에서 확인란을 선택하면 두 번째 자식 `<div>`의 클릭 이벤트가 부모 `<div>`로 전파되지 않습니다.

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>요소에 포커스

[요소 참조](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)에서 `FocusAsync`를 호출하여 코드의 요소에 포커스를 둡니다.

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
