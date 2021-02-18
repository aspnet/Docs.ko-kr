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
ms.openlocfilehash: f6a93eb9d95182d29a60cc1a5c48122b9166aa84
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280149"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="18b88-103">ASP.NET Core Blazor 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="18b88-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="18b88-104">Razor 구성 요소는 이벤트 처리 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-104">Razor components provide event handling features.</span></span> <span data-ttu-id="18b88-105">대리자 형식 값을 사용하는 [`@on{EVENT}`](xref:mvc/views/razor#onevent)(예: `@onclick`)라는 HTML 요소 특성의 경우 Razor 구성 요소는 특성 값을 이벤트 처리기로 취급합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-105">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="18b88-106">다음 코드는 UI에서 단추를 선택할 때 `UpdateHeading` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-106">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="18b88-107">다음 코드는 UI에서 확인란을 변경할 때 `CheckChanged` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-107">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="18b88-108">이벤트 처리기는 비동기로, <xref:System.Threading.Tasks.Task>를 반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-108">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="18b88-109">[StateHasChanged](xref:blazor/components/lifecycle#state-changes)를 수동으로 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-109">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="18b88-110">예외가 발생하면 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-110">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="18b88-111">다음 예제에서는 단추를 선택할 때 `UpdateHeading`이 비동기적으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-111">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="18b88-112">이벤트 인수 형식</span><span class="sxs-lookup"><span data-stu-id="18b88-112">Event argument types</span></span>

