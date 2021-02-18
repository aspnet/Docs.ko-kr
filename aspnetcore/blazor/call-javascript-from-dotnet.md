---
title: ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출
author: guardrex
description: Blazor 앱의 .NET 메서드에서 JavaScript 함수를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5b28ad594567e7bfc87e15eed419bea520125654
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280303"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출

Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다. 이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.

이 문서에서는 .NET에서 JavaScript 함수를 호출하는 방법을 설명합니다. JavaScript에서 .NET 메서드를 호출하는 방법에 대한 자세한 내용은 <xref:blazor/call-dotnet-from-javascript>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

> [!NOTE]
> `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)의 닫는 `</body>` 태그 앞에 JS 파일(`<script>` 태그)을 추가합니다. JS interop 메서드가 있는 JS 파일은 Blazor 프레임워크 JS 파일 전에 포함되어야 합니다.

.NET에서 JavaScript를 호출하려면 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용합니다. JS interop 호출을 실행하려면 구성 요소에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>는 원하는 수의 JSON 직렬화 가능 인수와 함께 호출하려는 JavaScript 함수에 대한 식별자를 사용합니다. 함수 식별자는 전역 범위(`window`)를 기준으로 합니다. `window.someScope.someFunction`을 호출하려는 경우 식별자는 `someScope.someFunction`입니다. 호출되기 전에 함수를 등록할 필요는 없습니다. 반환 형식 `T` 또한 JSON 직렬화 가능해야 합니다. `T`는 반환되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치해야 합니다.

[프라미스](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환하는 JavaScript 함수는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>를 사용하여 호출됩니다. `InvokeAsync`는 프라미스의 래핑을 해제하고 프라미스가 대기한 값을 반환합니다.

사전 렌더링을 사용하도록 설정한 Blazor Server 앱의 경우 초기 사전 렌더링 중에 JavaScript를 호출할 수 없습니다. JavaScript interop 호출은 브라우저와의 연결이 설정될 때까지 지연됩니다. 자세한 내용은 [Blazor Server 앱이 사전 렌더링 중인 경우 검색](#detect-when-a-blazor-server-app-is-prerendering) 섹션을 참조하세요.

다음 예제는 JavaScript 기반 디코더인 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기준으로 합니다. 이 예제에서는 C# 메서드에서 개발자 코드의 요구 사항을 기존 JavaScript API로 오프로드하는 JavaScript 함수를 호출하는 방법을 보여 줍니다. JavaScript 함수는 C# 메서드에서 바이트 배열을 수신하고, 배열을 디코딩하고, 표시를 위해 구성 요소에 텍스트를 반환합니다.

`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `TextDecoder`를 사용하여 전달된 배열을 디코딩하고 디코딩된 값을 반환하는 JavaScript 함수를 제공합니다.

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

앞의 예제에 표시된 코드와 같은 JavaScript 코드는 스크립트 파일에 대한 참조를 사용하여 JavaScript 파일(`.js`)에서 로드할 수도 있습니다.

```html
<script src="exampleJsInterop.js"></script>
```

다음 구성 요소는

* 구성 요소 단추( **`Convert Array`** )가 선택된 경우 `JS`을 사용하여 `convertArray` JavaScript 함수를 호출합니다.
* JavaScript 함수를 호출한 후에는 전달된 배열이 문자열로 변환됩니다. 이 문자열은 표시를 위해 구성 요소로 반환됩니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

<xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용하려면 다음 방법 중 하나를 채택합니다.

* Razor 구성 요소(`.razor`)에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다. 이 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 값을 반환하지 않습니다.

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 클래스(`.cs`)에 주입합니다.

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다. 이 함수는 `JS.InvokeAsync`를 사용하여 호출되며 값을 반환합니다.

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)를 사용하여 동적 콘텐츠를 생성하려면 `[Inject]` 특성을 사용합니다.

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

이 항목과 함께 제공되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 수신하고 환영 메시지를 표시하기 위해 DOM과 상호 작용하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.

