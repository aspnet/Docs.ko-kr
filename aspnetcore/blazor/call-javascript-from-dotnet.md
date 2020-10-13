---
title: ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출
author: guardrex
description: Blazor 앱의 .NET 메서드에서 JavaScript 함수를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
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
ms.openlocfilehash: d36140067ba6e75f2d00cb86ea488e40d28bd86f
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762167"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="999b7-103">ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="999b7-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="999b7-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="999b7-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="999b7-105">Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="999b7-106">이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="999b7-107">이 문서에서는 .NET에서 JavaScript 함수를 호출하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="999b7-108">JavaScript에서 .NET 메서드를 호출하는 방법에 대한 자세한 내용은 <xref:blazor/call-dotnet-from-javascript>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="999b7-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="999b7-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="999b7-110">.NET에서 JavaScript를 호출하려면 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="999b7-111">JS interop 호출을 실행하려면 구성 요소에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="999b7-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>는 원하는 수의 JSON 직렬화 가능 인수와 함께 호출하려는 JavaScript 함수에 대한 식별자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="999b7-113">함수 식별자는 전역 범위(`window`)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="999b7-114">`window.someScope.someFunction`을 호출하려는 경우 식별자는 `someScope.someFunction`입니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="999b7-115">호출되기 전에 함수를 등록할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="999b7-116">반환 형식 `T` 또한 JSON 직렬화 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="999b7-117">`T`는 반환되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="999b7-118">[프라미스](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 반환하는 JavaScript 함수는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="999b7-119">`InvokeAsync`는 프라미스의 래핑을 해제하고 프라미스가 대기한 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="999b7-120">사전 렌더링을 사용하도록 설정한 Blazor Server 앱의 경우 초기 사전 렌더링 중에 JavaScript를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="999b7-121">JavaScript interop 호출은 브라우저와의 연결이 설정될 때까지 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="999b7-122">자세한 내용은 [Blazor Server 앱이 사전 렌더링 중인 경우 검색](#detect-when-a-blazor-server-app-is-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="999b7-123">다음 예제는 JavaScript 기반 디코더인 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="999b7-124">이 예제에서는 C# 메서드에서 개발자 코드의 요구 사항을 기존 JavaScript API로 오프로드하는 JavaScript 함수를 호출하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="999b7-125">JavaScript 함수는 C# 메서드에서 바이트 배열을 수신하고, 배열을 디코딩하고, 표시를 위해 구성 요소에 텍스트를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="999b7-126">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `TextDecoder`를 사용하여 전달된 배열을 디코딩하고 디코딩된 값을 반환하는 JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="999b7-127">앞의 예제에 표시된 코드와 같은 JavaScript 코드는 스크립트 파일에 대한 참조를 사용하여 JavaScript 파일(`.js`)에서 로드할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="999b7-128">다음 구성 요소는</span><span class="sxs-lookup"><span data-stu-id="999b7-128">The following component:</span></span>

* <span data-ttu-id="999b7-129">구성 요소 단추( **`Convert Array`** )가 선택된 경우 `JSRuntime`을 사용하여 `convertArray` JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="999b7-130">JavaScript 함수를 호출한 후에는 전달된 배열이 문자열로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="999b7-131">이 문자열은 표시를 위해 구성 요소로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="999b7-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="999b7-132">IJSRuntime</span></span>