<span data-ttu-id="18b88-113">일부 이벤트의 경우 이벤트 인수 형식이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-113">For some events, event argument types are permitted.</span></span> <span data-ttu-id="18b88-114">이벤트 메서드 정의에서 이벤트 매개 변수를 지정하는 것은 선택 사항이며 이벤트 유형이 메서드에서 사용되는 경우에만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-114">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="18b88-115">다음 예제에서는 `ShowMessage` 메서드에서 `MouseEventArgs` 이벤트 인수를 사용하여 메시지 텍스트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-115">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="18b88-116">지원되는 <xref:System.EventArgs>는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="18b88-117">이벤트</span><span class="sxs-lookup"><span data-stu-id="18b88-117">Event</span></span>            | <span data-ttu-id="18b88-118">클래스</span><span class="sxs-lookup"><span data-stu-id="18b88-118">Class</span></span>  | <span data-ttu-id="18b88-119">DOM 이벤트 및 참고 사항</span><span class="sxs-lookup"><span data-stu-id="18b88-119">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="18b88-120">클립보드</span><span class="sxs-lookup"><span data-stu-id="18b88-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="18b88-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="18b88-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="18b88-122">끌기</span><span class="sxs-lookup"><span data-stu-id="18b88-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="18b88-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="18b88-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="18b88-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 및 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem>은 끌어온 항목 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="18b88-125">[JS interop](xref:blazor/call-javascript-from-dotnet)을 [HTML 끌어서 놓기 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)와 함께 사용하여 Blazor 앱에서 끌어서 놓기를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-125">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="18b88-126">Error</span><span class="sxs-lookup"><span data-stu-id="18b88-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="18b88-127">이벤트</span><span class="sxs-lookup"><span data-stu-id="18b88-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="18b88-128">*일반*</span><span class="sxs-lookup"><span data-stu-id="18b88-128">*General*</span></span><br><span data-ttu-id="18b88-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="18b88-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="18b88-130">*클립보드*</span><span class="sxs-lookup"><span data-stu-id="18b88-130">*Clipboard*</span></span><br><span data-ttu-id="18b88-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="18b88-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="18b88-132">*입력*</span><span class="sxs-lookup"><span data-stu-id="18b88-132">*Input*</span></span><br><span data-ttu-id="18b88-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="18b88-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="18b88-134">*미디어*</span><span class="sxs-lookup"><span data-stu-id="18b88-134">*Media*</span></span><br><span data-ttu-id="18b88-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="18b88-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="18b88-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>에는 이벤트 이름과 이벤트 인수 형식 간의 매핑을 구성하는 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="18b88-137">포커스</span><span class="sxs-lookup"><span data-stu-id="18b88-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="18b88-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="18b88-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="18b88-139">`relatedTarget` 지원을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="18b88-140">입력</span><span class="sxs-lookup"><span data-stu-id="18b88-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="18b88-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="18b88-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="18b88-142">키보드</span><span class="sxs-lookup"><span data-stu-id="18b88-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="18b88-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="18b88-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="18b88-144">마우스</span><span class="sxs-lookup"><span data-stu-id="18b88-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="18b88-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="18b88-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="18b88-146">마우스 포인터</span><span class="sxs-lookup"><span data-stu-id="18b88-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="18b88-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="18b88-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="18b88-148">마우스 휠</span><span class="sxs-lookup"><span data-stu-id="18b88-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="18b88-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="18b88-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="18b88-150">진행률</span><span class="sxs-lookup"><span data-stu-id="18b88-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="18b88-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="18b88-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="18b88-152">터치</span><span class="sxs-lookup"><span data-stu-id="18b88-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="18b88-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="18b88-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="18b88-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>는 터치 인식 디바이스에서 단일 접촉 지점을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="18b88-155">이벤트</span><span class="sxs-lookup"><span data-stu-id="18b88-155">Event</span></span>            | <span data-ttu-id="18b88-156">클래스</span><span class="sxs-lookup"><span data-stu-id="18b88-156">Class</span></span> | <span data-ttu-id="18b88-157">DOM 이벤트 및 참고 사항</span><span class="sxs-lookup"><span data-stu-id="18b88-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="18b88-158">클립보드</span><span class="sxs-lookup"><span data-stu-id="18b88-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="18b88-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="18b88-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="18b88-160">끌기</span><span class="sxs-lookup"><span data-stu-id="18b88-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="18b88-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="18b88-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="18b88-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 및 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem>은 끌어온 항목 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="18b88-163">[JS interop](xref:blazor/call-javascript-from-dotnet)을 [HTML 끌어서 놓기 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)와 함께 사용하여 Blazor 앱에서 끌어서 놓기를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-163">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="18b88-164">Error</span><span class="sxs-lookup"><span data-stu-id="18b88-164">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="18b88-165">이벤트</span><span class="sxs-lookup"><span data-stu-id="18b88-165">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="18b88-166">*일반*</span><span class="sxs-lookup"><span data-stu-id="18b88-166">*General*</span></span><br><span data-ttu-id="18b88-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="18b88-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="18b88-168">*클립보드*</span><span class="sxs-lookup"><span data-stu-id="18b88-168">*Clipboard*</span></span><br><span data-ttu-id="18b88-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="18b88-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="18b88-170">*입력*</span><span class="sxs-lookup"><span data-stu-id="18b88-170">*Input*</span></span><br><span data-ttu-id="18b88-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="18b88-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="18b88-172">*미디어*</span><span class="sxs-lookup"><span data-stu-id="18b88-172">*Media*</span></span><br><span data-ttu-id="18b88-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="18b88-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="18b88-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>에는 이벤트 이름과 이벤트 인수 형식 간의 매핑을 구성하는 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="18b88-175">포커스</span><span class="sxs-lookup"><span data-stu-id="18b88-175">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="18b88-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="18b88-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="18b88-177">`relatedTarget` 지원을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-177">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="18b88-178">입력</span><span class="sxs-lookup"><span data-stu-id="18b88-178">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="18b88-179">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="18b88-179">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="18b88-180">키보드</span><span class="sxs-lookup"><span data-stu-id="18b88-180">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="18b88-181">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="18b88-181">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="18b88-182">마우스</span><span class="sxs-lookup"><span data-stu-id="18b88-182">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="18b88-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="18b88-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="18b88-184">마우스 포인터</span><span class="sxs-lookup"><span data-stu-id="18b88-184">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="18b88-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="18b88-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="18b88-186">마우스 휠</span><span class="sxs-lookup"><span data-stu-id="18b88-186">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="18b88-187">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="18b88-187">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="18b88-188">진행률</span><span class="sxs-lookup"><span data-stu-id="18b88-188">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="18b88-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="18b88-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="18b88-190">터치</span><span class="sxs-lookup"><span data-stu-id="18b88-190">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="18b88-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="18b88-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="18b88-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>는 터치 인식 디바이스에서 단일 접촉 지점을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="18b88-193">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="18b88-193">For more information, see the following resources:</span></span>