* `showPrompt`: 사용자 입력(사용자 이름)을 수락하라는 메시지를 표시하고 호출자에게 이름을 반환합니다.
* `displayWelcome`: 호출자의 환영 메시지를 `id`가 `welcome`인 DOM 개체에 할당합니다.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript 파일을 참조하는 `<script>` 태그를 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 배치합니다.

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=23)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.

.NET 메서드는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>을 호출하여 `exampleJsInterop.js` 파일의 JavaScript 함수와 상호 운용합니다.

<xref:Microsoft.JSInterop.IJSRuntime> 추상화는 Blazor Server 시나리오를 허용하기 위해 비동기적으로 진행됩니다. 앱이 Blazor WebAssembly 앱이고 JavaScript 함수를 동기적으로 호출하려는 경우에는 <xref:Microsoft.JSInterop.IJSInProcessRuntime>으로 다운캐스트하고 대신 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>를 호출합니다. 대부분의 JS interop 라이브러리는 비동기 API를 사용하여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정하려면 [Blazor JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references) 섹션을 참조하세요.

::: moniker-end

샘플 앱에는 JS interop를 시연하기 위한 구성 요소가 포함되어 있습니다. 이 구성 요소는 다음을 수행합니다.

* JavaScript 프롬프트를 통해 사용자 입력을 받습니다.
* 처리를 위해 구성 요소에 텍스트를 반환합니다.
* DOM과 상호 작용하여 환영 메시지를 표시하는 두 번째 JavaScript 함수를 호출합니다.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

1. 구성 요소의 **`Trigger JavaScript Prompt`** 단추를 선택하여 `TriggerJsPrompt`가 실행되면 `wwwroot/exampleJsInterop.js` 파일에 제공된 JavaScript `showPrompt` 함수가 호출됩니다.
1. `showPrompt` 함수는 HTML로 인코딩되고 구성 요소로 반환되는 사용자 입력(사용자 이름)을 수락합니다. 이 구성 요소는 지역 변수, `name`에 사용자 이름을 저장합니다.
1. `name`에 저장된 문자열은 환영 메시지에 통합됩니다.그런 후 이 메시지는 JavaScript 함수, `displayWelcome`에 전달되고, 이 함수는 환영 메시지를 제목 태그로 렌더링합니다.

## <a name="call-a-void-javascript-function"></a>Void JavaScript 함수 호출

다음에 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>을 사용합니다.

* [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)를 반환하는 JavaScript 함수
* .NET에서 JavaScript 호출 결과를 읽을 필요가 없는 경우

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Blazor Server 앱을 미리 렌더링 중인 경우 검색
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a>요소에 대한 참조 캡처

일부 JS interop 시나리오에는 HTML 요소에 대한 참조가 필요합니다. 예를 들어, UI 라이브러리에는 초기화를 위해 요소 참조가 필요하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 API를 호출해야 할 수 있습니다.

다음 방법을 사용하여 구성 요소의 HTML 요소에 대한 참조를 캡처합니다.

* HTML 요소에 `@ref` 특성을 추가합니다.
* 해당 이름이 `@ref` 특성 값과 일치하는 <xref:Microsoft.AspNetCore.Components.ElementReference> 형식의 필드를 정의합니다.

다음 예에서는 `username` `<input>` 요소에 대한 참조를 캡처하는 방법을 보여 줍니다.

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> 요소 참조만 사용하여 Blazor와 상호 작용하지 않는 빈 요소의 내용을 변경합니다. 이 시나리오는 타사 API가 요소에 콘텐츠를 제공하는 경우에 유용합니다. Blazor는 요소와 상호 작용하지 않으므로 요소의 Blazor 표시와 DOM 간에 충돌이 발생할 가능성이 없습니다.
>
> 다음 예제에서는 Blazor가 DOM과 상호 작용하여 이 요소의 목록 항목(`<li>`)을 채우기 때문에 순서가 지정되지 않은 목록(`ul`)의 콘텐츠를 변경하는 것은 *위험* 합니다.
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> JS interop이 요소 `MyList`의 내용을 변경하고 Blazor가 요소에 diff를 적용하려고 하면 diff는 DOM과 일치하지 않습니다.