<span data-ttu-id="999b7-133"><xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="999b7-134">Razor 구성 요소(`.razor`)에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="999b7-135">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="999b7-136">이 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 값을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="999b7-137"><xref:Microsoft.JSInterop.IJSRuntime> 추상화를 클래스(`.cs`)에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="999b7-138">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="999b7-139">이 함수는 `JSRuntime.InvokeAsync`를 사용하여 호출되며 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="999b7-140">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)를 사용하여 동적 콘텐츠를 생성하려면 `[Inject]` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="999b7-141">이 항목과 함께 제공되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 수신하고 환영 메시지를 표시하기 위해 DOM과 상호 작용하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="999b7-142">`showPrompt`: 사용자 입력(사용자 이름)을 수락하라는 메시지를 표시하고 호출자에게 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="999b7-143">`displayWelcome`: 호출자의 환영 메시지를 `id`가 `welcome`인 DOM 개체에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="999b7-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="999b7-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="999b7-145">JavaScript 파일을 참조하는 `<script>` 태그를 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="999b7-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="999b7-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="999b7-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="999b7-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="999b7-148">`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="999b7-149">.NET 메서드는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>을 호출하여 `exampleJsInterop.js` 파일의 JavaScript 함수와 상호 운용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="999b7-150"><xref:Microsoft.JSInterop.IJSRuntime> 추상화는 Blazor Server 시나리오를 허용하기 위해 비동기적으로 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="999b7-151">앱이 Blazor WebAssembly 앱이고 JavaScript 함수를 동기적으로 호출하려는 경우에는 <xref:Microsoft.JSInterop.IJSInProcessRuntime>으로 다운캐스트하고 대신 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="999b7-152">대부분의 JS interop 라이브러리는 비동기 API를 사용하여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="999b7-153">표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정하려면 [Blazor JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="999b7-154">샘플 앱에는 JS interop를 시연하기 위한 구성 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="999b7-155">이 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-155">The component:</span></span>

* <span data-ttu-id="999b7-156">JavaScript 프롬프트를 통해 사용자 입력을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="999b7-157">처리를 위해 구성 요소에 텍스트를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="999b7-158">DOM과 상호 작용하여 환영 메시지를 표시하는 두 번째 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="999b7-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="999b7-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="999b7-160">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="999b7-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="999b7-161">구성 요소의 **`Trigger JavaScript Prompt`** 단추를 선택하여 `TriggerJsPrompt`가 실행되면 `wwwroot/exampleJsInterop.js` 파일에 제공된 JavaScript `showPrompt` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="999b7-162">`showPrompt` 함수는 HTML로 인코딩되고 구성 요소로 반환되는 사용자 입력(사용자 이름)을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="999b7-163">이 구성 요소는 지역 변수, `name`에 사용자 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="999b7-164">`name`에 저장된 문자열은 환영 메시지에 통합됩니다.그런 후 이 메시지는 JavaScript 함수, `displayWelcome`에 전달되고, 이 함수는 환영 메시지를 제목 태그로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="999b7-165">Void JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="999b7-165">Call a void JavaScript function</span></span>

<span data-ttu-id="999b7-166">[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)를 반환하는 JavaScript 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-166">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="999b7-167">Blazor Server 앱을 미리 렌더링 중인 경우 검색</span><span class="sxs-lookup"><span data-stu-id="999b7-167">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="999b7-168">요소에 대한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="999b7-168">Capture references to elements</span></span>

<span data-ttu-id="999b7-169">일부 JS interop 시나리오에는 HTML 요소에 대한 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-169">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="999b7-170">예를 들어, UI 라이브러리에는 초기화를 위해 요소 참조가 필요하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 API를 호출해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-170">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="999b7-171">다음 방법을 사용하여 구성 요소의 HTML 요소에 대한 참조를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-171">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="999b7-172">HTML 요소에 `@ref` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-172">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="999b7-173">해당 이름이 `@ref` 특성 값과 일치하는 <xref:Microsoft.AspNetCore.Components.ElementReference> 형식의 필드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-173">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="999b7-174">다음 예에서는 `username` `<input>` 요소에 대한 참조를 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-174">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="999b7-175">요소 참조만 사용하여 Blazor와 상호 작용하지 않는 빈 요소의 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-175">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="999b7-176">이 시나리오는 타사 API가 요소에 콘텐츠를 제공하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-176">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="999b7-177">Blazor는 요소와 상호 작용하지 않으므로 요소의 Blazor 표시와 DOM 간에 충돌이 발생할 가능성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-177">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="999b7-178">다음 예제에서는 Blazor가 DOM과 상호 작용하여 이 요소의 목록 항목(`<li>`)을 채우기 때문에 순서가 지정되지 않은 목록(`ul`)의 콘텐츠를 변경하는 것은 *위험*합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-178">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="999b7-179">JS interop이 요소 `MyList`의 내용을 변경하고 Blazor가 요소에 diff를 적용하려고 하면 diff는 DOM과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-179">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="999b7-180">.NET 코드와 관련하여 <xref:Microsoft.AspNetCore.Components.ElementReference>는 불투명 핸들입니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-180">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="999b7-181"><xref:Microsoft.AspNetCore.Components.ElementReference>으로 수행할 수 있는 *유일한* 작업이 JS interop을 통해 JavaScript 코드에 전달하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-181">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="999b7-182">이렇게 하면 JavaScript 쪽 코드는 일반적인 DOM API에서 사용할 수 있는 `HTMLElement` 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-182">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="999b7-183">예를 들어, 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-183">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="999b7-184">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="999b7-184">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="999b7-185">값을 반환하지 않는 JavaScript 함수를 호출하려면 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-185">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="999b7-186">다음 코드는 캡처된 <xref:Microsoft.AspNetCore.Components.ElementReference>로 이전 JavaScript 함수를 호출하여 사용자 이름 입력에 포커스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-186">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="999b7-187">확장 메서드를 사용하려면 <xref:Microsoft.JSInterop.IJSRuntime> 인스턴스를 수신하는 정적 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-187">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="999b7-188">`Focus` 메서드는 개체에서 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-188">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="999b7-189">다음 예제에서는 `Focus` 메서드를 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-189">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="999b7-190">`username` 변수는 구성 요소가 렌더링된 후에만 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-190">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="999b7-191">JavaScript 코드에 채워지지 않은 <xref:Microsoft.AspNetCore.Components.ElementReference>가 전달되면 JavaScript 코드는 `null` 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-191">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="999b7-192">구성 요소에서 렌더링을 완료한 후 요소에 초기 포커스를 설정하기 위해 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 구성 요소 수명 주기 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-192">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="999b7-193">제네릭 형식으로 작업하고 값을 반환하는 경우 <xref:System.Threading.Tasks.ValueTask%601>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-193">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="999b7-194">`GenericMethod`는 형식이 있는 개체에 대해 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-194">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="999b7-195">다음 예제에서는 `GenericMethod`가 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-195">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="999b7-196">구성 요소 간 참조 요소</span><span class="sxs-lookup"><span data-stu-id="999b7-196">Reference elements across components</span></span>

<span data-ttu-id="999b7-197"><xref:Microsoft.AspNetCore.Components.ElementReference>는 구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 메서드에서만 유효한 것으로 보장되며 요소 참조는 `struct`이므로 구성 요소 간에 요소 참조를 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-197">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="999b7-198">부모 구성 요소는 다른 구성 요소에서 요소 참조를 사용할 수 있도록 하기 위해 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-198">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="999b7-199">자식 구성 요소가 콜백을 등록할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-199">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="999b7-200">전달된 요소 참조를 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 이벤트 중에 등록된 콜백을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-200">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="999b7-201">간접적으로 이 방법을 사용하면 자식 구성 요소가 부모의 요소 참조와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-201">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="999b7-202">다음 Blazor WebAssembly 예제에서 이 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-202">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="999b7-203">`wwwroot/index.html`의 `<head>`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-203">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="999b7-204">`wwwroot/index.html`의 `<body>`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-204">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="999b7-205">`Pages/Index.razor`(부모 구성 요소):</span><span class="sxs-lookup"><span data-stu-id="999b7-205">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="999b7-206">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="999b7-206">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="999b7-207">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="999b7-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="999b7-208">`Shared/SurveyPrompt.razor`(자식 구성 요소):</span><span class="sxs-lookup"><span data-stu-id="999b7-208">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="999b7-209">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="999b7-209">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="999b7-210">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="999b7-210">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="999b7-211">JS interop 호출 강화</span><span class="sxs-lookup"><span data-stu-id="999b7-211">Harden JS interop calls</span></span>

<span data-ttu-id="999b7-212">JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-212">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="999b7-213">기본적으로 Blazor Server 앱은 서버에서 1분 후에 JS interop 호출 시간을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-213">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="999b7-214">앱에서 좀 더 적극적인 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용하여 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-214">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="999b7-215">`Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-215">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="999b7-216">구성 요소 코드의 호출마다 단일 호출은 다음과 같이 제한 시간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-216">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="999b7-217">리소스 소모에 대한 자세한 내용은 <xref:blazor/security/server/threat-mitigation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-217">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="999b7-218">순환 개체 참조 방지</span><span class="sxs-lookup"><span data-stu-id="999b7-218">Avoid circular object references</span></span>

