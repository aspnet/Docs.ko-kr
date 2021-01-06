---
title: ASP.NET Core Blazor 연계 값 및 매개 변수
author: guardrex
description: 상위 구성 요소에서 하위 구성 요소로 데이터를 전달하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 56d70cea50a3a913b4483f6ea488438269aa58fe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507982"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor 연계 값 및 매개 변수

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

일부 시나리오에서, 특히 여러 구성 요소 계층이 있는 경우 [구성 요소 매개 변수](xref:blazor/components/index#component-parameters)를 사용하여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동하는 것이 불편할 수 있습니다. 연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공하는 편리한 방법을 제공하여 이 문제를 해결합니다. 연계 값 및 매개 변수를 사용하여 구성 요소를 조정할 수도 있습니다.

### <a name="theme-example"></a>테마 예

샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 앱의 지정된 부분 내에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 테마 정보가 흐르도록 지정합니다.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

상위 구성 요소는 연계 값 구성 요소를 사용하여 연계 값을 제공할 수 있습니다. <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공합니다.

예를 들어, 샘플 앱은 테마 정보(`ThemeInfo`)를 앱 레이아웃 중 하나에 `@Body` 속성의 레이아웃 본문을 구성하는 모든 구성 요소에 대한 연계 매개 변수로 지정합니다. 레이아웃 구성 요소에서 `ButtonClass`에는 `btn-success` 값이 할당됩니다. 모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해 이 속성을 사용할 수 있습니다.

`CascadingValuesParametersLayout` 구성 요소:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

연계 값을 사용하기 위해 구성 요소는 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 특성을 사용하여 연계 매개 변수를 선언합니다. 연계 값은 형식별로 연계 매개 변수에 바인딩됩니다.

샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다. 이 매개 변수는 구성 요소에 의해 표시되는 단추 중 하나에 대해 CSS 클래스를 설정하는 데 사용됩니다.

`CascadingValuesParametersTheme` 구성 요소:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

동일한 형식의 여러 값을 동일한 하위 트리 내에서 연계하려면 각 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 구성 요소와 해당 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 특성에 고유한 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 문자열을 제공합니다. 다음 예제에서는 두 개의 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 구성 요소가 이름별로 다른 `MyCascadingType` 인스턴스를 연계합니다.

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

하위 구성 요소에서 연계 매개 변수는 상위 구성 요소의 해당 연계 값에서 해당 값을 이름별로 수신합니다.

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet 예제

연계 매개 변수를 사용하면 여러 구성 요소가 구성 요소 계층 구조 전체에서 공동으로 작용할 수 있습니다. 예를 들어 샘플 앱에서 다음 `TabSet` 예제를 고려합니다.

샘플 앱에는 탭이 구현하는 `ITab` 인터페이스가 있습니다.

[!code-csharp[](../common/samples/5.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함하는 `TabSet` 구성 요소를 사용합니다.

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

자식 `Tab` 구성 요소는 `TabSet`에 대한 매개 변수로 명시적으로 전달되지 않습니다. 대신, 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다. 그러나 `TabSet`은 헤더 및 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구하지 않고 이러한 조정을 사용하도록 설정하기 위해 `TabSet` 구성 요소는 연계 값으로 제공된 다음, 하위 `Tab` 구성 요소에서 선택될 수 있습니다.

`TabSet` 구성 요소:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TabSet.razor)]

하위 `Tab` 구성 요소는 포함하는 `TabSet`을 연계 매개 변수로 캡처하므로 `Tab` 구성 요소는 `TabSet`에 추가되고 활성 상태인 탭을 조정합니다.

`Tab` 구성 요소:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/Tab.razor)]
