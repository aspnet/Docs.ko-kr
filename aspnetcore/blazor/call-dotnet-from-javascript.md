---
title: ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출
author: guardrex
description: Blazor 앱의 JavaScript 함수에서 .NET 메서드를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e602f29e6932280f4625ade64201ff232e02150d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751639"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="9f738-103">ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="9f738-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="9f738-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9f738-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9f738-105">Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="9f738-106">이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="9f738-107">이 문서에서는 JavaScript에서 .NET 메서드를 호출하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="9f738-108">.NET에서 JavaScript 함수를 호출하는 방법에 대한 내용은 <xref:blazor/call-javascript-from-dotnet>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9f738-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="9f738-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f738-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="9f738-110">`wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)의 닫는 `</body>` 태그 앞에 JS 파일(`<script>` 태그)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="9f738-111">JS interop 메서드가 있는 JS 파일은 Blazor 프레임워크 JS 파일 전에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="9f738-112">정적 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="9f738-112">Static .NET method call</span></span>

<span data-ttu-id="9f738-113">JavaScript에서 정적 .NET 메서드를 호출하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-113">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="9f738-114">호출할 정적 메서드의 식별자, 함수를 포함하는 어셈블리의 이름 및 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-114">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="9f738-115">비동기 버전은 Blazor Server 시나리오를 지원하는 데 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-115">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="9f738-116">.NET 메서드는 공용, 정적이며 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-116">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="9f738-117">개방형 제네릭 메서드를 호출하는 것은 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-117">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="9f738-118">샘플 앱에는 `int` 배열을 반환하기 위한 C# 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-118">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="9f738-119">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성이 메서드에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-119">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="9f738-120">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="9f738-120">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="9f738-121">클라이언트에 제공된 JavaScript는 C# .Net 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-121">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="9f738-122">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="9f738-122">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="9f738-123">**`Trigger .NET static method ReturnArrayAsync`** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-123">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="9f738-124">콘솔 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-124">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="9f738-125">네 번째 배열 값은 `ReturnArrayAsync`에서 반환된 배열(`data.push(4);`)로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-125">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="9f738-126">기본적으로 메서드 식별자는 메서드 이름이지만 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성 생성자를 사용하여 다른 식별자를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-126">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="9f738-127">클라이언트 쪽 JavaScript 파일에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-127">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="9f738-128">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="9f738-128">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="9f738-129">인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="9f738-129">Instance method call</span></span>

<span data-ttu-id="9f738-130">JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-130">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="9f738-131">JavaScript에서 .NET 인스턴스 메서드를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-131">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="9f738-132">JavaScript에 대한 참조로 .NET 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-132">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="9f738-133"><xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>에 대한 정적 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-133">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="9f738-134">인스턴스를 <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스로 래핑하고 <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스에서 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-134">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="9f738-135"><xref:Microsoft.JSInterop.DotNetObjectReference> 개체를 삭제합니다. 이 섹션의 뒷부분을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9f738-135">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="9f738-136">`invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 인스턴스에서 .NET 인스턴스 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-136">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="9f738-137">JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스를 인수로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-137">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="9f738-138">샘플 앱은 클라이언트 쪽 콘솔에 메시지를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-138">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="9f738-139">샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-139">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="9f738-140">**`Trigger .NET instance method HelloHelper.SayHello`** 단추를 선택하면 `ExampleJsInterop.CallHelloHelperSayHello`를 호출하고 `Blazor` 이름을 메서드에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-140">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="9f738-141">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="9f738-141">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="9f738-142">`CallHelloHelperSayHello`는 `HelloHelper`의 새 인스턴스를 사용하여 JavaScript 함수 `sayHello`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-142">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="9f738-143">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="9f738-143">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="9f738-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="9f738-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="9f738-145">이 이름은 `HelloHelper.Name` 속성을 설정하는 `HelloHelper`의 생성자에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-145">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="9f738-146">JavaScript 함수 `sayHello`가 실행되면 `HelloHelper.SayHello`는 JavaScript 함수를 통해 콘솔에 기록되는 `Hello, {Name}!` 메시지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-146">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="9f738-147">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="9f738-147">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="9f738-148">브라우저의 웹 개발자 도구에 있는 콘솔 출력:</span><span class="sxs-lookup"><span data-stu-id="9f738-148">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="9f738-149">메모리 누수를 방지하고 <xref:Microsoft.JSInterop.DotNetObjectReference>를 만드는 구성 요소에서 가비지 수집을 허용하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-149">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="9f738-150"><xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스를 만든 클래스에서 개체를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-150">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="9f738-151">`ExampleJsInterop` 클래스에 표시된 이전 패턴은 구성 요소에서 구현될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-151">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="9f738-152">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="9f738-152">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="9f738-153">구성 요소나 클래스가 <xref:Microsoft.JSInterop.DotNetObjectReference>를 삭제하지 않는 경우 `.dispose()`를 호출하여 클라이언트에서 개체를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-153">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="9f738-154">구성 요소 인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="9f738-154">Component instance method call</span></span>

<span data-ttu-id="9f738-155">구성 요소의 .NET 메서드를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-155">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="9f738-156">`invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 구성 요소에 대한 정적 메서드 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-156">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="9f738-157">구성 요소의 정적 메서드는 인스턴스 메서드 호출을 호출되는 <xref:System.Action>으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-157">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="9f738-158">여러 사용자가 동일한 구성 요소를 동시에 사용할 수 있는 Blazor Server 앱의 경우 도우미 클래스를 사용하여 인스턴스 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-158">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="9f738-159">자세한 내용은 [구성 요소 인스턴스 메서드 도우미 클래스](#component-instance-method-helper-class) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9f738-159">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="9f738-160">클라이언트 쪽 JavaScript에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-160">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="9f738-161">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="9f738-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="9f738-162">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9f738-162">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="9f738-163">인스턴스 메서드에 인수를 전달하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-163">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="9f738-164">JS 메서드 호출에 매개 변수를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-164">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="9f738-165">다음 예제에서는 이름이 메서드에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-165">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="9f738-166">필요에 따라 목록에 매개 변수를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-166">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="9f738-167">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="9f738-167">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="9f738-168">매개 변수에 대한 <xref:System.Action>에 올바른 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-168">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="9f738-169">C# 메서드에 매개 변수 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-169">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="9f738-170">매개 변수(`action.Invoke(name)`)를 사용하여 <xref:System.Action>(`UpdateMessage`)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-170">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="9f738-171">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9f738-171">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="9f738-172">**JS 메서드 호출** 단추가 선택된 경우 `message`를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-172">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="9f738-173">구성 요소 인스턴스 메서드 도우미 클래스</span><span class="sxs-lookup"><span data-stu-id="9f738-173">Component instance method helper class</span></span>

