중첩된 구성 요소는 일반적으로 <xref:blazor/components/data-binding>에서 설명하는 것처럼 *체인 바인딩* 을 사용하여 데이터를 바인딩합니다. 중첩된 구성 요소와 중첩되지 않은 구성 요소는 등록된 메모리 내 상태 컨테이너를 사용하여 데이터에 대한 액세스를 공유할 수 있습니다. 사용자 지정 상태 컨테이너 클래스는 할당 가능한 <xref:System.Action>을 사용하여 앱의 서로 다른 구성 요소에게 상태 변경을 알립니다. 다음 예제에서는

* 구성 요소 쌍은 상태 컨테이너를 사용하여 속성을 추적합니다.
* 예제의 구성 요소는 중첩되어 있지만 이 방법이 작동하려면 중첩이 필요하지 않습니다.

`StateContainer.cs`:

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

`Program.Main`(Blazor WebAssembly):

```csharp
builder.Services.AddSingleton<StateContainer>();
```

`Startup.ConfigureServices`(Blazor Server):

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

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

`Shared/Component2.razor`:

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
