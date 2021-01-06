<span data-ttu-id="7766c-101">중첩된 구성 요소는 일반적으로 <xref:blazor/components/data-binding>에서 설명하는 것처럼 *체인 바인딩* 을 사용하여 데이터를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7766c-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="7766c-102">중첩된 구성 요소와 중첩되지 않은 구성 요소는 등록된 메모리 내 상태 컨테이너를 사용하여 데이터에 대한 액세스를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7766c-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="7766c-103">사용자 지정 상태 컨테이너 클래스는 할당 가능한 <xref:System.Action>을 사용하여 앱의 서로 다른 구성 요소에게 상태 변경을 알립니다.</span><span class="sxs-lookup"><span data-stu-id="7766c-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="7766c-104">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7766c-104">In the following example:</span></span>

* <span data-ttu-id="7766c-105">구성 요소 쌍은 상태 컨테이너를 사용하여 속성을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="7766c-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="7766c-106">예제의 구성 요소는 중첩되어 있지만 이 방법이 작동하려면 중첩이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7766c-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="7766c-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="7766c-107">`StateContainer.cs`:</span></span>

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="7766c-108">`Program.Main`(Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="7766c-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="7766c-109">`Startup.ConfigureServices`(Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="7766c-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="7766c-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="7766c-110">`Pages/Component1.razor`:</span></span>

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="7766c-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="7766c-111">`Shared/Component2.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```
