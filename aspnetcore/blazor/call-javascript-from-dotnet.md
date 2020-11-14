---
title: ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출
author: guardrex
description: 'Blazor 앱의 .NET 메서드에서 JavaScript 함수를 호출하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 5d9934c8bebbe994489380faf55140fce6beec95
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507800"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="df7e6-103">ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="df7e6-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="df7e6-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="df7e6-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="df7e6-105">Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="df7e6-106">이러한 시나리오를 *JavaScript 상호 운용성* ( *JS interop* )이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="df7e6-107">이 문서에서는 .NET에서 JavaScript 함수를 호출하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="df7e6-108">JavaScript에서 .NET 메서드를 호출하는 방법에 대한 자세한 내용은 <xref:blazor/call-dotnet-from-javascript>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="df7e6-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df7e6-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="df7e6-110">.NET에서 JavaScript를 호출하려면 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="df7e6-111">JS interop 호출을 실행하려면 구성 요소에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="df7e6-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>는 원하는 수의 JSON 직렬화 가능 인수와 함께 호출하려는 JavaScript 함수에 대한 식별자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="df7e6-113">함수 식별자는 전역 범위(`window`)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="df7e6-114">`window.someScope.someFunction`을 호출하려는 경우 식별자는 `someScope.someFunction`입니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="df7e6-115">호출되기 전에 함수를 등록할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="df7e6-116">반환 형식 `T` 또한 JSON 직렬화 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="df7e6-117">`T`는 반환되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="df7e6-118">[프라미스](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환하는 JavaScript 함수는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="df7e6-119">`InvokeAsync`는 프라미스의 래핑을 해제하고 프라미스가 대기한 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="df7e6-120">사전 렌더링을 사용하도록 설정한 Blazor Server 앱의 경우 초기 사전 렌더링 중에 JavaScript를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="df7e6-121">JavaScript interop 호출은 브라우저와의 연결이 설정될 때까지 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="df7e6-122">자세한 내용은 [Blazor Server 앱이 사전 렌더링 중인 경우 검색](#detect-when-a-blazor-server-app-is-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="df7e6-123">다음 예제는 JavaScript 기반 디코더인 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="df7e6-124">이 예제에서는 C# 메서드에서 개발자 코드의 요구 사항을 기존 JavaScript API로 오프로드하는 JavaScript 함수를 호출하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="df7e6-125">JavaScript 함수는 C# 메서드에서 바이트 배열을 수신하고, 배열을 디코딩하고, 표시를 위해 구성 요소에 텍스트를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="df7e6-126">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `TextDecoder`를 사용하여 전달된 배열을 디코딩하고 디코딩된 값을 반환하는 JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="df7e6-127">앞의 예제에 표시된 코드와 같은 JavaScript 코드는 스크립트 파일에 대한 참조를 사용하여 JavaScript 파일(`.js`)에서 로드할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="df7e6-128">다음 구성 요소는</span><span class="sxs-lookup"><span data-stu-id="df7e6-128">The following component:</span></span>

* <span data-ttu-id="df7e6-129">구성 요소 단추( **`Convert Array`** )가 선택된 경우 `JS`을 사용하여 `convertArray` JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-129">Invokes the `convertArray` JavaScript function using `JS` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="df7e6-130">JavaScript 함수를 호출한 후에는 전달된 배열이 문자열로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="df7e6-131">이 문자열은 표시를 위해 구성 요소로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="df7e6-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="df7e6-132">IJSRuntime</span></span>

<span data-ttu-id="df7e6-133"><xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="df7e6-134">Razor 구성 요소(`.razor`)에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="df7e6-135">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="df7e6-136">이 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 값을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="df7e6-137"><xref:Microsoft.JSInterop.IJSRuntime> 추상화를 클래스(`.cs`)에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="df7e6-138">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="df7e6-139">이 함수는 `JS.InvokeAsync`를 사용하여 호출되며 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="df7e6-140">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)를 사용하여 동적 콘텐츠를 생성하려면 `[Inject]` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="df7e6-141">이 항목과 함께 제공되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 수신하고 환영 메시지를 표시하기 위해 DOM과 상호 작용하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="df7e6-142">`showPrompt`: 사용자 입력(사용자 이름)을 수락하라는 메시지를 표시하고 호출자에게 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="df7e6-143">`displayWelcome`: 호출자의 환영 메시지를 `id`가 `welcome`인 DOM 개체에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="df7e6-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="df7e6-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="df7e6-145">JavaScript 파일을 참조하는 `<script>` 태그를 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="df7e6-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="df7e6-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="df7e6-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="df7e6-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="df7e6-148">`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="df7e6-149">.NET 메서드는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>을 호출하여 `exampleJsInterop.js` 파일의 JavaScript 함수와 상호 운용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="df7e6-150"><xref:Microsoft.JSInterop.IJSRuntime> 추상화는 Blazor Server 시나리오를 허용하기 위해 비동기적으로 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="df7e6-151">앱이 Blazor WebAssembly 앱이고 JavaScript 함수를 동기적으로 호출하려는 경우에는 <xref:Microsoft.JSInterop.IJSInProcessRuntime>으로 다운캐스트하고 대신 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="df7e6-152">대부분의 JS interop 라이브러리는 비동기 API를 사용하여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="df7e6-153">표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정하려면 [Blazor JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="df7e6-154">샘플 앱에는 JS interop를 시연하기 위한 구성 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="df7e6-155">이 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-155">The component:</span></span>

* <span data-ttu-id="df7e6-156">JavaScript 프롬프트를 통해 사용자 입력을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="df7e6-157">처리를 위해 구성 요소에 텍스트를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="df7e6-158">DOM과 상호 작용하여 환영 메시지를 표시하는 두 번째 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="df7e6-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="df7e6-159">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="df7e6-160">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="df7e6-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="df7e6-161">구성 요소의 **`Trigger JavaScript Prompt`** 단추를 선택하여 `TriggerJsPrompt`가 실행되면 `wwwroot/exampleJsInterop.js` 파일에 제공된 JavaScript `showPrompt` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="df7e6-162">`showPrompt` 함수는 HTML로 인코딩되고 구성 요소로 반환되는 사용자 입력(사용자 이름)을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="df7e6-163">이 구성 요소는 지역 변수, `name`에 사용자 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="df7e6-164">`name`에 저장된 문자열은 환영 메시지에 통합됩니다.그런 후 이 메시지는 JavaScript 함수, `displayWelcome`에 전달되고, 이 함수는 환영 메시지를 제목 태그로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="df7e6-165">Void JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="df7e6-165">Call a void JavaScript function</span></span>

<span data-ttu-id="df7e6-166">다음에 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="df7e6-167">[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)를 반환하는 JavaScript 함수</span><span class="sxs-lookup"><span data-stu-id="df7e6-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="df7e6-168">.NET에서 JavaScript 호출 결과를 읽을 필요가 없는 경우</span><span class="sxs-lookup"><span data-stu-id="df7e6-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="df7e6-169">Blazor Server 앱을 미리 렌더링 중인 경우 검색</span><span class="sxs-lookup"><span data-stu-id="df7e6-169">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="df7e6-170">요소에 대한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="df7e6-170">Capture references to elements</span></span>

<span data-ttu-id="df7e6-171">일부 JS interop 시나리오에는 HTML 요소에 대한 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="df7e6-172">예를 들어, UI 라이브러리에는 초기화를 위해 요소 참조가 필요하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 API를 호출해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="df7e6-173">다음 방법을 사용하여 구성 요소의 HTML 요소에 대한 참조를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="df7e6-174">HTML 요소에 `@ref` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="df7e6-175">해당 이름이 `@ref` 특성 값과 일치하는 <xref:Microsoft.AspNetCore.Components.ElementReference> 형식의 필드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="df7e6-176">다음 예에서는 `username` `<input>` 요소에 대한 참조를 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="df7e6-177">요소 참조만 사용하여 Blazor와 상호 작용하지 않는 빈 요소의 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="df7e6-178">이 시나리오는 타사 API가 요소에 콘텐츠를 제공하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="df7e6-179">Blazor는 요소와 상호 작용하지 않으므로 요소의 Blazor 표시와 DOM 간에 충돌이 발생할 가능성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-179">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="df7e6-180">다음 예제에서는 Blazor가 DOM과 상호 작용하여 이 요소의 목록 항목(`<li>`)을 채우기 때문에 순서가 지정되지 않은 목록(`ul`)의 콘텐츠를 변경하는 것은 *위험* 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="df7e6-181">JS interop이 요소 `MyList`의 내용을 변경하고 Blazor가 요소에 diff를 적용하려고 하면 diff는 DOM과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-181">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="df7e6-182"><xref:Microsoft.AspNetCore.Components.ElementReference>는 JS interop을 통해 JavaScript 코드로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="df7e6-183">JavaScript 코드는 일반적인 DOM API에서 사용할 수 있는 `HTMLElement` 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="df7e6-184">예를 들어 다음 코드는 요소에 마우스 클릭을 전송할 수 있도록 하는 .NET 확장 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="df7e6-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="df7e6-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="df7e6-186">Blazor 프레임워크에 기본 제공되고 요소 참조에서 작동하는 [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element)를 C# 코드에서 사용하여 요소에 포커스를 둡니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="df7e6-187">값을 반환하지 않는 JavaScript 함수를 호출하려면 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="df7e6-188">다음 코드는 캡처된 <xref:Microsoft.AspNetCore.Components.ElementReference>로 이전 JavaScript 함수를 호출하여 클라이언트 쪽 `Click` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="df7e6-189">확장 메서드를 사용하려면 <xref:Microsoft.JSInterop.IJSRuntime> 인스턴스를 수신하는 정적 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="df7e6-190">`clickElement` 메서드는 개체에서 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="df7e6-191">다음 예제에서는 `TriggerClickEvent` 메서드를 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="df7e6-192">`exampleButton` 변수는 구성 요소가 렌더링된 후에만 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="df7e6-193">JavaScript 코드에 채워지지 않은 <xref:Microsoft.AspNetCore.Components.ElementReference>가 전달되면 JavaScript 코드는 `null` 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="df7e6-194">구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 구성 요소 수명 주기 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="df7e6-195">제네릭 형식으로 작업하고 값을 반환하는 경우 <xref:System.Threading.Tasks.ValueTask%601>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="df7e6-196">`GenericMethod`는 형식이 있는 개체에 대해 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="df7e6-197">다음 예제에서는 `GenericMethod`가 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="df7e6-198">구성 요소 간 참조 요소</span><span class="sxs-lookup"><span data-stu-id="df7e6-198">Reference elements across components</span></span>

<span data-ttu-id="df7e6-199">다음과 같은 이유로 구성 요소 간에 <xref:Microsoft.AspNetCore.Components.ElementReference>를 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="df7e6-200">인스턴스는 구성 요소가 렌더링된 후 즉, 구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 메서드가 실행되는 동안이나 실행된 후에만 존재하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="df7e6-201"><xref:Microsoft.AspNetCore.Components.ElementReference>는 [`struct`](/csharp/language-reference/builtin-types/struct)이며, [구성 요소 매개 변수](xref:blazor/components/index#component-parameters)로 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="df7e6-202">부모 구성 요소는 다른 구성 요소에서 요소 참조를 사용할 수 있도록 하기 위해 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="df7e6-203">자식 구성 요소가 콜백을 등록할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="df7e6-204">전달된 요소 참조를 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 이벤트 중에 등록된 콜백을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="df7e6-205">간접적으로 이 방법을 사용하면 자식 구성 요소가 부모의 요소 참조와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="df7e6-206">다음 Blazor WebAssembly 예제에서 이 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-206">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="df7e6-207">`wwwroot/index.html`의 `<head>`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="df7e6-208">`wwwroot/index.html`의 `<body>`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="df7e6-209">`Pages/Index.razor`(부모 구성 요소):</span><span class="sxs-lookup"><span data-stu-id="df7e6-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="df7e6-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="df7e6-210">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="df7e6-211">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="df7e6-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="df7e6-212">`Shared/SurveyPrompt.razor`(자식 구성 요소):</span><span class="sxs-lookup"><span data-stu-id="df7e6-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="df7e6-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="df7e6-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="df7e6-214">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="df7e6-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="df7e6-215">JS interop 호출 강화</span><span class="sxs-lookup"><span data-stu-id="df7e6-215">Harden JS interop calls</span></span>

<span data-ttu-id="df7e6-216">JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="df7e6-217">기본적으로 Blazor Server 앱은 서버에서 1분 후에 JS interop 호출 시간을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-217">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="df7e6-218">앱에서 좀 더 적극적인 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용하여 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="df7e6-219">`Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="df7e6-220">구성 요소 코드의 호출마다 단일 호출은 다음과 같이 제한 시간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="df7e6-221">리소스 소모에 대한 자세한 내용은 <xref:blazor/security/server/threat-mitigation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="df7e6-222">순환 개체 참조 방지</span><span class="sxs-lookup"><span data-stu-id="df7e6-222">Avoid circular object references</span></span>

<span data-ttu-id="df7e6-223">순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="df7e6-224">.NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="df7e6-224">.NET method calls.</span></span>
* <span data-ttu-id="df7e6-225">반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="df7e6-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="df7e6-226">자세한 내용은 다음 문제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="df7e6-227">순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="df7e6-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="df7e6-228">제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="df7e6-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="df7e6-229">Blazor JavaScript 격리 및 개체 참조</span><span class="sxs-lookup"><span data-stu-id="df7e6-229">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="df7e6-230">Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-230">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="df7e6-231">JavaScript 격리는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="df7e6-232">가져온 JavaScript는 전역 네임스페이스를 더 이상 오염시키지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="df7e6-233">라이브러리 및 구성 요소의 소비자는 관련 JavaScript를 가져올 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="df7e6-234">예를 들어 다음 JavaScript 모듈은 브라우저 프롬프트를 표시하기 위해 JavaScript 함수를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="df7e6-235">위의 JavaScript 모듈을 .NET 라이브러리에 정적 웹 자산(`wwwroot/exampleJsInterop.js`)으로 추가한 다음, <xref:Microsoft.JSInterop.IJSRuntime> 서비스를 사용하여 .NET 코드로 모듈을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="df7e6-236">다음 예제에서는 서비스가 `js`(표시되지 않음)으로 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="df7e6-237">위 예제에서 `import` 식별자는 JavaScript 모듈을 가져오기 위해 특별히 사용되는 특수 식별자입니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="df7e6-238">안정적인 정적 웹 자산 경로 `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`를 사용하여 모듈을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="df7e6-239">`{LIBRARY NAME}` 자리 표시자는 라이브러리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="df7e6-240">`{PATH UNDER WWWROOT}` 자리 표시자는 `wwwroot` 아래의 스크립트에 대한 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="df7e6-241"><xref:Microsoft.JSInterop.IJSRuntime>은 모듈을 `IJSObjectReference`로 가져와 .NET 코드에서 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="df7e6-242">`IJSObjectReference`를 사용하여 모듈에서 내보낸 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="df7e6-243">`IJSInProcessObjectReference`는 함수를 동기적으로 호출할 수 있는 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="df7e6-244">`IJSUnmarshalledObjectReference`는 .NET 데이터를 직렬화하는 오버헤드 없이 함수를 호출할 수 있는 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-244">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="df7e6-245">이 참조는 성능이 중요한 경우 Blazor WebAssembly에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-245">This can be used in Blazor WebAssembly when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

<span data-ttu-id="df7e6-246">앞의 예제에서 <xref:Microsoft.JSInterop.IJSRuntime> 서비스는 클래스에 삽입되고 `js`에 할당됩니다(표시되지 않음).</span><span class="sxs-lookup"><span data-stu-id="df7e6-246">In the preceding example, the <xref:Microsoft.JSInterop.IJSRuntime> service is injected into the class and assigned to `js` (not shown).</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="df7e6-247">UI(DOM 요소)를 렌더링하는 JavaScript 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="df7e6-247">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="df7e6-248">브라우저 DOM 내에 표시되는 사용자 인터페이스 요소를 생성하는 JavaScript 라이브러리를 사용하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-248">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="df7e6-249">처음에는, Blazor의 diff 시스템은 DOM 요소의 트리를 제어해야 하는데 일부 외부 코드가 DOM 트리를 변경하고 diff 적용 메커니즘을 무효화하면 오류가 발생하기 때문에 이것이 어려워 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-249">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="df7e6-250">이는 Blazor 특정 제한 사항이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-250">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="df7e6-251">동일한 문제가 모든 diff 기반 UI 프레임워크에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-251">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="df7e6-252">다행히도 Blazor 구성 요소 UI 내에 외부에서 생성된 UI를 안정적으로 포함하는 것은 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-252">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="df7e6-253">구성 요소 코드(`.razor` 파일)가 빈 요소를 생성하도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-253">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="df7e6-254">Blazor의 diff 시스템이 관련되는 한 요소는 항상 비어 있으므로 렌더러는 요소로 재귀되지 않으며 대신 콘텐츠를 그대로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-254">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="df7e6-255">이렇게 하면 외부 관리형 임의 콘텐츠로 요소를 안전하게 채울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-255">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="df7e6-256">다음 예제에서는 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-256">The following example demonstrates the concept.</span></span> <span data-ttu-id="df7e6-257">`firstRender`가 `true`인 경우 `if` 문 내에서 `myElement`를 사용하여 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-257">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="df7e6-258">예를 들어 외부 JavaScript 라이브러리를 호출하여 요소를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-258">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="df7e6-259">Blazor는 해당 구성 요소 자체가 제거될 때까지 요소 콘텐츠를 그대로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-259">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="df7e6-260">구성 요소가 제거되면 구성 요소의 전체 DOM 하위 트리도 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-260">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

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

<span data-ttu-id="df7e6-261">더 자세한 예제로는 [오픈 소스 Mapbox API](https://www.mapbox.com/)를 사용하여 대화형 맵을 렌더링하는 다음 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-261">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="df7e6-262">`wwwroot/mapComponent.js`에 배치해야 하는 해당 JavaScript 모듈은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-262">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="df7e6-263">앞의 예제에서 `{ACCESS TOKEN}` 문자열을 https://account.mapbox.com 에서 가져올 수 있는 유효한 액세스 토큰으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-263">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="df7e6-264">올바른 스타일을 생성하려면 호스트 HTML 페이지(`index.html` 또는 `_Host.cshtml`)에 다음 스타일시트 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-264">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="df7e6-265">앞의 예제에서는 사용자가 다음을 수행하는 대화형 맵 UI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-265">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="df7e6-266">끌어서 스크롤하거나 확대/축소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-266">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="df7e6-267">단추를 클릭하여 미리 정의된 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-267">Click buttons to jump to predefined locations.</span></span>

![영국 브리스틀 및 일본 도쿄를 선택하는 단추가 포함된 일본 도쿄의 Mapbox 거리 지도](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="df7e6-269">이해해야 할 주요 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-269">The key points to understand are:</span></span>

 * <span data-ttu-id="df7e6-270">Blazor가 관련되는 한 `@ref="mapElement"`가 있는 `<div>`는 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-270">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="df7e6-271">따라서 `mapbox-gl.js`가 안전하게 요소를 채우고 시간이 지남에 따라 해당 콘텐츠를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-271">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="df7e6-272">UI를 렌더링하는 JavaScript 라이브러리와 함께 이 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-272">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="df7e6-273">페이지의 다른 부분에 연결하고 해당 부분을 수정하려고 시도하지 않는 한 Blazor 구성 요소 내부에 타사 JavaScript SPA 프레임워크의 구성 요소를 포함할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-273">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="df7e6-274">외부 JavaScript 코드는 Blazor가 비어 있는 것으로 간주하지 않는 요소를 안전하게 수정할 수 ‘없습니다’.</span><span class="sxs-lookup"><span data-stu-id="df7e6-274">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="df7e6-275">이 접근 방식을 사용하는 경우 Blazor가 DOM 요소를 유지하거나 제거하는 방법에 대한 규칙에 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-275">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="df7e6-276">앞의 예제에서 구성 요소는 단추 클릭 이벤트를 안전하게 처리하며, 기본적으로 가능한 경우 DOM 요소가 유지되므로 기존 맵 인스턴스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-276">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="df7e6-277">`@foreach` 루프 내부에서 맵 요소 목록을 렌더링한 경우 `@key`를 사용하여 구성 요소 인스턴스를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-277">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="df7e6-278">그렇지 않으면 목록 데이터의 변경으로 인해 안타깝게도 구성 요소 인스턴스가 이전 인스턴스의 상태를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-278">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="df7e6-279">자세한 내용은 [@key를 사용하여 요소 및 구성 요소 유지](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-279">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="df7e6-280">또한 앞의 예제에서는 ES6 모듈 내에서 JavaScript 논리 및 종속성을 캡슐화하고 `import` 식별자를 사용하여 동적으로 로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-280">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="df7e6-281">자세한 내용은 [JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-281">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="df7e6-282">JS Interop 호출의 크기 제한</span><span class="sxs-lookup"><span data-stu-id="df7e6-282">Size limits on JS interop calls</span></span>

<span data-ttu-id="df7e6-283">Blazor WebAssembly에서 프레임워크는 JS interop 호출의 입력 및 출력 크기에 제한을 두지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-283">In Blazor WebAssembly, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="df7e6-284">Blazor Server에서 JS Interop 호출 결과는 SignalR(<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>)에 의해 적용된 최대 페이로드 크기로 제한되며 기본값은 32KB입니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-284">In Blazor Server, the result of a JS interop call is limited by the maximum payload size enforced by SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="df7e6-285">페이로드가 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 보다 큰 JS interop 호출에 응답을 시도하는 애플리케이션은 오류를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-285">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="df7e6-286"><xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>를 수정하여 제한을 더 크게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-286">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="df7e6-287">다음 예는 최대 수신 메시지 크기를 64KB(64 \* 1024 \* 1024)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-287">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="df7e6-288">SignalR 제한을 늘리면 더 많은 서버 리소스를 사용해야 하며 이로 인해 서버가 악성 사용자에게 노출될 위험이 더 커집니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-288">Increasing the SignalR limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="df7e6-289">또한, 메모리에 대용량의 콘텐츠를 문자열 또는 바이트 배열로 읽어오면 할당이 가비지 수집기에서 제대로 작동하지 않아서 추가적인 성능 손실로 이어질 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-289">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="df7e6-290">대용량의 페이로드를 읽어오는 옵션 중 하나는 콘텐츠를 작은 덩어리로 전송하고 페이로드를 <xref:System.IO.Stream>으로 처리하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-290">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="df7e6-291">이 옵션은 큰 JSON 페이로드를 읽어오거나 데이터를 JavaScript에서 원시 바이트로 사용할 수 있는 경우에 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-291">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="df7e6-292">`InputFile` 구성 요소와 유사한 기법을 사용하는 Blazor Server에서 대용량 이진 페이로드 전송을 보여주는 사례는 [이진 제출 샘플 앱](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df7e6-292">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="df7e6-293">JavaScript와 Blazor 간에 대용량 데이터를 전송하는 코드를 개발하는 경우 다음 지침을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-293">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="df7e6-294">데이터를 작은 조각으로 분할하고, 서버가 모든 데이터를 받을 때까지 데이터 세그먼트를 순차적으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-294">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="df7e6-295">JavaScript 및 C# 코드에서 큰 개체를 할당하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-295">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="df7e6-296">데이터를 보내거나 받을 때 주 UI 스레드를 장기간 차단하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-296">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="df7e6-297">프로세스가 완료되거나 취소되면 사용된 메모리를 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-297">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="df7e6-298">보안을 위해 다음과 같은 추가 요구 사항을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-298">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="df7e6-299">전달할 수 있는 최대 파일 또는 데이터 크기를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-299">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="df7e6-300">클라이언트에서 서버로의 최소 업로드 속도를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-300">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="df7e6-301">서버가 데이터를 받은 후에 데이터를 다음과 같이 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-301">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="df7e6-302">모든 세그먼트가 수집될 때까지 메모리 버퍼에 임시로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-302">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="df7e6-303">즉시 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-303">Consumed immediately.</span></span> <span data-ttu-id="df7e6-304">예를 들어 각 세그먼트가 수신됨에 따라 데이터를 즉시 데이터베이스에 저장하거나 디스크에 쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df7e6-304">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df7e6-305">추가 자료</span><span class="sxs-lookup"><span data-stu-id="df7e6-305">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="df7e6-306">InteropComponent.razor 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="df7e6-306">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