<span data-ttu-id="9f738-174">도우미 클래스는 인스턴스 메서드를 <xref:System.Action>으로 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-174">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="9f738-175">도우미 클래스는 다음과 같은 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-175">Helper classes are useful when:</span></span>

* <span data-ttu-id="9f738-176">동일한 형식의 여러 구성 요소가 동일한 페이지에 렌더링되는 경우</span><span class="sxs-lookup"><span data-stu-id="9f738-176">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="9f738-177">여러 사용자가 구성 요소를 동시에 사용할 수 있는 Blazor Server 앱을 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="9f738-177">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="9f738-178">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="9f738-178">In the following example:</span></span>

* <span data-ttu-id="9f738-179">`JSInteropExample` 구성 요소에는 몇 가지 `ListItem` 구성 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-179">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="9f738-180">각 `ListItem` 구성 요소는 메시지와 단추로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-180">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="9f738-181">`ListItem` 구성 요소 단추를 선택하면 해당 `ListItem`의 `UpdateMessage` 메서드가 목록 항목 텍스트를 변경하고 단추를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-181">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="9f738-182">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="9f738-182">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="9f738-183">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="9f738-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="9f738-184">클라이언트 쪽 JavaScript에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-184">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="9f738-185">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="9f738-185">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="9f738-186">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="9f738-186">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="9f738-187">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="9f738-187">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="9f738-188">순환 개체 참조 방지</span><span class="sxs-lookup"><span data-stu-id="9f738-188">Avoid circular object references</span></span>

<span data-ttu-id="9f738-189">순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-189">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="9f738-190">.NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="9f738-190">.NET method calls.</span></span>
* <span data-ttu-id="9f738-191">반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="9f738-191">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="9f738-192">자세한 내용은 다음 문제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9f738-192">For more information, see the following issues:</span></span>