* <span data-ttu-id="18b88-194">[ASP.NET Core 참조 원본(dotnet/aspnetcore `master` 분기)의 `EventArgs` 클래스](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="18b88-194">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="18b88-195">`master` 분기는 ‘다음’ ASP.NET Core 릴리스에 대해 개발 중인 API를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-195">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="18b88-196">현재 릴리스의 경우 적절한 GitHub 리포지토리 분기(예: `release/3.1`)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-196">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="18b88-197">[MDN 웹 문서: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): 각 DOM 이벤트를 지원하는 HTML 요소에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-197">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="18b88-198">람다 식</span><span class="sxs-lookup"><span data-stu-id="18b88-198">Lambda expressions</span></span>

<span data-ttu-id="18b88-199">[람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-199">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="18b88-200">요소 집합을 반복하는 경우와 같이 추가 값을 둘러싸는 것이 편리한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-200">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="18b88-201">다음 예제에서는 UI에서 선택할 경우 각각 이벤트 인수(<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>)와 해당 단추 번호(`buttonNumber`)를 전달하여 `UpdateHeading`을 호출하는 세 개의 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-201">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="18b88-202">위 `for` 루프 예제의 `i`와 같이 람다 식에서 직접 루프 변수를 사용하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="18b88-202">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="18b88-203">직접 사용하는 경우 모든 람다 식에서 동일한 변수가 사용되어 모든 람다에서 동일한 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-203">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="18b88-204">변수 값을 항상 지역 변수에 캡처한 다음에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-204">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="18b88-205">위의 예제에서는 루프 변수 `i`가 `buttonNumber`에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-205">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="18b88-206">EventCallback</span><span class="sxs-lookup"><span data-stu-id="18b88-206">EventCallback</span></span>

<span data-ttu-id="18b88-207">중첩된 구성 요소를 사용하는 일반적인 시나리오는 자식 구성 요소 이벤트가 발생할 때 부모 구성 요소의 메서드를 실행하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-207">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="18b88-208">자식 구성 요소에서 발생하는 `onclick` 이벤트가 일반적인 사용 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-208">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="18b88-209">구성 요소 간에 이벤트를 공개하려면 <xref:Microsoft.AspNetCore.Components.EventCallback>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-209">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="18b88-210">부모 구성 요소는 자식 구성 요소의 <xref:Microsoft.AspNetCore.Components.EventCallback>에 콜백 메서드를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-210">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="18b88-211">샘플 앱(`Components/ChildComponent.razor`)의 `ChildComponent`는 단추의 `onclick` 처리기가 샘플의 `ParentComponent`에서 <xref:Microsoft.AspNetCore.Components.EventCallback> 대리자를 수신하도록 설정된 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-211">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="18b88-212"><xref:Microsoft.AspNetCore.Components.EventCallback>은 주변 디바이스의 `onclick` 이벤트에 적합한 `MouseEventArgs` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-212">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="18b88-213">`ParentComponent`는 자식의 <xref:Microsoft.AspNetCore.Components.EventCallback%601>(`OnClickCallback`)를 해당 `ShowMessage` 메서드에 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-213">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="18b88-214">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="18b88-214">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="18b88-215">`ChildComponent`에서 단추를 선택한 경우</span><span class="sxs-lookup"><span data-stu-id="18b88-215">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="18b88-216">`ParentComponent`의 `ShowMessage` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-216">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="18b88-217">`messageText`가 업데이트되고 `ParentComponent`에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-217">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="18b88-218">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) 호출은 콜백의 메서드(`ShowMessage`)에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-218">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="18b88-219">자식 이벤트가 자식 내에서 실행되는 이벤트 처리기에서 다시 렌더링되는 구성 요소를 트리거하는 것처럼 `ParentComponent`를 다시 렌더링하기 위해 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-219"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span> <span data-ttu-id="18b88-220">자세한 내용은 <xref:blazor/components/rendering>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="18b88-220">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="18b88-221"><xref:Microsoft.AspNetCore.Components.EventCallback> 및 <xref:Microsoft.AspNetCore.Components.EventCallback%601>는 비동기 대리자를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="18b88-222"><xref:Microsoft.AspNetCore.Components.EventCallback>은 약한 형식이며 `InvokeAsync(Object)`에서 모든 형식 인수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="18b88-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601>은 강력한 형식이며 `InvokeAsync(T)`에서 `TValue`에 할당할 수 있는 `T` 인수를 전달해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="18b88-224"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A>를 사용하여 <xref:Microsoft.AspNetCore.Components.EventCallback> 또는 <xref:Microsoft.AspNetCore.Components.EventCallback%601>를 호출하고 <xref:System.Threading.Tasks.Task>를 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="18b88-225">이벤트 처리 및 바인딩 구성 요소 매개 변수에 <xref:Microsoft.AspNetCore.Components.EventCallback> 및 <xref:Microsoft.AspNetCore.Components.EventCallback%601>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="18b88-226"><xref:Microsoft.AspNetCore.Components.EventCallback>보다 강력한 형식의 <xref:Microsoft.AspNetCore.Components.EventCallback%601>를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="18b88-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601>는 구성 요소 사용자에게 더 나은 오류 피드백을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="18b88-228">다른 UI 이벤트 처리기와 마찬가지로 이벤트 매개 변수 지정은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="18b88-229">콜백에 전달되는 값이 없는 경우 <xref:Microsoft.AspNetCore.Components.EventCallback>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="18b88-230">기본 작업 방지</span><span class="sxs-lookup"><span data-stu-id="18b88-230">Prevent default actions</span></span>

<span data-ttu-id="18b88-231">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 지시문 특성을 사용하여 이벤트의 기본 작업을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="18b88-232">입력 디바이스에서 키를 선택하고 요소 포커스가 텍스트 상자에 놓이면 일반적으로 브라우저의 텍스트 상자에 키 문자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="18b88-233">다음 예제에서는 `@onkeypress:preventDefault` 지시문 특성을 지정하여 기본 동작을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="18b88-234">카운터가 증가하고 **+** 키가 `<input>` 요소의 값에 캡처되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="18b88-235">값 없이 `@on{EVENT}:preventDefault` 특성을 지정하는 것은 `@on{EVENT}:preventDefault="true"`와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="18b88-236">특성 값으로 식을 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="18b88-237">다음 예제에서 `shouldPreventDefault`는 `true` 또는 `false`로 설정되는 `bool` 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="18b88-238">이벤트 전파 중지</span><span class="sxs-lookup"><span data-stu-id="18b88-238">Stop event propagation</span></span>

<span data-ttu-id="18b88-239">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 지시문 특성을 사용하여 이벤트 전파를 중지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="18b88-240">다음 예제에서 확인란을 선택하면 두 번째 자식 `<div>`의 클릭 이벤트가 부모 `<div>`로 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="focus-an-element"></a><span data-ttu-id="18b88-241">요소에 포커스</span><span class="sxs-lookup"><span data-stu-id="18b88-241">Focus an element</span></span>

<span data-ttu-id="18b88-242">[요소 참조](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)에서 `FocusAsync`를 호출하여 코드의 요소에 포커스를 둡니다.</span><span class="sxs-lookup"><span data-stu-id="18b88-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

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
