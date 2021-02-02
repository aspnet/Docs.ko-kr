---
title: ASP.NET Core Blazor 데이터 바인딩
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: e873fdd201cf0cdc809030fde30c6a8da2899861
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751557"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor 데이터 바인딩

작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Steve Sanderson](https://github.com/SteveSandersonMS)

Razor 구성 요소는 필드, 속성 또는 Razor 식 값을 사용하여 [`@bind`](xref:mvc/views/razor#bind)라는 HTML 요소 특성을 통해 데이터 바인딩 기능을 제공합니다.

다음 예제에서는 `<input>` 요소를 `currentValue` 필드에 바인딩하고 `<input>` 요소를 `CurrentValue` 속성에 바인딩합니다.

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

요소 중 하나가 포커스를 잃으면 바인딩된 필드 또는 속성이 업데이트됩니다.

텍스트 상자는 필드 또는 속성 값 변경에 대한 대응이 아니라, 구성 요소가 렌더링되는 경우에만 UI에서 업데이트됩니다. 이벤트 처리기 코드를 실행하면 구성 요소가 자체적으로 렌더링되므로 필드 및 속성 업데이트는 ‘일반적으로’ 이벤트 처리기가 트리거되는 즉시 UI에 반영됩니다.

`CurrentValue` 속성에 [`@bind`](xref:mvc/views/razor#bind)를 사용하는 것(`<input @bind="CurrentValue" />`)은 기본적으로 다음과 같습니다.

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

구성 요소가 렌더링되면 input 요소의 `value`를 `CurrentValue` 속성에서 가져옵니다. 사용자가 텍스트 상자에 입력을 하고 요소 포커스를 변경하면 `onchange` 이벤트가 발생하고 `CurrentValue` 속성이 변경된 값으로 설정됩니다. 실제로는 [`@bind`](xref:mvc/views/razor#bind)에서 형식 변환이 수행되는 경우를 처리하므로 코드 생성은 더 복잡해집니다. 원칙적으로 [`@bind`](xref:mvc/views/razor#bind)는 식의 현재 값을 `value` 특성과 연결하고 등록된 처리기를 사용하여 변경 내용을 처리합니다.

또한 `event` 매개 변수에 `@bind:event` 특성을 포함하여 다른 이벤트에 속성 또는 필드를 바인딩합니다. 다음 예에서는 `oninput` 이벤트에서 `CurrentValue` 속성을 바인딩합니다.

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

요소가 포커스를 잃을 때 발생하는 `onchange`와는 달리 텍스트 상자의 값이 변경될 때 `oninput`이 발생합니다.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

특성 바인딩은 대/소문자를 구분합니다.

* `@bind`는 유효합니다.
* `@Bind` 및 `@BIND`는 잘못되었습니다.

## <a name="unparsable-values"></a>구문 분석할 수 없는 값

사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공하면 바인딩 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.

다음 시나리오를 고려하세요.

* `<input>` 요소는 초기 값 `123`을 사용하여 `int` 형식에 바인딩됩니다.

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* 사용자는 페이지에서 요소의 값을 `123.45`로 업데이트하고 요소 포커스를 변경합니다.

위의 시나리오에서 요소의 값은 `123`으로 되돌아갑니다. 값 `123.45`가 원래 값 `123`에 따라 거부되면 사용자는 해당 값이 수용되지 않았다는 것을 이해합니다.

기본적으로 바인딩은 요소의 `onchange` 이벤트(`@bind="{PROPERTY OR FIELD}"`)에 적용됩니다. `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`를 사용하여 다른 이벤트에 대한 바인딩을 트리거합니다. `oninput` 이벤트(`@bind:event="oninput"`)의 경우 구문 분석할 수 있는 값을 도입하는 키 입력 후에 되돌려집니다. `oninput` 이벤트의 대상을 `int` 바인딩 형식으로 지정하는 경우 사용자는 `.` 문자를 입력할 수 없게 됩니다. `.` 문자는 즉시 제거되므로 사용자는 정수만 허용된다는 즉각적인 피드백을 받습니다. 사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합하지 않은 경우가 있습니다. 대안은 다음과 같습니다.

* `oninput` 이벤트를 사용하지 마세요. 기본 `onchange` 이벤트를 사용합니다(`@bind="{PROPERTY OR FIELD}"`만 지정). 이 경우 요소가 포커스를 잃을 때까지 잘못된 값은 복귀되지 않습니다.
* `int?` 또는 `string`과 같은 nullable 형식에 바인딩하고 잘못된 항목을 처리하기 위한 사용자 지정 논리를 제공합니다.
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 또는 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>와 같은 [양식 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용합니다. 양식 유효성 검사 구성 요소에는 잘못된 입력을 관리하기 위한 기본 제공 지원이 있습니다. 자세한 내용은 <xref:blazor/forms-validation>를 참조하세요. 양식 유효성 검사 구성 요소:
  * 사용자가 연결된 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 잘못된 입력을 제공하고 유효성 검사 오류를 수신할 수 있도록 허용합니다.
  * 사용자가 추가 Webform 데이터를 입력하는 것을 방해하지 않고 UI에서 유효성 검사 오류를 표시합니다.

## <a name="format-strings"></a>형식 문자열

데이터 바인딩은 `@bind:format`을 사용하여 <xref:System.DateTime> 형식 문자열에 작동합니다. 통화 또는 숫자 형식 등의 다른 형식 식은 현재 사용할 수 없습니다.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

위의 코드에서 `<input>` 요소의 필드 형식(`type`)은 기본적으로 `text`입니다. `@bind:format`은 다음 .NET 형식을 바인딩하는 데 지원됩니다.

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정합니다. 이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석하는 데도 사용됩니다.

Blazor에서는 기본적으로 날짜 형식을 지정할 수 있도록 지원하므로 `date` 필드의 형식을 지정하는 것은 권장되지 않습니다. 권장 사항에도 불구하고 `date` 필드의 형식이 제공된 경우 바인딩이 올바르게 작동하려면 `yyyy-MM-dd` 날짜 형식만 사용합니다.

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a>구성 요소 매개 변수를 사용하여 바인딩

일반적인 시나리오는 자식 구성 요소의 속성을 부모의 속성에 바인딩하는 것입니다. 여러 수준의 바인딩이 동시에 발생하기 때문에 이 시나리오를 *체인 바인딩* 이라고 합니다.

[구성 요소 매개 변수](xref:blazor/components/index#component-parameters)는 `@bind-{PROPERTY OR FIELD}` 구문을 사용하여 부모 구성 요소의 속성 및 필드 바인딩을 허용합니다.

자식 구성 요소에 [`@bind`](xref:mvc/views/razor#bind) 구문을 사용하여 체인 바인딩을 구현할 수 없습니다. 자식 구성 요소에서 부모의 속성 업데이트를 지원하려면 이벤트 처리기와 값을 별도로 지정해야 합니다.

부모 구성 요소는 계속 [`@bind`](xref:mvc/views/razor#bind) 구문을 활용하여 자식 구성 요소와 데이터 바인딩을 설정합니다.

다음 `Child` 구성 요소(`Shared/Child.razor`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

콜백(<xref:Microsoft.AspNetCore.Components.EventCallback%601>)의 이름은 구성 요소 매개 변수 이름 뒤에 “`Changed`” 접미사(`{PARAMETER NAME}Changed`)를 붙여 지정해야 합니다. 이전 예제에서 콜백의 이름은 `YearChanged`로 지정됩니다. <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType>는 제공된 인수를 사용하여 바인딩과 연결된 대리자를 호출하고 변경된 속성에 대한 이벤트 알림을 디스패치합니다.

다음 `Parent` 구성 요소(`Parent.razor`)에서 `year` 필드는 자식 구성 요소의 `Year` 매개 변수에 바인딩됩니다.

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

`Year` 매개 변수는 `Year` 매개 변수 형식과 일치하는 도우미 `YearChanged` 이벤트를 포함하기 때문에 바인딩 가능합니다.

일반적으로 속성은 처리기에 할당된 `@bind-{PROPERTY}:event` 특성을 포함하여 해당 이벤트 처리기에 바인딩될 수 있습니다. `<Child @bind-Year="year" />`는 다음과 같이 작성하는 것과 같습니다.

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

더욱 정교한 실제 예제에서 다음 `PasswordField` 구성 요소(`PasswordField.razor`)는 다음을 수행합니다.

* `<input>` 요소의 값을 `password` 필드로 설정합니다.
* 자식의 `password` 필드의 현재 값을 인수로 전달하는 [`EventCallback`](xref:blazor/components/event-handling#eventcallback)을 사용하여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출합니다.
* `ToggleShowPassword` 메서드를 트리거하는 데 `onclick` 이벤트를 사용합니다. 자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField` 구성 요소는 다른 구성 요소에서 사용됩니다.

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

바인딩의 대리자를 호출하는 메서드에서 검사를 수행하거나 오류를 트래핑합니다. 다음 예에서는 암호 값에 공백을 사용하는 경우 사용자에게 즉각적인 피드백을 제공합니다.

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<xref:Microsoft.AspNetCore.Components.EventCallback%601>에 대한 자세한 내용은 <xref:blazor/components/event-handling#eventcallback>를 참조하세요.

## <a name="bind-across-more-than-two-components"></a>셋 이상의 구성 요소에서 바인딩

중첩된 구성 요소는 개수에 관계없이 바인딩할 수 있지만 데이터의 단방향 흐름을 준수해야 합니다.

* 변경 알림은 *계층 구조를 따라 올라갑니다*.
* 새 매개 변수 값은 *계층 구조를 따라 내려옵니다*.

일반적이고 권장되는 방법은 업데이트할 상태에 대한 혼동을 방지할 수 있게, 부모 구성 요소에 기본 데이터만 저장하는 것입니다.

다음 구성 요소는 이 개념을 설명합니다.

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

중첩되지 않을 수도 있는 구성 요소 간에 메모리 내 데이터를 공유하는 데 적합한 다른 방법은 <xref:blazor/state-management> 문서의 메모리 내 상태 컨테이너 서비스 섹션을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [양식의 라디오 단추에 바인딩](xref:blazor/forms-validation#radio-buttons)
* [양식의 C# 개체 `null` 값에 `<select>` 요소 옵션 바인딩](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