<span data-ttu-id="999b7-219">순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-219">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="999b7-220">.NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="999b7-220">.NET method calls.</span></span>
* <span data-ttu-id="999b7-221">반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="999b7-221">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="999b7-222">자세한 내용은 다음 문제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-222">For more information, see the following issues:</span></span>

* [<span data-ttu-id="999b7-223">순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="999b7-223">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="999b7-224">제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="999b7-224">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="999b7-225">Blazor JavaScript 격리 및 개체 참조</span><span class="sxs-lookup"><span data-stu-id="999b7-225">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="999b7-226">Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-226">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="999b7-227">JavaScript 격리는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-227">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="999b7-228">가져온 JavaScript는 전역 네임스페이스를 더 이상 오염시키지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-228">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="999b7-229">라이브러리 및 구성 요소의 소비자는 관련 JavaScript를 가져올 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-229">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="999b7-230">예를 들어 다음 JavaScript 모듈은 브라우저 프롬프트를 표시하기 위해 JavaScript 함수를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-230">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="999b7-231">위의 JavaScript 모듈을 .NET 라이브러리에 정적 웹 자산(`wwwroot/exampleJsInterop.js`)으로 추가한 다음, <xref:Microsoft.JSInterop.IJSRuntime> 서비스를 사용하여 .NET 코드로 모듈을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-231">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="999b7-232">다음 예제에서는 서비스가 `jsRuntime`(표시되지 않음)으로 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-232">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="999b7-233">위 예제에서 `import` 식별자는 JavaScript 모듈을 가져오기 위해 특별히 사용되는 특수 식별자입니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-233">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="999b7-234">안정적인 정적 웹 자산 경로 `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`를 사용하여 모듈을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-234">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="999b7-235">`{LIBRARY NAME}` 자리 표시자는 라이브러리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-235">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="999b7-236">`{PATH UNDER WWWROOT}` 자리 표시자는 `wwwroot` 아래의 스크립트에 대한 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-236">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="999b7-237"><xref:Microsoft.JSInterop.IJSRuntime>은 모듈을 `IJSObjectReference`로 가져와 .NET 코드에서 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-237"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="999b7-238">`IJSObjectReference`를 사용하여 모듈에서 내보낸 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-238">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="999b7-239">`IJSInProcessObjectReference`는 함수를 동기적으로 호출할 수 있는 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-239">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="999b7-240">`IJSUnmarshalledObjectReference`는 .NET 데이터를 직렬화하는 오버헤드 없이 함수를 호출할 수 있는 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-240">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="999b7-241">이 참조는 성능이 중요한 경우 Blazor WebAssembly에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-241">This can be used in Blazor WebAssembly when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="999b7-242">UI(DOM 요소)를 렌더링하는 JavaScript 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="999b7-242">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="999b7-243">브라우저 DOM 내에 표시되는 사용자 인터페이스 요소를 생성하는 JavaScript 라이브러리를 사용하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-243">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="999b7-244">처음에는, Blazor의 diff 시스템은 DOM 요소의 트리를 제어해야 하는데 일부 외부 코드가 DOM 트리를 변경하고 diff 적용 메커니즘을 무효화하면 오류가 발생하기 때문에 이것이 어려워 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-244">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="999b7-245">이는 Blazor 특정 제한 사항이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-245">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="999b7-246">동일한 문제가 모든 diff 기반 UI 프레임워크에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-246">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="999b7-247">다행히도 Blazor 구성 요소 UI 내에 외부에서 생성된 UI를 안정적으로 포함하는 것은 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-247">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="999b7-248">구성 요소 코드(`.razor` 파일)가 빈 요소를 생성하도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-248">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="999b7-249">Blazor의 diff 시스템이 관련되는 한 요소는 항상 비어 있으므로 렌더러는 요소로 재귀되지 않으며 대신 콘텐츠를 그대로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-249">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="999b7-250">이렇게 하면 외부 관리형 임의 콘텐츠로 요소를 안전하게 채울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-250">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="999b7-251">다음 예제에서는 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-251">The following example demonstrates the concept.</span></span> <span data-ttu-id="999b7-252">`firstRender`가 `true`인 경우 `if` 문 내에서 `myElement`를 사용하여 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-252">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="999b7-253">예를 들어 외부 JavaScript 라이브러리를 호출하여 요소를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-253">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="999b7-254">Blazor는 해당 구성 요소 자체가 제거될 때까지 요소 콘텐츠를 그대로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-254">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="999b7-255">구성 요소가 제거되면 구성 요소의 전체 DOM 하위 트리도 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-255">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

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

<span data-ttu-id="999b7-256">더 자세한 예제로는 [오픈 소스 Mapbox API](https://www.mapbox.com/)를 사용하여 대화형 맵을 렌더링하는 다음 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-256">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="999b7-257">`wwwroot/mapComponent.js`에 배치해야 하는 해당 JavaScript 모듈은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-257">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="999b7-258">앞의 예제에서 `{ACCESS TOKEN}` 문자열을 https://account.mapbox.com 에서 가져올 수 있는 유효한 액세스 토큰으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-258">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="999b7-259">올바른 스타일을 생성하려면 호스트 HTML 페이지(`index.html` 또는 `_Host.cshtml`)에 다음 스타일시트 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-259">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="999b7-260">앞의 예제에서는 사용자가 다음을 수행하는 대화형 맵 UI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-260">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="999b7-261">끌어서 스크롤하거나 확대/축소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-261">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="999b7-262">단추를 클릭하여 미리 정의된 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-262">Click buttons to jump to predefined locations.</span></span>

![영국 브리스틀 및 일본 도쿄를 선택하는 단추가 포함된 일본 도쿄의 Mapbox 거리 지도](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="999b7-264">이해해야 할 주요 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-264">The key points to understand are:</span></span>

 * <span data-ttu-id="999b7-265">Blazor가 관련되는 한 `@ref="mapElement"`가 있는 `<div>`는 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-265">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="999b7-266">따라서 `mapbox-gl.js`가 안전하게 요소를 채우고 시간이 지남에 따라 해당 콘텐츠를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-266">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="999b7-267">UI를 렌더링하는 JavaScript 라이브러리와 함께 이 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-267">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="999b7-268">페이지의 다른 부분에 연결하고 해당 부분을 수정하려고 시도하지 않는 한 Blazor 구성 요소 내부에 타사 JavaScript SPA 프레임워크의 구성 요소를 포함할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-268">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="999b7-269">외부 JavaScript 코드는 Blazor가 비어 있는 것으로 간주하지 않는 요소를 안전하게 수정할 수 ‘없습니다’.</span><span class="sxs-lookup"><span data-stu-id="999b7-269">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="999b7-270">이 접근 방식을 사용하는 경우 Blazor가 DOM 요소를 유지하거나 제거하는 방법에 대한 규칙에 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-270">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="999b7-271">앞의 예제에서 구성 요소는 단추 클릭 이벤트를 안전하게 처리하며, 기본적으로 가능한 경우 DOM 요소가 유지되므로 기존 맵 인스턴스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-271">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="999b7-272">`@foreach` 루프 내부에서 맵 요소 목록을 렌더링한 경우 `@key`를 사용하여 구성 요소 인스턴스를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-272">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="999b7-273">그렇지 않으면 목록 데이터의 변경으로 인해 안타깝게도 구성 요소 인스턴스가 이전 인스턴스의 상태를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-273">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="999b7-274">자세한 내용은 [@key를 사용하여 요소 및 구성 요소 유지](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-274">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="999b7-275">또한 앞의 예제에서는 ES6 모듈 내에서 JavaScript 논리 및 종속성을 캡슐화하고 `import` 식별자를 사용하여 동적으로 로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="999b7-275">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="999b7-276">자세한 내용은 [JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="999b7-276">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="999b7-277">추가 자료</span><span class="sxs-lookup"><span data-stu-id="999b7-277">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="999b7-278">InteropComponent.razor 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="999b7-278">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="999b7-279">Blazor Server 앱에서 대량 데이터 전송 수행</span><span class="sxs-lookup"><span data-stu-id="999b7-279">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
