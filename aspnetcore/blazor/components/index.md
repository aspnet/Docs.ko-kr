---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리하고, 구성 요소 수명 주기를 관리하는 방법을 비롯하여 Razor 구성 요소를 만들고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/components/index
ms.openlocfilehash: b87986442bb8127f03df1f7ecff8167cafa27fdf
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035686"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a>ASP.NET Core Razor 구성 요소 만들기 및 사용

작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie), 및 [Tobias Bartsch](https://www.aveo-solutions.com/)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Blazor 앱은 *구성 요소* 를 사용하여 빌드됩니다. 구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다. 구성 요소는 데이터를 주입하거나 UI 이벤트에 응답하는 데 필요한 HTML 태그와 처리 논리를 포함합니다. 구성 요소는 유연하고 간단합니다. 프로젝트 간에 중첩, 재사용 및 공유될 수 있습니다.

## <a name="component-classes"></a>구성 요소 클래스

구성 요소는 C# 및 HTML 태그 조합을 사용하여 [Razor](xref:mvc/views/razor) 구성 요소 파일(`.razor`)에서 구현됩니다. Blazor의 구성 요소는 공식적으로 ‘Razor 구성 요소’라고 합니다.

### <a name="no-locrazor-syntax"></a>Razor 구문

Blazor 앱의 Razor 구성 요소는 Razor 구문을 광범위하게 사용합니다. Razor 태그 언어에 익숙하지 않은 경우 계속하기 전에 <xref:mvc/views/razor>를 읽는 것이 좋습니다.

Razor 구문에서 콘텐츠에 액세스하는 경우 다음 섹션에 특히 주의해야 합니다.

* [지시문](xref:mvc/views/razor#directives): 일반적으로 구성 요소 태그가 구문 분석되거나 작동하는 방식을 변경하는 `@` 접두사가 있는 예약 키워드입니다.
* [지시문 특성](xref:mvc/views/razor#directive-attributes): 일반적으로 구성 요소가 구문 분석되거나 작동하는 방식을 변경하는 `@` 접두사가 있는 예약 키워드입니다.

### <a name="names"></a>이름

구성 요소의 이름은 대문자로 시작해야 합니다. 예를 들어, `MyCoolComponent.razor`는 유효하고 `myCoolComponent.razor`는 유효하지 않습니다.

### <a name="routing"></a>라우팅

Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공하여 수행됩니다. [`@page`][9] 지시문을 포함하는 Razor 파일이 컴파일되면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>가 제공됩니다. 런타임에 라우터는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>를 사용하여 구성 요소 클래스를 검색하고, 요청된 URL과 일치하는 경로 템플릿을 포함하는 구성 요소를 렌더링합니다. 자세한 내용은 <xref:blazor/fundamentals/routing>를 참조하세요.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>태그

구성 요소의 UI는 HTML을 사용하여 정의됩니다. 동적 렌더링 논리(예: 루프, 조건, 식)는 *Razor* 라고 하는 포함된 C# 구문을 사용하여 추가됩니다. 앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리는 구성 요소 클래스로 변환됩니다. 생성된 클래스의 이름은 파일 이름과 일치합니다.

구성 요소 클래스의 멤버는 [`@code`][1] 블록에서 정의됩니다. [`@code`][1] 블록에서 구성 요소 상태(속성, 필드)는 이벤트 처리 또는 다른 구성 요소 논리 정의를 위한 메서드로 지정됩니다. 두 개 이상의 [`@code`][1] 블록이 허용됩니다.

구성 요소 멤버는 `@`으로 시작되는 C# 식을 사용하는 구성 요소 렌더링 논리의 일부로 사용할 수 있습니다. 예를 들어, C# 필드는 필드 이름에 앞에 `@`을 붙여 렌더링됩니다. 다음 예제는 다음 작업을 수행합니다.

* `headingFontStyle`을 평가하고 `font-style`에 대한 CSS 속성 값으로 렌더링합니다.
* `headingText`를 평가하고 `<h1>` 요소의 내용으로 렌더링합니다.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

구성 요소가 처음 렌더링되면 구성 요소는 이벤트에 대한 응답으로 렌더링 트리를 다시 생성합니다. 그런 후 Blazor는 새로운 렌더링 트리를 이전 렌터링 트리와 비교하여 수정 사항을 브라우저 DOM(문서 개체 모델)에 적용합니다.

구성 요소는 일반 C# 클래스이며 프로젝트 내의 어느 위치에나 배치할 수 있습니다. 웹 페이지를 생성하는 구성 요소는 일반적으로 `Pages` 폴더에 있습니다. 페이지와 무관한 구성 요소는 `Shared` 폴더 또는 프로젝트에 추가된 사용자 지정 폴더에 자주 배치됩니다.

### <a name="namespaces"></a>네임스페이스

일반적으로 구성 요소의 네임스페이스는 앱의 루트 네임스페이스와 앱 내의 구성 요소 위치(폴더)에서 파생됩니다. 앱의 루트 네임스페이스가 `BlazorSample`이고 `Counter` 구성 요소가 `Pages` 폴더에 있다면 다음이 적용됩니다.

* `Counter` 구성 요소의 네임스페이스는 `BlazorSample.Pages`입니다.
* 구성 요소의 정규화된 형식 이름은 `BlazorSample.Pages.Counter`입니다.

구성 요소를 포함하는 사용자 지정 폴더의 경우 부모 구성 요소 또는 앱의 `_Imports.razor` 파일에 [`@using`][2] 지시문을 추가합니다. 다음 예에서는 `Components` 폴더의 구성 요소를 사용할 수 있도록 만듭니다.

```razor
@using BlazorSample.Components
```

구성 요소는 정규화된 이름을 사용하여 참조할 수도 있습니다. 이 경우에는 [`@using`][2] 지시문이 필요하지 않습니다.

```razor
<BlazorSample.Components.MyComponent />
```

Razor로 작성된 구성 요소의 네임스페이스는 다음을 기준으로 합니다(우선 순위에 따름).

* Razor 파일(`.razor`) 태그(`@namespace BlazorSample.MyNamespace`)의 [`@namespace`][8] 지정
* 프로젝트 파일(`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`
* 프로젝트 파일의 파일 이름(`.csproj`)에서 가져온 프로젝트 이름, 프로젝트 루트에서 구성 요소로의 경로. 예를 들어 프레임워크는 `{PROJECT ROOT}/Pages/Index.razor`(`BlazorSample.csproj`)를 `BlazorSample.Pages` 네임스페이스로 확인합니다. 구성 요소는 C# 이름 바인딩 규칙을 따릅니다. 이 예의 `Index` 구성 요소에서는 범위의 구성 요소가 모든 구성 요소입니다.
  * 동일한 폴더의 `Pages`.
  * 다른 네임스페이스를 명시적으로 지정하지 않는 프로젝트 루트의 구성 요소

> [!NOTE]
> `global::` 한정자는 지원되지 않습니다.
>
> 별칭이 지정된 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 문(예: `@using Foo = Bar`)을 사용하여 구성 요소를 가져오는 것은 지원되지 않습니다.
>
> 부분적으로 정규화된 이름은 지원되지 않습니다. 예를 들어 `<Shared.NavMenu></Shared.NavMenu>`를 사용하여 `@using BlazorSample`을 추가하고 `NavMenu` 구성 요소(`NavMenu.razor`)를 참조하는 것은 지원되지 않습니다.

### <a name="partial-class-support"></a>Partial 클래스 지원

Razor 구성 요소가 partial 클래스로 생성됩니다. Razor 구성 요소는 다음 방법 중 하나를 사용하여 작성됩니다.

* C# 코드는 단일 파일에서 HTML 태그와 Razor 코드를 사용하여 [`@code`][1] 블록에 정의됩니다. Blazor 템플릿은 이 접근 방식을 사용하여 Razor 구성 요소를 정의합니다.
* C# 코드는 partial 클래스로 정의된 코드 숨김 파일에 배치됩니다.

다음 예제에서는 Blazor 템플릿에서 생성된 앱에서 [`@code`][1] 블록을 포함하는 기본 `Counter` 구성 요소를 보여 줍니다. HTML 태그, Razor 코드 및 C# 코드는 다음과 같은 동일한 파일에 있습니다.

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Partial 클래스에서 코드 숨김 파일을 사용하여 `Counter` 구성 요소를 만들 수도 있습니다.

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

필요한 경우 partial 클래스 파일에 필요한 네임스페이스를 추가합니다. Razor 구성 요소에 사용되는 일반적인 네임스페이스는 다음과 같습니다.

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2] `_Imports.razor` 파일의 지시문은 C# 파일(`.cs`)이 아닌 Razor 파일(`.razor`)에만 적용됩니다.

### <a name="specify-a-base-class"></a>기본 클래스 지정

[`@inherits`][6] 지시어를 사용하여 구성 요소에 대한 기본 클래스를 지정할 수 있습니다. 다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속하여 구성 요소의 속성과 메서드를 제공하는 방법을 보여 줍니다. 기본 클래스는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생되어야 합니다.

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a>구성 요소 사용

구성 요소는 HTML 요소 구문을 사용하여 선언함으로써 다른 구성 요소를 포함할 수 있습니다. 구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.

`Pages/Index.razor`의 다음 태그는 `HeadingComponent` 인스턴스를 렌더링합니다.

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

구성 요소에 구성 요소 이름과 일치하지 않는 HTML 요소(첫 글자가 대문자임)가 포함되면 요소에 예기치 않은 이름이 있음을 나타내는 경고가 발생합니다. 구성 요소 네임스페이스에 대한 [`@using`][2] 지시문을 추가하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결됩니다.

## <a name="parameters"></a>매개 변수

### <a name="route-parameters"></a>경로 매개 변수

구성 요소는 [`@page`][9] 지시문에 제공된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다. 라우터는 경로 매개 변수를 사용하여 해당하는 구성 요소 매개 변수를 채웁니다.

::: moniker range=">= aspnetcore-5.0"

선택적 매개 변수가 지원됩니다. 다음 예제에서 `text` 선택적 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다. 세그먼트가 없으면 `Text` 값이 `fantastic`으로 설정됩니다.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

선택적 매개 변수는 지원되지 않으므로 앞의 예제에서 두 개의 [`@page`][9] 지시문이 적용됩니다. 첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다. 두 번째 [`@page`][9] 지시문은 `{text}` 경로 매개 변수를 받고 `Text` 속성에 값을 할당합니다.

::: moniker-end

여러 폴더 경계에서 경로를 캡처하는 범용 경로 매개 변수(`{*pageRoute}`)에 대한 자세한 내용은 <xref:blazor/fundamentals/routing#catch-all-route-parameters>을 참조하세요.

### <a name="component-parameters"></a>구성 요소 매개 변수

구성 요소에는 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성이 지정된 구성 요소 클래스의 퍼블릭 단순 또는 복합 속성을 사용하여 정의되는 구성 요소 매개 변수가 있을 수 있습니다. 특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

샘플 앱의 다음 예제에서는 `ParentComponent`는 `ChildComponent`의 `Title` 속성 값을 설정합니다.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

규칙에 따라 C# 코드로 구성되는 특성 값은 [Razor의 예약된 `@` 기호](xref:mvc/views/razor#razor-syntax)를 사용하여 매개 변수에 할당됩니다.

* 부모 필드 또는 속성: `Title="@{FIELD OR PROPERTY}`, 여기서 `{FIELD OR PROPERTY}` 자리 표시자는 부모 구성 요소의 C# 필드 또는 속성입니다.
* 메서드의 결과: `Title="@{METHOD}"`, 여기서 `{METHOD}` 자리 표시자는 부모 구성 요소의 C# 메서드입니다.
* [암시적 또는 명시적 식](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, 여기서 `{EXPRESSION}` 자리 표시자는 C# 식입니다.
  
자세한 내용은 <xref:mvc/views/razor>를 참조하세요.

> [!WARNING]
> 자체 *구성 요소 매개 변수* 에 쓰는 구성 요소를 만들지 말고 대신 private 필드를 사용합니다. 자세한 내용은 [덮어쓴 매개 변수](#overwritten-parameters) 섹션을 참조하세요.

## <a name="child-content"></a>자식 콘텐츠

구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다. 할당 구성 요소는 받는 구성 요소를 지정하는 태그 간 콘텐츠를 제공합니다.

다음 예제에서 `ChildComponent`에는 렌더링할 UI의 세그먼트를 나타내는 <xref:Microsoft.AspNetCore.Components.RenderFragment>를 나타내는 `ChildContent` 속성이 있습니다. `ChildContent`의 값은 콘텐츠를 렌더링해야 하는 구성 요소의 태그에 배치됩니다. `ChildContent` 값은 부모 구성 요소에서 수신되고 부트스트랩 패널의 `panel-body` 내에서 렌더링됩니다.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.RenderFragment> 콘텐츠를 받는 속성은 규칙에 따라 이름 `ChildContent`가 지정되어야 합니다.

샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치하여 `ChildComponent`를 렌더링하기 위한 콘텐츠를 제공할 수 있습니다.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

Blazor가 자식 콘텐츠를 렌더링하는 방식 때문에 `for` 루프 내에서 구성 요소를 렌더링하려면 자식 구성 요소 콘텐츠에서 증분 루프 변수를 사용할 경우 로컬 인덱스 변수가 필요합니다.
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> 또는 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>를 활용하여 `foreach` 루프를 사용할 수 있습니다.
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>특성 스플래팅 및 임의 매개 변수

구성 요소는 구성 요소의 선언된 매개 변수 외에, 추가 특성도 캡처하고 렌더링할 수 있습니다. 추가 특성을 사전에 캡처한 다음, [`@attributes`][3] Razor 지시문을 사용하여 구성 요소를 렌더링할 때 요소에 ‘스플래팅’할 수 있습니다. 이 시나리오는 다양한 사용자 지정을 지원하는 태그 요소를 생성하는 구성 요소를 정의할 때 유용합니다. 예를 들어, 많은 매개 변수를 지원하는 `<input>`에 대해 개별적으로 특성을 정의하는 것이 번거로울 수 있습니다.

다음 예제에서 첫 번째 `<input>` 요소(`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용하지만 두 번째 `<input>` 요소(`id="useAttributesDict"`)는 특성 스플래팅을 사용합니다.

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

매개 변수의 형식은 문자열 키를 사용하여 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`를 구현해야 합니다.

두 방법을 사용하여 렌더링되는 `<input>` 요소는 동일합니다.

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

임의 특성을 허용하려면 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 속성이 `true`로 설정된 상태로 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성을 사용하여 구성 요소 매개 변수를 정의합니다.

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)의 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 속성을 사용하면 해당 매개 변수는 다른 매개 변수와 일치하지 않는 모든 특성을 일치시킬 수 있습니다. 구성 요소는 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>를 사용하여 단일 매개 변수만 정의할 수 있습니다. <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>에 사용되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다. 이 시나리오에서는 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.

요소 특성의 위치에 상대적인 [`@attributes`][3]의 위치는 중요합니다. 요소에 [`@attributes`][3]가 스플래팅되면 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 특성이 처리됩니다. `Child` 구성 요소를 사용하는 구성 요소의 다음 예를 살펴보세요.

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` 구성 요소의 `extra` 특성은 [`@attributes`][3] 오른쪽으로 설정됩니다. 특성은 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 처리되기 때문에 `Parent` 구성 요소의 렌더링된 `<div>`는 추가 특성을 통해 전달될 때 `extra="5"`를 포함합니다.

```html
<div extra="5" />
```

다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 [`@attributes`][3]의 순서가 반대로 바뀝니다.

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` 구성 요소의 렌더링된 `<div>`에는 추가 특성을 통해 전달될 경우 `extra="10"`을 포함됩니다.

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>구성 요소에 대한 참조 캡처

구성 요소 참조에서는 해당 인스턴스에 대해 명령(예: (`Show` 또는 `Reset`)을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다. 구성 요소 참조를 캡처하려면 다음을 수행합니다.

* 자식 구성 요소에 [`@ref`][4] 특성을 추가합니다.
* 자식 구성 요소와 동일한 유형으로 필드를 정의합니다.

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

구성 요소가 렌더링되면 `loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다. 그런 다음, 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.

> [!IMPORTANT]
> `loginDialog` 변수는 구성 요소가 렌더링된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함됩니다. 구성 요소가 렌더링될 때까지는 참조할 요소가 없습니다.
>
> 구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.
>
> 이벤트 처리기에서 참조 변수를 사용하려면 람다 식을 사용하거나 [`OnAfterRenderAsync` 또는 `OnAfterRender` 메서드](xref:blazor/components/lifecycle#after-component-render)에 이벤트 처리기 대리자를 할당합니다. 이렇게 하면 이벤트 처리기가 할당되기 전에 참조 변수가 할당됩니다.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

루프의 구성 요소를 참조하려면 [Capture references to multiple similar child-components(dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)(여러 비슷한 자식 구성 요소에 대한 참조 캡처)를 참조하세요.

구성 요소 참조 캡처에는 [요소 참조 캡처](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)와 유사한 구문을 사용하지만 JavaScript interop 기능이 아닙니다. 구성 요소 참조가 JavaScript 코드로 전달되지 않습니다. 구성 요소 참조는 .NET 코드에서만 사용됩니다.

> [!NOTE]
> 구성 요소 참조를 사용하여 자식 구성 요소의 상태를 변경하지 **않도록 합니다**. 대신, 일반 선언적 매개 변수를 사용하여 자식 구성 요소에 데이터를 전달합니다. 일반 선언적 매개 변수를 사용하면 자식 구성 요소가 올바른 시간에 자동으로 다시 렌더링됩니다.

## <a name="synchronization-context"></a>동기화 컨텍스트

Blazor는 동기화 컨텍스트(<xref:System.Threading.SynchronizationContext>)를 사용하여 단일 논리적 실행 스레드를 적용합니다. 구성 요소의 [수명 주기 메서드](xref:blazor/components/lifecycle) 및 Blazor에서 발생하는 모든 이벤트 콜백은 이 동기화 컨텍스트에서 실행됩니다.

Blazor Server의 동기화 컨텍스트는 단일 스레드인 브라우저의 WebAssembly 모델과 거의 일치하도록 단일 스레드 환경 에뮬레이션을 시도합니다. 지정된 시점에서 작업이 정확히 하나의 스레드에서만 수행되어 단일 논리적 스레드의 느낌을 제공합니다. 두 작업이 동시에 실행되지는 않습니다.

### <a name="avoid-thread-blocking-calls"></a>스레드 차단 호출 방지

일반적으로 다음 메서드를 호출하지 마세요. 다음 메서드는 스레드를 차단하므로 기본 <xref:System.Threading.Tasks.Task>가 완료될 때까지 앱이 작업을 다시 시작하지 못하게 차단합니다.

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>외부에서 구성 요소 메서드를 호출하여 상태 업데이트

외부 이벤트(예: 타이머 또는 다른 알림)를 기준으로 구성 요소를 업데이트해야 하는 경우 Blazor의 동기화 컨텍스트에 디스패치되는 `InvokeAsync` 메서드를 사용합니다. 예를 들어, 업데이트된 상태를 수신 구성 요소에 알릴 수 있는 *알림 서비스* 을 고려해 보세요.

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

`NotifierService`를 등록합니다.

* Blazor WebAssembly에서 `Program.Main`의 singleton으로 서비스를 등록합니다.

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Blazor Server에서 `Startup.ConfigureServices`에 지정된 범위대로 서비스를 등록합니다.

  ```csharp
  services.AddScoped<NotifierService>();
  ```

`NotifierService`를 사용하여 구성 요소를 업데이트합니다.

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

위의 예제에서 `NotifierService`는 Blazor의 동기화 컨텍스트 외부에서 구성 요소의 `OnNotify` 메서드를 호출합니다. `InvokeAsync`는 올바른 컨텍스트로 전환하고 렌더링을 큐에 대기하는 데 사용됩니다.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@ 키를 사용하여 요소 및 구성 요소 유지

요소 또는 구성 요소 목록을 렌더링하고, 이후에 요소 또는 구성 요소가 변경되는 경우 Blazor의 Diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정해야 합니다. 일반적으로 이 프로세스는 자동이며 무시해도 되지만 프로세스를 제어하려는 경우가 있습니다.

다음 예제를 참조하세요.

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬된 항목으로 변경될 수 있습니다. 구성 요소가 다시 렌더링되면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 수신하도록 변경될 수 있습니다. 이로 인해 다시 렌더링이 예상보다 더 복잡해질 수 있습니다. 경우에 따라 다시 렌더링을 수행하면 요소 포커스 손실과 같은 동작 차이가 표시될 수 있습니다.

매핑 프로세스는 [`@key`][5] 지시문 특성을 사용하여 제어할 수 있습니다. [`@key`][5]를 사용하면 diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소가 유지되도록 합니다.

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 컬렉션이 변경되면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간 연결을 유지합니다.

* `People` 목록에서 `Person`이 삭제된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거됩니다. 다른 인스턴스는 변경되지 않은 상태로 유지됩니다.
* `Person`이 목록의 특정 위치에 삽입되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입됩니다. 다른 인스턴스는 변경되지 않은 상태로 유지됩니다.
* `Person` 항목이 다시 정렬되면 해당 `<DetailsEditor>` 인스턴스가 유지되고 UI에서 다시 정렬됩니다.

일부 시나리오에서는 [`@key`][5]를 사용하여 재랜더링의 복잡성을 최소화하고, DOM의 상태 저장 부분(예: 포커스 위치)이 변경될 수 있는 잠재적 문제를 방지할 수 있습니다.

> [!IMPORTANT]
> 키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다. 문서 전체에서 키가 전역적으로 비교되지 않습니다.

### <a name="when-to-use-key"></a>\@ 키를 사용하는 경우

일반적으로 목록이 렌더링될 때마다(예: [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 블록에서) [`@key`][5]를 사용하는 것이 적절하며 [`@key`][5]를 정의하기 위한 적절한 값이 있습니다.

[`@key`][5]를 사용하여 개체가 변경될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지하지 않도록 할 수도 있습니다.

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson`이 변경되면 [`@key`][5] 특성 지시문은 Blazor가 강제로 전체 `<div>` 및 해당 하위 항목을 삭제하고 새 요소와 구성 요소를 사용하여 UI 내에서 하위 트리를 다시 빌드하도록 합니다. 이것은 `@currentPerson`이 변경될 때 UI 상태가 유지되지 않도록 해야 하는 경우에 유용할 수 있습니다.

### <a name="when-not-to-use-key"></a>\@ 키를 사용하지 않는 경우

[`@key`][5]로 diff를 수행할 때 성능 비용이 발생합니다. 성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어할 때 앱에 도움이 되는 경우에만 [`@key`][5]를 지정합니다.

[`@key`][5]가 사용되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존합니다. [`@key`][5]를 사용할 때의 유일한 장점은 매핑을 선택하는 diff 알고리즘 대신, 모델 인스턴스가 유지된 구성 요소 인스턴스에 매핑되는 *방법* 을 제어할 수 있다는 것입니다.

### <a name="what-values-to-use-for-key"></a>\@ 키에 사용할 값

일반적으로 [`@key`][5]에 대해 다음과 같은 종류의 값 중 하나를 제공하는 것이 좋습니다.

* 모델 개체 인스턴스(예: 이전 예제와 같은 `Person` 인스턴스) 이렇게 하면 개체 참조 같음에 따라 보존이 이루어집니다.
* 고유 식별자(예: `int`, `string` 또는 `Guid` 형식의 기본 키 값)

[`@key`][5]에 사용되는 값이 충돌하지 않는지 확인합니다. 동일한 부모 요소 내에서 충돌하는 값이 감지되면 이전 요소나 구성 요소를 새 요소나 구성 요소에 확정적으로 매핑할 수 없으므로 Blazor는 예외를 throw합니다. 개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용합니다.

## <a name="overwritten-parameters"></a>덮어쓴 매개 변수

Blazor 프레임워크는 일반적으로 안전한 부모-자식 매개 변수 할당을 적용합니다.

* 매개 변수는 예기치 않게 덮어쓰지 않습니다.
* 부작용이 최소화됩니다. 예를 들어 추가 렌더링은 무한 렌더링 루프를 만들 수 있으므로 피합니다.

자식 구성 요소는 부모 구성 요소가 렌더링될 때 기존 값을 덮어쓸 수 있는 새 매개 변수 값을 받습니다. 자식 구성 요소의 매개 변수 값을 실수로 덮어쓰게 되면 하나 이상의 데이터 바인딩 매개 변수가 있는 구성 요소를 개발하고 개발자가 자식의 매개 변수에 직접 쓸 때 종종 발생합니다.

* 자식 구성 요소는 부모 구성 요소의 매개 변수 값을 하나 이상 사용하여 렌더링됩니다.
* 자식은 매개 변수 값에 직접 씁니다.
* 부모 구성 요소는 자식 매개 변수의 값을 렌더링하고 덮어씁니다.

매개 변수 값을 덮어쓸 수 있는 가능성은 자식 구성 요소의 속성 setter로도 확장됩니다.

**일반적인 지침은 자체 매개 변수에 직접 쓰는 구성 요소를 만드는 것이 아닙니다.**

다음과 같은 잘못된 `Expander` 구성 요소를 고려해 보세요.

* 자식 콘텐츠를 렌더링합니다.
* 구성 요소 매개 변수(`Expanded`)로 자식 콘텐츠 표시를 설정/해제합니다.
* 구성 요소는 `Expanded` 매개 변수에 직접 기록하는데, 이는 덮어쓴 매개 변수의 문제를 보여 주므로 피해야 합니다.

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

`Expander` 구성 요소는 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출할 수 있는 부모 구성 요소에 추가됩니다.

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

처음에 `Expander` 구성 요소는 `Expanded` 속성이 전환될 때 독립적으로 동작합니다. 자식 구성 요소는 상태를 예상대로 유지합니다. 부모에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출되면 첫 번째 자식 구성 요소의 `Expanded` 매개 변수가 초기 값(`true`)으로 다시 설정됩니다. 두 번째 `Expander` 구성 요소에서는 렌더링디는 자식 콘텐츠가 없으므로 구성 요소의 `Expanded` 값이 다시 설정되지 않습니다.

앞의 시나리오에서 상태를 유지하려면 `Expander` 구성 요소에서 ‘private 필드’를 사용하여 전환된 상태를 유지합니다 *.*

다음은 수정된 `Expander` 구성 요소입니다.

* 부모의 `Expanded` 구성 요소 매개 변수 값을 허용합니다.
* [OnInitialized 이벤트](xref:blazor/components/lifecycle#component-initialization-methods)에서 구성 요소 매개 변수 값을 ‘private 필드’(`expanded`)에 할당합니다 *.*
* 프라이빗 필드를 사용하여 내부 설정/해제 상태를 유지합니다. 이 상태는 매개 변수에 직접 쓰는 것을 방지하는 방법을 보여줍니다.

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

자세한 내용은 [Blazor 양방향 바인딩 오류(dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599)를 참조하세요. 

## <a name="apply-an-attribute"></a>특성 적용

[`@attribute`][7] 지시문을 사용하여 Razor 구성 요소에 특성을 적용할 수 있습니다. 다음 예제에서는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 구성 요소 클래스에 적용합니다.

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>조건부 HTML 요소 특성

HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링됩니다. 값이 `false` 또는 `null`이면 특성이 렌더링되지 않습니다. 값이 `true`이면 특성이 최소화된 상태로 렌더링됩니다.

다음 예제에서 `IsCompleted`는 `checked`가 요소의 태그에서 렌더링되는지를 확인합니다.

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted`가 `true`이면 확인란이 다음과 같이 렌더링됩니다.

```html
<input type="checkbox" checked />
```

`IsCompleted`가 `false`이면 확인란이 다음과 같이 렌더링됩니다.

```html
<input type="checkbox" />
```

자세한 내용은 <xref:mvc/views/razor>를 참조하세요.

> [!WARNING]
> [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) 같은 일부 HTML 특성은 .NET 형식이 `bool`일 경우 제대로 작동하지 않습니다. 이러한 경우 `bool` 대신 `string` 형식을 사용합니다.

## <a name="raw-html"></a>원시 HTML

일반적으로 문자열은 DOM 텍스트 노드를 사용하여 렌더링됩니다. 즉, 포함될 수 있는 모든 태그는 무시되고 리터럴 텍스트로 처리됩니다. 원시 HTML을 렌더링하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다. 값은 HTML 또는 SVG로 구문 분석되고 DOM에 삽입됩니다.

> [!WARNING]
> 신뢰할 수 없는 원본에서 생성된 원시 HTML을 렌더링할 경우 **보안 위험** 이 있으므로 피해야 합니다.

다음 예제에서는 `MarkupString` 형식을 사용하여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링된 출력에 추가하는 방법을 보여 줍니다.

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a>Razor 템플릿

렌더링 조각은 Razor 템플릿 구문을 사용하여 정의할 수 있습니다. Razor 템플릿은 UI 코드 조각을 정의하는 방법으로, 다음 형식을 가정합니다.

```razor
@<{HTML tag}>...</{HTML tag}>
```

다음 예제에서는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 및 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 값을 지정하고 구성 요소에서 직접 템플릿을 렌더링하는 방법을 보여 줍니다. 렌더링 조각은 [템플릿 구성 요소](xref:blazor/components/templated-components)에 인수로 전달될 수도 있습니다.

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

이전 코드의 렌더링된 출력:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>정적 자산

Blazor는 프로젝트의 [`web root (wwwroot)` 폴더](xref:fundamentals/index#web-root) 아래에 정적 자산을 배치하는 ASP.NET Core 앱의 규칙을 따릅니다.

기본 상대 경로(`/`)를 사용하여 정적 자산의 웹 루트를 참조합니다. 다음 예제에서 `logo.png`는 실제로 `{PROJECT ROOT}/wwwroot/images` 폴더에 있습니다.

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor 구성 요소는 물결표-슬래시 표기법(`~/`)을 지원하지 **않습니다**.

앱의 기본 경로를 설정하는 방법에 대한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.

## <a name="tag-helpers-arent-supported-in-components"></a>태그 도우미는 구성 요소에서 지원되지 않습니다.

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)는 Razor 구성 요소(`.razor` 파일)에서 지원되지 않습니다. Blazor에서 태그 도우미와 유사한 기능을 제공하려면 대신, 태그 도우미와 동일한 기능을 포함하는 구성 요소를 만들고 해당 구성 요소를 사용합니다.

## <a name="scalable-vector-graphics-svg-images"></a>SVG(Scalable Vector Graphics) 이미지

Blazor는 HTML을 렌더링하므로 SVG(Scalable Vector Graphics) 이미지(`.svg`)를 포함하는 브라우저 지원 이미지는 `<img>` 태그를 통해 지원됩니다.

```html
<img alt="Example image" src="some-image.svg" />
```

마찬가지로, 스타일시트 파일(`.css`)의 CSS 규칙에서는 SVG 이미지가 지원됩니다.

```css
.my-element {
    background-image: url("some-image.svg");
}
```

그러나 인라인 SVG 태그는 일부 시나리오에서 지원되지 않습니다. `<svg>` 태그를 구성 요소 파일(`.razor`)에 직접 배치하면 기본 이미지 렌더링이 지원되지만 고급 시나리오 중 아직 지원되지 않는 시나리오가 많습니다. 예를 들어, `<use>` 태그는 현재 적용되지 않으며 [`@bind`][10]를 일부 SVG 태그에서는 사용할 수 없습니다. 자세한 내용은 [Blazor(dotnet/aspnetcore #18271)에서 SVG 지원](https://github.com/dotnet/aspnetcore/issues/18271)을 참조하세요.

## <a name="whitespace-rendering-behavior"></a>공백 렌더링 동작

::: moniker range=">= aspnetcore-5.0"

[`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) 지시문이 `true` 값과 함께 사용되지 않을 때 다음과 같은 추가 공백이 제거됩니다.

* 요소 내의 선행 또는 후행 공백.
* `RenderFragment` 매개 변수 내의 선행 또는 후행 공백. 예: 자식 콘텐츠가 다른 구성 요소로 전달됨.
* C# 코드 블록(예: `@if` 또는 `@foreach`)의 앞 또는 뒤에 있는 공백.

`white-space: pre`와 같은 CSS 규칙을 사용하는 경우 공백 제거는 렌더링된 출력에 영향을 줄 수 있습니다. 성능 최적화를 사용하지 않고 공백을 유지하려면 다음 작업 중 하나를 수행합니다.

* `.razor` 파일의 맨 위에 `@preservewhitespace true` 지시문을 추가하여 특정 구성 요소에 기본 설정을 적용합니다.
* `_Imports.razor` 파일 안에 `@preservewhitespace true` 지시문을 추가하여 전체 하위 디렉터리 또는 전체 프로젝트에 기본 설정을 적용합니다.

대부분의 경우 앱이 일반적으로 계속해서 정상적으로(그러나 더 빠르게) 동작하므로 수행해야 할 조치가 없습니다. 공백 제거 시 특정 구성 요소에 문제가 발생하는 경우에는 해당 구성 요소에서 `@preservewhitespace true`를 사용하여 이 최적화를 사용하지 않도록 설정합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

구성 요소의 소스 코드에서는 공백이 유지됩니다. 공백만 있는 텍스트는 시각적 효과가 없는 경우에도 브라우저의 DOM(문서 개체 모델)에서 렌더링됩니다.

다음 Razor 구성 요소를 고려해 보세요.

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

앞의 예제에서는 다음과 같은 불필요한 공백을 렌더링합니다.

* `@foreach` 코드 블록의 외부
* `<li>` 요소의 주위
* `@item.Text` 출력의 주위

100개의 항목을 포함하는 목록의 경우 402개의 공백 영역이 있으며, 추가 공백은 렌더링된 출력에 시각적으로 영향을 주지 않습니다.

구성 요소의 정적 HTML을 렌더링하면 태그 안의 공백이 유지되지 않습니다. 예를 들어, 렌더링된 출력에서 다음 구성 요소의 소스를 살펴보겠습니다.

```razor
<img     alt="My image"   src="img.png"     />
```

앞의 Razor 마크업에서 공백이 유지되지 않습니다.

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/security/server/threat-mitigation>: 리소스를 소모하지 않도록 하는 Blazor Server 앱을 빌드하는 방법에 대한 지침을 포함합니다.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