<xref:Microsoft.AspNetCore.Components.ElementReference>는 JS interop을 통해 JavaScript 코드로 전달됩니다. JavaScript 코드는 일반적인 DOM API에서 사용할 수 있는 `HTMLElement` 인스턴스를 수신합니다. 예를 들어 다음 코드는 요소에 마우스 클릭을 전송할 수 있도록 하는 .NET 확장 메서드를 정의합니다.

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Blazor 프레임워크에 기본 제공되고 요소 참조에서 작동하는 [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element)를 C# 코드에서 사용하여 요소에 포커스를 둡니다.

::: moniker-end

값을 반환하지 않는 JavaScript 함수를 호출하려면 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용합니다. 다음 코드는 캡처된 <xref:Microsoft.AspNetCore.Components.ElementReference>로 이전 JavaScript 함수를 호출하여 클라이언트 쪽 `Click` 이벤트를 트리거합니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

확장 메서드를 사용하려면 <xref:Microsoft.JSInterop.IJSRuntime> 인스턴스를 수신하는 정적 확장 메서드를 만듭니다.

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

`clickElement` 메서드는 개체에서 직접 호출됩니다. 다음 예제에서는 `TriggerClickEvent` 메서드를 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> `exampleButton` 변수는 구성 요소가 렌더링된 후에만 채워집니다. JavaScript 코드에 채워지지 않은 <xref:Microsoft.AspNetCore.Components.ElementReference>가 전달되면 JavaScript 코드는 `null` 값을 받습니다. 구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 구성 요소 수명 주기 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.

제네릭 형식으로 작업하고 값을 반환하는 경우 <xref:System.Threading.Tasks.ValueTask%601>를 사용합니다.

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`는 형식이 있는 개체에 대해 직접 호출됩니다. 다음 예제에서는 `GenericMethod`가 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>구성 요소 간 참조 요소

다음과 같은 이유로 구성 요소 간에 <xref:Microsoft.AspNetCore.Components.ElementReference>를 전달할 수 없습니다.

* 인스턴스는 구성 요소가 렌더링된 후 즉, 구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 메서드가 실행되는 동안이나 실행된 후에만 존재하도록 보장됩니다.
* <xref:Microsoft.AspNetCore.Components.ElementReference>는 [`struct`](/csharp/language-reference/builtin-types/struct)이며, [구성 요소 매개 변수](xref:blazor/components/index#component-parameters)로 전달할 수 없습니다.

부모 구성 요소는 다른 구성 요소에서 요소 참조를 사용할 수 있도록 하기 위해 다음을 수행할 수 있습니다.

* 자식 구성 요소가 콜백을 등록할 수 있도록 허용합니다.
* 전달된 요소 참조를 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 이벤트 중에 등록된 콜백을 호출합니다. 간접적으로 이 방법을 사용하면 자식 구성 요소가 부모의 요소 참조와 상호 작용할 수 있습니다.

다음 Blazor WebAssembly 예제에서 이 방법을 보여 줍니다.

`wwwroot/index.html`의 `<head>`에서 다음을 수행합니다.

```html
<style>
    .red { color: red }
</style>
```

`wwwroot/index.html`의 `<body>`에서 다음을 수행합니다.

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor`(부모 구성 요소):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

`Shared/SurveyPrompt.razor`(자식 구성 요소):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

## <a name="harden-js-interop-calls"></a>JS interop 호출 강화

JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리되어야 합니다. 기본적으로 Blazor Server 앱은 서버에서 1분 후에 JS interop 호출 시간을 제한합니다. 앱에서 좀 더 적극적인 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용하여 시간 제한을 설정합니다.

