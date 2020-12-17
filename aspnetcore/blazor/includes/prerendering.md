Blazor 서버 앱을 미리 렌더링 중이면 브라우저에 연결되어 있지 않으므로 JavaScript 호출 등의 특정 작업을 수행할 수 없습니다. 미리 렌더링된 경우 구성 요소를 다르게 렌더링해야 할 수 있습니다.

브라우저와의 연결이 설정된 후로 JavaScript interop 호출을 지연하려면 [OnAfterRenderAsync 구성 요소 수명 주기 이벤트](xref:blazor/components/lifecycle#after-component-render)를 사용하면 됩니다. 이 이벤트는 앱이 완전히 렌더링되고 클라이언트 연결이 설정된 후에만 호출됩니다.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

앞에 나온 예제 코드에서, `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor 서버)의 `<head>` 요소 안에 `setElementText` JavaScript 함수를 제공합니다. 이 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 값을 반환하지 않습니다.

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> 앞에 나온 예제에서 DOM(문서 개체 모델)을 직접 수정한 것은 단지 데모용일 뿐입니다. JavaScript를 사용하여 DOM을 직접 수정할 경우 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있으므로 대부분 시나리오에서 권장되지 않습니다.

다음 구성 요소는 미리 렌더링과 호환되는 방식으로 구성 요소의 초기화 논리의 일부로서 JavaScript interop를 사용하는 방법을 보여 줍니다. 이 구성 요소는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 내부에서 렌더링 업데이트를 트리거하는 것이 가능함을 보여 줍니다. 개발자는 이 시나리오에서 무한 루프를 만들지 않도록 주의해야 합니다.

<xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType>가 호출될 경우, 구성 요소가 렌더링되기 전까지는 JavaScript 요소가 없으므로 `ElementRef`는 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>에서만 사용되고 그 전의 수명 주기 메서드에서는 사용되지 않습니다.

JavaScript interop 호출에서 얻은 새 상태로 구성 요소를 다시 렌더링하도록 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)가 호출됩니다. `StateHasChanged`는 `infoFromJs`가 `null`인 경우에만 호출되기 때문에 이 코드는 무한 루프를 만들지 않습니다.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

앞에 나온 예제 코드에서, `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor 서버)의 `<head>` 요소 안에 `setElementText` JavaScript 함수를 제공합니다. 이 함수는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 다음 값을 반환합니다.

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> 앞에 나온 예제에서 DOM(문서 개체 모델)을 직접 수정한 것은 단지 데모용일 뿐입니다. JavaScript를 사용하여 DOM을 직접 수정할 경우 JavaScript가 Blazor의 변경 내용 추적을 방해할 수 있으므로 대부분 시나리오에서 권장되지 않습니다.