* [<span data-ttu-id="9f738-193">순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="9f738-193">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="9f738-194">제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="9f738-194">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="9f738-195">JS Interop 호출의 크기 제한</span><span class="sxs-lookup"><span data-stu-id="9f738-195">Size limits on JS interop calls</span></span>

<span data-ttu-id="9f738-196">Blazor WebAssembly에서 프레임워크는 JS interop 입력 및 출력 크기에 제한을 두지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-196">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="9f738-197">Blazor Server에서 JS interop 호출의 크기는 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType>에 의해 적용되는 허브 메서드에 허용되는 최대 수신 SignalR 메시지 크기로 제한됩니다(기본값: 32KB).</span><span class="sxs-lookup"><span data-stu-id="9f738-197">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="9f738-198">.NET SignalR 메시지에 대한 JS가 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>보다 크면 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-198">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="9f738-199">프레임워크는 허브에서 클라이언트로 전송되는 SignalR 메시지의 크기에 제한을 적용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-199">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="9f738-200">SignalR 로깅이 [디버그](xref:Microsoft.Extensions.Logging.LogLevel) 또는 [추적](xref:Microsoft.Extensions.Logging.LogLevel)으로 설정되지 않은 경우 메시지 크기 오류는 브라우저의 개발자 도구 콘솔에만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-200">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="9f738-201">오류: 다음 오류로 인해 연결이 끊어졌습니다. ‘오류: 닫을 때 서버에서 오류를 반환했습니다. 오류로 인해 연결이 닫혔습니다.’</span><span class="sxs-lookup"><span data-stu-id="9f738-201">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="9f738-202">[SignalR 서버 쪽 로깅](xref:signalr/diagnostics#server-side-logging)이 [디버그](xref:Microsoft.Extensions.Logging.LogLevel) 또는 [추적](xref:Microsoft.Extensions.Logging.LogLevel)으로 설정되면 서버 쪽 로깅은 메시지 크기 오류에 해당하는 <xref:System.IO.InvalidDataException>을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-202">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="9f738-203">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="9f738-203">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="9f738-204">System.IO.InvalidDataException: 32768B의 최대 메시지 크기를 초과했습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-204">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="9f738-205">메시지 크기는 AddHubOptions에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-205">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="9f738-206">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>를 설정하여 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-206">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f738-207">다음 예제에서는 최대 수신 메시지 크기를 64KB(64 \* 1024)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-207">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="9f738-208">SignalR 들어오는 메시지 크기 제한을 늘리면 더 많은 서버 리소스가 필요하며 이로 인해 서버가 악의적인 사용자에게 노출될 위험이 더 커집니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-208">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="9f738-209">또한, 메모리에 대용량의 콘텐츠를 문자열 또는 바이트 배열로 읽어오면 할당이 가비지 수집기에서 제대로 작동하지 않아서 추가적인 성능 손실로 이어질 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-209">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="9f738-210">대용량의 페이로드를 읽어 오는 옵션 중 하나는 콘텐츠를 작은 청크로 전송하고 페이로드를 <xref:System.IO.Stream>으로 처리하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-210">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="9f738-211">이 옵션은 큰 JSON 페이로드를 읽어오거나 데이터를 JavaScript에서 원시 바이트로 사용할 수 있는 경우에 사용 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-211">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="9f738-212">`InputFile` 구성 요소와 유사한 기법을 사용하는 Blazor Server에서 대용량 이진 페이로드 전송을 보여주는 사례는 [이진 제출 샘플 앱](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9f738-212">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="9f738-213">JavaScript와 Blazor 간에 대용량 데이터를 전송하는 코드를 개발하는 경우 다음 지침을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-213">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="9f738-214">데이터를 작은 조각으로 분할하고, 서버가 모든 데이터를 받을 때까지 데이터 세그먼트를 순차적으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-214">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="9f738-215">JavaScript 및 C# 코드에서 큰 개체를 할당하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-215">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="9f738-216">데이터를 보내거나 받을 때 주 UI 스레드를 장기간 차단하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-216">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="9f738-217">프로세스가 완료되거나 취소되면 사용된 메모리를 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-217">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="9f738-218">보안을 위해 다음과 같은 추가 요구 사항을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-218">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="9f738-219">전달할 수 있는 최대 파일 또는 데이터 크기를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-219">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="9f738-220">클라이언트에서 서버로의 최소 업로드 속도를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-220">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="9f738-221">서버가 데이터를 받은 후에 데이터를 다음과 같이 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-221">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="9f738-222">모든 세그먼트가 수집될 때까지 메모리 버퍼에 임시로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-222">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="9f738-223">즉시 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-223">Consumed immediately.</span></span> <span data-ttu-id="9f738-224">예를 들어 각 세그먼트가 수신됨에 따라 데이터를 즉시 데이터베이스에 저장하거나 디스크에 쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-224">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="9f738-225">JS 모듈</span><span class="sxs-lookup"><span data-stu-id="9f738-225">JS modules</span></span>

<span data-ttu-id="9f738-226">JS 격리의 경우 JS interop은 [ESM(EcmaScript 모듈)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)([ECMAScript 사양](https://tc39.es/ecma262/#sec-modules))에 대한 브라우저의 기본 지원과 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9f738-226">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f738-227">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9f738-227">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="9f738-228">`InteropComponent.razor` 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="9f738-228">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