* `Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정합니다.

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 구성 요소 코드의 호출마다 단일 호출은 다음과 같이 제한 시간을 지정할 수 있습니다.

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

리소스 소모에 대한 자세한 내용은 <xref:blazor/security/server/threat-mitigation>를 참조하세요.

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>순환 개체 참조 방지

순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.

* .NET 메서드 호출
* 반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출

자세한 내용은 [순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)을 참조하세요.

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 격리 및 개체 참조

Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다. JavaScript 격리는 다음과 같은 이점을 제공합니다.

* 가져온 JavaScript는 전역 네임스페이스를 더 이상 오염시키지 않습니다.
* 라이브러리 및 구성 요소의 소비자는 관련 JavaScript를 가져올 필요가 없습니다.

예를 들어 다음 JavaScript 모듈은 브라우저 프롬프트를 표시하기 위해 JavaScript 함수를 내보냅니다.

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

위의 JavaScript 모듈을 .NET 라이브러리에 정적 웹 자산(`wwwroot/exampleJsInterop.js`)으로 추가한 다음, <xref:Microsoft.JSInterop.IJSRuntime> 서비스에서 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>를 호출하여 .NET 코드로 모듈을 가져옵니다. 다음 예제에서는 서비스가 `js`(표시되지 않음)으로 삽입됩니다.

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

위 예제에서 `import` 식별자는 JavaScript 모듈을 가져오기 위해 특별히 사용되는 특수 식별자입니다. 안정적인 정적 웹 자산 경로 `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`를 사용하여 모듈을 지정합니다. JavaScript 파일의 올바른 정적 자산 경로를 만들려면 현재 디렉터리의 패스 세그먼트(`./`)가 필요합니다. 모듈을 동적으로 가져오려면 네트워크 요청이 필요하므로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>를 호출하여 비동기적으로 수행해야 합니다. `{LIBRARY NAME}` 자리 표시자는 라이브러리 이름입니다. `{PATH UNDER WWWROOT}` 자리 표시자는 `wwwroot` 아래의 스크립트에 대한 경로입니다.

<xref:Microsoft.JSInterop.IJSRuntime>은 모듈을 `IJSObjectReference`로 가져와 .NET 코드에서 JavaScript 개체에 대한 참조를 나타냅니다. `IJSObjectReference`를 사용하여 모듈에서 내보낸 JavaScript 함수를 호출합니다.

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference`는 함수를 동기적으로 호출할 수 있는 JavaScript 개체에 대한 참조를 나타냅니다.

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>UI(DOM 요소)를 렌더링하는 JavaScript 라이브러리 사용

브라우저 DOM 내에 표시되는 사용자 인터페이스 요소를 생성하는 JavaScript 라이브러리를 사용하려는 경우가 있습니다. 처음에는, Blazor의 diff 시스템은 DOM 요소의 트리를 제어해야 하는데 일부 외부 코드가 DOM 트리를 변경하고 diff 적용 메커니즘을 무효화하면 오류가 발생하기 때문에 이것이 어려워 보일 수 있습니다. 이는 Blazor 특정 제한 사항이 아닙니다. 동일한 문제가 모든 diff 기반 UI 프레임워크에서 발생합니다.

다행히도 Blazor 구성 요소 UI 내에 외부에서 생성된 UI를 안정적으로 포함하는 것은 간단합니다. 구성 요소 코드(`.razor` 파일)가 빈 요소를 생성하도록 하는 것이 좋습니다. Blazor의 diff 시스템이 관련되는 한 요소는 항상 비어 있으므로 렌더러는 요소로 재귀되지 않으며 대신 콘텐츠를 그대로 둡니다. 이렇게 하면 외부 관리형 임의 콘텐츠로 요소를 안전하게 채울 수 있습니다.

다음 예제에서는 개념을 보여 줍니다. `firstRender`가 `true`인 경우 `if` 문 내에서 `myElement`를 사용하여 작업을 수행합니다. 예를 들어 외부 JavaScript 라이브러리를 호출하여 요소를 채웁니다. Blazor는 해당 구성 요소 자체가 제거될 때까지 요소 콘텐츠를 그대로 둡니다. 구성 요소가 제거되면 구성 요소의 전체 DOM 하위 트리도 제거됩니다.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

