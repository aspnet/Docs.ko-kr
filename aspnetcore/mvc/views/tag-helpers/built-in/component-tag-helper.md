---
title: ASP.NET Core의 구성 요소 태그 도우미
author: guardrex
ms.author: riande
description: ASP.NET Core 구성 요소 태그 도우미를 사용 하 여 Razor 페이지 및 뷰에서 구성 요소를 렌더링 하는 방법을 알아봅니다.
ms.custom: mvc
ms.date: 04/15/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: cddbca7f95e4d2143d4632aaa83133bc6210e251
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059158"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core의 구성 요소 태그 도우미

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

페이지 또는 뷰에서 구성 요소를 렌더링하려면 [구성 요소 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

문서의 *페이지 및 뷰에서 구성 요소를 사용 하도록 앱 준비* 섹션의 지침을 따르세요 <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> .

## <a name="component-tag-helper"></a>구성 요소 태그 도우미

다음 구성 요소 태그 도우미는 `Counter` 페이지 또는 뷰에서 구성 요소를 렌더링 합니다.

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

위의 예제에서는 `Counter` 구성 요소가 앱의 *Pages* 폴더에 있다고 가정 합니다. 자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `@using BlazorSample.Pages`).

구성 요소 태그 도우미는 구성 요소에 매개 변수를 전달할 수도 있습니다. `ColorfulCheckbox`확인란 레이블의 색 및 크기를 설정 하는 다음 구성 요소를 고려 합니다.

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

`Size`( `int` ) 및 `Color` ( `string` ) [구성 요소 매개 변수](xref:blazor/components/index#component-parameters) 는 구성 요소 태그 도우미를 통해 설정할 수 있습니다.

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

위의 예제에서는 `ColorfulCheckbox` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다. 자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `@using BlazorSample.Shared`).

페이지 또는 뷰에서 렌더링 되는 HTML은 다음과 같습니다.

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

앞의 예제에서와 같이 따옴표 붙은 문자열을 전달 하려면 [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요 `param-Color` 합니다. Razor특성이 형식이 기 때문에 형식 값에 대 한 구문 분석 동작은 `string` 특성에 적용 되지 않습니다 `param-*` `object` .

매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다. 예를 들어 `Size` `Color` 및의 형식이 `Size` `Color` `int` `string` JSON serializer에서 지원 되는 기본 형식 (및) 이기 때문에 앞의 예제에서 및에 대 한 값을 지정할 수 있습니다.

다음 예제에서는 클래스 개체가 구성 요소에 전달 됩니다.

*MyClass.cs* :

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**클래스에는 매개 변수가 없는 public 생성자가 있어야 합니다.**

*Shared/MyComponent* :

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Pages/m* :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

위의 예제에서는 `MyComponent` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다. 자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름 (예: `@using BlazorSample` 및)입니다 `@using BlazorSample.Shared` . `MyClass` 는 응용 프로그램의 네임 스페이스에 있습니다.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.

* 페이지에 미리 렌더링할지 여부
* 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

| 렌더링 모드 | 설명 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server 앱의 마커를 렌더링합니다. 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 구성 요소를 정적 HTML에 렌더링합니다. |

페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다. 구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 기능을 사용할 수 없습니다. 구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소로 구분 합니다.

정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
