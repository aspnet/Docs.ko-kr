---
title: ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출
author: guardrex
description: 'Blazor 앱의 JavaScript 함수에서 .NET 메서드를 호출하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 1de4996b18642b7a17c696a51a0d7f909179d5f1
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507787"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="dac47-103">ASP.NET Core Blazor의 JavaScript 함수에서 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="dac47-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="dac47-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dac47-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dac47-105">Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="dac47-106">이러한 시나리오를 *JavaScript 상호 운용성* ( *JS interop* )이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="dac47-107">이 문서에서는 JavaScript에서 .NET 메서드를 호출하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="dac47-108">.NET에서 JavaScript 함수를 호출하는 방법에 대한 내용은 <xref:blazor/call-javascript-from-dotnet>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dac47-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="dac47-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dac47-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="dac47-110">정적 .NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="dac47-110">Static .NET method call</span></span>

<span data-ttu-id="dac47-111">JavaScript에서 정적 .NET 메서드를 호출하려면 `DotNet.invokeMethod` 또는 `DotNet.invokeMethodAsync` 함수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="dac47-112">호출할 정적 메서드의 식별자, 함수를 포함하는 어셈블리의 이름 및 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="dac47-113">비동기 버전은 Blazor Server 시나리오를 지원하는 데 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="dac47-114">.NET 메서드는 공용, 정적이며 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="dac47-115">개방형 제네릭 메서드를 호출하는 것은 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="dac47-116">샘플 앱에는 `int` 배열을 반환하기 위한 C# 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="dac47-117">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성이 메서드에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="dac47-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="dac47-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="dac47-119">클라이언트에 제공된 JavaScript는 C# .Net 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="dac47-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="dac47-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="dac47-121">**`Trigger .NET static method ReturnArrayAsync`** 단추를 선택한 경우 브라우저의 웹 개발자 도구에서 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="dac47-122">콘솔 출력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="dac47-123">네 번째 배열 값은 `ReturnArrayAsync`에서 반환된 배열(`data.push(4);`)로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="dac47-124">기본적으로 메서드 식별자는 메서드 이름이지만 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 특성 생성자를 사용하여 다른 식별자를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="dac47-125">클라이언트 쪽 JavaScript 파일에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="dac47-126">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dac47-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="dac47-127">인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="dac47-127">Instance method call</span></span>