더 자세한 예제로는 [오픈 소스 Mapbox API](https://www.mapbox.com/)를 사용하여 대화형 맵을 렌더링하는 다음 구성 요소를 고려합니다.

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

`wwwroot/mapComponent.js`에 배치해야 하는 해당 JavaScript 모듈은 다음과 같습니다.

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

앞의 예제에서 `{ACCESS TOKEN}` 문자열을 https://account.mapbox.com 에서 가져올 수 있는 유효한 액세스 토큰으로 바꿉니다.

올바른 스타일을 생성하려면 호스트 HTML 페이지(`index.html` 또는 `_Host.cshtml`)에 다음 스타일시트 태그를 추가합니다.

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

앞의 예제에서는 사용자가 다음을 수행하는 대화형 맵 UI를 생성합니다.

* 끌어서 스크롤하거나 확대/축소할 수 있습니다.
* 단추를 클릭하여 미리 정의된 위치로 이동합니다.

![영국 브리스틀 및 일본 도쿄를 선택하는 단추가 포함된 일본 도쿄의 Mapbox 거리 지도](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

이해해야 할 주요 사항은 다음과 같습니다.

 * Blazor가 관련되는 한 `@ref="mapElement"`가 있는 `<div>`는 비어 있습니다. 따라서 `mapbox-gl.js`가 안전하게 요소를 채우고 시간이 지남에 따라 해당 콘텐츠를 수정할 수 있습니다. UI를 렌더링하는 JavaScript 라이브러리와 함께 이 방법을 사용할 수 있습니다. 페이지의 다른 부분에 연결하고 해당 부분을 수정하려고 시도하지 않는 한 Blazor 구성 요소 내부에 타사 JavaScript SPA 프레임워크의 구성 요소를 포함할 수도 있습니다. 외부 JavaScript 코드는 Blazor가 비어 있는 것으로 간주하지 않는 요소를 안전하게 수정할 수 ‘없습니다’.
 * 이 접근 방식을 사용하는 경우 Blazor가 DOM 요소를 유지하거나 제거하는 방법에 대한 규칙에 고려해야 합니다. 앞의 예제에서 구성 요소는 단추 클릭 이벤트를 안전하게 처리하며, 기본적으로 가능한 경우 DOM 요소가 유지되므로 기존 맵 인스턴스를 업데이트합니다. `@foreach` 루프 내부에서 맵 요소 목록을 렌더링한 경우 `@key`를 사용하여 구성 요소 인스턴스를 유지할 수 있습니다. 그렇지 않으면 목록 데이터의 변경으로 인해 안타깝게도 구성 요소 인스턴스가 이전 인스턴스의 상태를 유지할 수 있습니다. 자세한 내용은 [@key를 사용하여 요소 및 구성 요소 유지](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)를 참조하세요.

또한 앞의 예제에서는 ES6 모듈 내에서 JavaScript 논리 및 종속성을 캡슐화하고 `import` 식별자를 사용하여 동적으로 로드하는 방법을 보여 줍니다. 자세한 내용은 [JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references)를 참조하세요.

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>JS Interop 호출의 크기 제한

Blazor WebAssembly에서 프레임워크는 JS interop 입력 및 출력 크기에 제한을 두지 않습니다.

Blazor Server에서 JS interop 호출의 크기는 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>에 의해 적용되는 허브 메서드에 허용되는 최대 수신 SignalR 메시지 크기로 제한됩니다(기본값: 32KB). .NET SignalR 메시지에 대한 JS가 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>보다 크면 오류가 throw됩니다. 프레임워크는 허브에서 클라이언트로 전송되는 SignalR 메시지의 크기에 제한을 적용하지 않습니다. 자세한 내용은 <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>를 참조하세요.
  
## <a name="js-modules"></a>JS 모듈

JS 격리의 경우 JS interop은 [ESM(EcmaScript 모듈)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)([ECMAScript 사양](https://tc39.es/ecma262/#sec-modules))에 대한 브라우저의 기본 지원과 함께 작동합니다.

## <a name="unmarshalled-js-interop"></a>역 마샬링된 JS interop

.NET 개체가 JS interop에 대해 직렬화되고 다음 중 하나가 참인 경우 Blazor WebAssembly 구성 요소의 성능이 저하될 수 있습니다.

* 많은 양의 .NET 개체가 빠르게 직렬화됩니다. 예: JS interop 호출이 마우스 휠 회전과 같은 입력 디바이스의 이동을 기반으로 이루어집니다.
* 많은 양의 .NET 개체 또는 여러 .NET 개체가 JS interop에 대해 직렬화되어야 합니다. 예: JS interop 호출에 수십 개의 파일의 직렬화가 필요합니다.

<xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference>는 .NET 데이터를 직렬화하는 오버헤드 없이 함수를 호출할 수 있는 JavaScript 개체에 대한 참조를 나타냅니다.

다음 예제에서는

* 문자열과 정수를 포함하는 [구조체](/dotnet/csharp/language-reference/builtin-types/struct)가 직렬화되지 않은 상태로 JavaScript로 전달됩니다.
* JavaScript 함수가 데이터를 처리하고 부울 또는 문자열을 호출자에게 반환합니다.
* JavaScript 문자열은 .NET `string` 개체로 직접 변환할 수 없습니다. `unmarshalledFunctionReturnString` 함수가 Javascript 문자열의 변환을 관리하기 위해 `BINDING.js_string_to_mono_string`을 호출합니다.

> [!NOTE]
> JavaScript로 전달된 [구조체](/dotnet/csharp/language-reference/builtin-types/struct)의 결과로 성능이 저하되지 않기 때문에 다음 예제는 이 시나리오의 일반적인 사용 사례가 아닙니다. 예제에서는 직렬화되지 않은 .NET 데이터의 전달이라는 개념을 보여 주기 위한 용도로 작은 개체를 사용합니다.

`wwwroot/index.html` 또는 `wwwroot/index.html`에서 참조하는 외부 Javascript의 `<script>` 블록 내용:

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> `js_string_to_mono_string` 함수 이름, 동작 및 존재는 향후 .NET 릴리스에서 변경될 수 있습니다. 예를 들면 다음과 같습니다.
>
> * 함수의 이름이 바뀔 수 있습니다.
> * 함수가 제거되고 프레임워크에 의한 자동 문자열 변환 기능이 사용될 수 있습니다.

`Pages/UnmarshalledJSInterop.razor`(URL: `/unmarshalled-js-interop`):

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

`IJSUnmarshalledObjectReference` 인스턴스가 C# 코드에서 삭제되지 않을 경우 JavaScript에서 삭제될 수 있습니다. 다음 `dispose` 함수는 JavaScript에서 호출된 경우 개체 참조를 삭제합니다.

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

배열 형식이 `js_typed_array_to_array`를 사용하여 JavaScript 개체에서 .NET으로 변환될 수 있지만 JavaScript 배열은 형식화된 배열이어야 합니다. JavaScript의 배열은 C# 코드에서 .NET 개체 배열(`object[]`)로 읽힐 수 있습니다.

문자열 배열과 같은 기타 데이터 형식은 변환될 수 있지만 이를 위해서는 새 Mono 배열 개체(`mono_obj_array_new`)를 만들고 값을 설정(`mono_obj_array_set`)해야 합니다.

> [!WARNING]
> Blazor 프레임워크에서 제공하는 JavaScript 함수(예: `js_typed_array_to_array`, `mono_obj_array_new`, `mono_obj_array_set`)는 .NET의 향후 릴리스에서 이름이 바뀌거나 동작이 변경되거나 제거될 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