<span data-ttu-id="dac47-128">JavaScript에서 .NET 인스턴스 메서드를 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="dac47-129">JavaScript에서 .NET 인스턴스 메서드를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="dac47-130">JavaScript에 대한 참조로 .NET 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="dac47-131"><xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>에 대한 정적 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="dac47-132">인스턴스를 <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스로 래핑하고 <xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스에서 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="dac47-133"><xref:Microsoft.JSInterop.DotNetObjectReference> 개체를 삭제합니다. 이 섹션의 뒷부분을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dac47-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="dac47-134">`invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 인스턴스에서 .NET 인스턴스 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="dac47-135">JavaScript에서 다른 .NET 메서드를 호출할 때 .NET 인스턴스를 인수로 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="dac47-136">샘플 앱은 클라이언트 쪽 콘솔에 메시지를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="dac47-137">샘플 앱에서 보여 주는 다음 예제에서는 브라우저의 개발자 도구에서 브라우저의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="dac47-138">**`Trigger .NET instance method HelloHelper.SayHello`** 단추를 선택하면 `ExampleJsInterop.CallHelloHelperSayHello`를 호출하고 `Blazor` 이름을 메서드에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="dac47-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="dac47-139">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="dac47-140">`CallHelloHelperSayHello`는 `HelloHelper`의 새 인스턴스를 사용하여 JavaScript 함수 `sayHello`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="dac47-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="dac47-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="dac47-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="dac47-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="dac47-143">이 이름은 `HelloHelper.Name` 속성을 설정하는 `HelloHelper`의 생성자에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="dac47-144">JavaScript 함수 `sayHello`가 실행되면 `HelloHelper.SayHello`는 JavaScript 함수를 통해 콘솔에 기록되는 `Hello, {Name}!` 메시지를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="dac47-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="dac47-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="dac47-146">브라우저의 웹 개발자 도구에 있는 콘솔 출력:</span><span class="sxs-lookup"><span data-stu-id="dac47-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="dac47-147">메모리 누수를 방지하고 <xref:Microsoft.JSInterop.DotNetObjectReference>를 만드는 구성 요소에서 가비지 수집을 허용하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="dac47-148"><xref:Microsoft.JSInterop.DotNetObjectReference> 인스턴스를 만든 클래스에서 개체를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="dac47-149">`ExampleJsInterop` 클래스에 표시된 이전 패턴은 구성 요소에서 구현될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
  
  <span data-ttu-id="dac47-150">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dac47-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="dac47-151">구성 요소나 클래스가 <xref:Microsoft.JSInterop.DotNetObjectReference>를 삭제하지 않는 경우 `.dispose()`를 호출하여 클라이언트에서 개체를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="dac47-152">구성 요소 인스턴스 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="dac47-152">Component instance method call</span></span>

<span data-ttu-id="dac47-153">구성 요소의 .NET 메서드를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="dac47-154">`invokeMethod` 또는 `invokeMethodAsync` 함수를 사용하여 구성 요소에 대한 정적 메서드 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="dac47-155">구성 요소의 정적 메서드는 인스턴스 메서드 호출을 호출되는 <xref:System.Action>으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="dac47-156">여러 사용자가 동일한 구성 요소를 동시에 사용할 수 있는 Blazor Server 앱의 경우 도우미 클래스를 사용하여 인스턴스 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="dac47-157">자세한 내용은 [구성 요소 인스턴스 메서드 도우미 클래스](#component-instance-method-helper-class) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dac47-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="dac47-158">클라이언트 쪽 JavaScript에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="dac47-159">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dac47-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="dac47-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="dac47-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="dac47-161">인스턴스 메서드에 인수를 전달하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="dac47-162">JS 메서드 호출에 매개 변수를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="dac47-163">다음 예제에서는 이름이 메서드에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="dac47-164">필요에 따라 목록에 매개 변수를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="dac47-165">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dac47-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="dac47-166">매개 변수에 대한 <xref:System.Action>에 올바른 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="dac47-167">C# 메서드에 매개 변수 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="dac47-168">매개 변수(`action.Invoke(name)`)를 사용하여 <xref:System.Action>(`UpdateMessage`)을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="dac47-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="dac47-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="dac47-170">**JS 메서드 호출** 단추가 선택된 경우 `message`를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="dac47-171">구성 요소 인스턴스 메서드 도우미 클래스</span><span class="sxs-lookup"><span data-stu-id="dac47-171">Component instance method helper class</span></span>

<span data-ttu-id="dac47-172">도우미 클래스는 인스턴스 메서드를 <xref:System.Action>으로 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="dac47-173">도우미 클래스는 다음과 같은 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="dac47-174">동일한 형식의 여러 구성 요소가 동일한 페이지에 렌더링되는 경우</span><span class="sxs-lookup"><span data-stu-id="dac47-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="dac47-175">여러 사용자가 구성 요소를 동시에 사용할 수 있는 Blazor Server 앱을 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="dac47-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="dac47-176">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="dac47-176">In the following example:</span></span>

* <span data-ttu-id="dac47-177">`JSInteropExample` 구성 요소에는 몇 가지 `ListItem` 구성 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="dac47-178">각 `ListItem` 구성 요소는 메시지와 단추로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="dac47-179">`ListItem` 구성 요소 단추를 선택하면 해당 `ListItem`의 `UpdateMessage` 메서드가 목록 항목 텍스트를 변경하고 단추를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="dac47-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="dac47-180">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="dac47-181">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dac47-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="dac47-182">클라이언트 쪽 JavaScript에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="dac47-183">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dac47-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="dac47-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="dac47-184">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="dac47-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="dac47-185">`Pages/JSInteropExample.razor`:</span></span>

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="dac47-186">순환 개체 참조 방지</span><span class="sxs-lookup"><span data-stu-id="dac47-186">Avoid circular object references</span></span>

<span data-ttu-id="dac47-187">순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dac47-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="dac47-188">.NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="dac47-188">.NET method calls.</span></span>
* <span data-ttu-id="dac47-189">반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="dac47-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="dac47-190">자세한 내용은 다음 문제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dac47-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="dac47-191">순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="dac47-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="dac47-192">제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="dac47-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="dac47-193">추가 자료</span><span class="sxs-lookup"><span data-stu-id="dac47-193">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="dac47-194">`InteropComponent.razor` 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="dac47-194">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
