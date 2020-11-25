---
title: ASP.NET Core에 대 한 razor 구문 참조
author: rick-anderson
description: Razor웹 페이지에 서버 기반 코드를 포함 하는 태그 구문에 대해 알아봅니다.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 91e35a7cbd97e2bd6e77566362f02409915de7d7
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035712"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a>Razor ASP.NET Core에 대 한 구문 참조

[Rick Anderson](https://twitter.com/RickAndMSFT), [Mullen](https://twitter.com/ntaylormullen)및 [Dan Vicarel](https://github.com/Rabadash8820)

Razor 서버 기반 코드를 웹 페이지에 포함 하는 태그 구문입니다. Razor구문은 Razor 태그, c # 및 HTML로 구성 됩니다. Razor일반적으로를 포함 하는 파일의 확장명은 *cshtml* 입니다. Razor는 [ Razor 구성 요소](xref:blazor/components/index) 파일 (*razor*)에도 있습니다.

## <a name="rendering-html"></a>HTML 렌더링

기본 Razor 언어는 HTML입니다. 태그에서 HTML을 렌더링 하 Razor 는 것은 html 파일에서 html을 렌더링 하는 것과는 다릅니다. *Cshtml* 파일의 HTML 태그 Razor 는 서버에서 변경 되지 않은 상태로 렌더링 됩니다.

## <a name="no-locrazor-syntax"></a>Razor 구문

Razor 는 c #을 지원 하 고 기호를 사용 하 여 `@` HTML에서 c #으로 전환 합니다. Razor c # 식을 평가 하 고 HTML 출력으로 렌더링 합니다.

`@`기호 다음에 [ Razor 예약 된 키워드가](#razor-reserved-keywords)오면 Razor 특정 태그를 특정 태그로 전환 합니다. 그렇지 않으면 C#으로 전환됩니다.

`@`태그에서 기호를 이스케이프 하려면 Razor 두 번째 기호를 사용 합니다 `@` .

```cshtml
<p>@@Username</p>
```

코드는 단일 `@` 기호를 사용하여 HTML로 렌더링됩니다.

```html
<p>@Username</p>
```

이메일 주소를 포함하는 HTML 특성 및 콘텐츠는 `@` 기호를 전환 문자로 취급하지 않습니다. 다음 예제의 전자 메일 주소는 구문 분석에 의해 영향을 받지 않습니다 Razor .

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a>암시적 Razor 식

암시적 Razor 식은로 시작 하 `@` 고 그 다음에 c # 코드를 사용 합니다.

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# `await` 키워드를 제외하고, 암시적 식에 공백이 있으면 안 됩니다. C# 문에 명확한 끝이 있으면 공백을 혼합 수 있습니다.

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

암시적 식은 C# 제네릭을 포함할 수 **없습니다**. 대괄호(`<>`) 안에 있는 문자가 HTML 태그로 해석되기 때문입니다. 다음 코드는 유효하지 **않습니다**.

```cshtml
<p>@GenericMethod<int>()</p>
```

위의 코드는 다음 중 하나와 비슷한 컴파일러 오류를 생성합니다.

* "Int" 요소가 종료되지 않았습니다. 모든 요소는 하나 있어야 자체적으로 닫히거나 일치하는 끝 태그가 있어야 합니다.
* 'GenericMethod' 메서드 그룹을 비대리자 형식 '개체'로 변환할 수 없습니다. 메서드를 호출할 생각이었나요?

제네릭 메서드 호출은 [명시적 Razor 식](#explicit-razor-expressions) 또는 [ Razor 코드 블록](#razor-code-blocks)으로 래핑해야 합니다.

## <a name="explicit-no-locrazor-expressions"></a>명시적 Razor 식

명시적 Razor 식은 짝이 되는 `@` 괄호가 있는 기호로 구성 됩니다. 지난 주 시간을 렌더링 하려면 다음 Razor 태그가 사용 됩니다.

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

`@()` 괄호 안의 모든 콘텐츠가 평가되고 출력에 렌더링됩니다.

이전 섹션에서 설명한 암시적 식은 일반적으로 공백을 포함할 수 없습니다. 다음 코드에서 일주일은 현재 시간에서 차감되지 않습니다.

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

이 코드는 다음 HTML을 렌더링합니다.

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

명시적 식은 텍스트를 식 결과와 연결하는 데 사용할 수 있습니다.

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

명시적 식이 없으면 `<p>Age@joe.Age</p>`는 이메일 주소로 처리되고 `<p>Age@joe.Age</p>`가 렌더링됩니다. 명시적 식으로 작성되면 `<p>Age33</p>`이 렌더링됩니다.

명시적 식은 *.cshtml* 파일에 있는 제네릭 메서드의 출력을 렌더링하는 데 사용할 수 있습니다. 다음 표시는 앞에서 C# 제네릭의 대괄호로 인해 발생한 오류를 해결하는 방법을 보여 줍니다. 이 코드는 명시적 식으로 작성됩니다.

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>식 인코딩

문자열로 확인되는 C# 식은 인코딩된 HTML입니다. `IHtmlContent`로 확인되는 C# 식은 `IHtmlContent.WriteTo`를 통해 직접 렌더링됩니다. `IHtmlContent`로 확인되지 않는 C# 식은 `ToString`을 통해 문자열로 변환되고 인코딩된 후 렌더링됩니다.

```cshtml
@("<span>Hello World</span>")
```

위의 코드는 다음 HTML을 렌더링 합니다.

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML은 브라우저에서 일반 텍스트로 표시 됩니다.

&lt;범위 &gt; Hello World &lt; /span&gt;

`HtmlHelper.Raw` 출력은 인코딩되지 않지만 HTML 태그로 렌더링됩니다.

> [!WARNING]
> 삭제되지 않은 사용자 입력에서 `HtmlHelper.Raw`를 사용하는 것은 보안상 위험합니다. 사용자 입력에 악의적인 JavaScript 또는 다른 악용 기법이 포함될 수 있습니다. 사용자 입력을 제거하기는 어렵습니다. 사용자 입력에 `HtmlHelper.Raw`를 사용하지 마세요.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

이 코드는 다음 HTML을 렌더링합니다.

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a>Razor 코드 블록

Razor 코드 블록은로 시작 `@` 하 고로 묶입니다 `{}` . 식과는 달리, 코드 블록 내부의 C# 코드는 렌더링되지 않습니다. 보기의 코드 블록과 식은 같은 범위를 공유하고 순서대로 정의됩니다.

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

이 코드는 다음 HTML을 렌더링합니다.

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

코드 블록에서 템플릿 메서드로 제공되는 태그를 사용하여 [로컬 함수](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)를 선언합니다.

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

이 코드는 다음 HTML을 렌더링합니다.

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>암시적 전환

코드 블록의 기본 언어는 c # 이지만 Razor 페이지를 다시 HTML로 전환할 수 있습니다.

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>구분 기호로 분리된 명시적 전환

HTML을 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 태그를 사용 하 여 렌더링 하기 위해 문자를 묶습니다 Razor `<text>` .

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

HTML 태그로 묶이지 않은 HTML을 렌더링하려면 이 방법을 사용하세요. HTML 또는 태그가 없으면 Razor Razor 런타임 오류가 발생 합니다.

`<text>` 태그는 콘텐츠를 렌더링할 때 공백을 제어하는 데 유용합니다.

* `<text>` 태그 사이의 콘텐츠만 렌더링됩니다.
* `<text>` 태그 앞 또는 뒤에 있는 공백은 HTML 출력에 나타나지 않습니다.

### <a name="explicit-line-transition"></a>명시적 줄 전환

코드 블록 내부의 나머지 전체 줄을 HTML로 렌더링하려면 `@:` 구문을 사용합니다.

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

`@:`코드에이 없으면 Razor 런타임 오류가 발생 합니다.

파일에 추가 문자를 추가 하면 `@` Razor 블록의 뒷부분에 있는 문에서 컴파일러 오류가 발생할 수 있습니다. 실제 오류가 보고된 오류보다 먼저 발생하기 때문에 이러한 컴파일러 오류를 이해하기 어려울 수 있습니다. 이 오류는 여러 암시적/명시적 식을 단일 코드 블록에 결합한 이후에 자주 발생합니다.

## <a name="control-structures"></a>제어 구조

제어 구조는 코드 블록의 확장입니다. 코드 블록의 모든 측면(태그로 전환, 인라인 C#)은 다음 구조에도 적용됩니다.

### <a name="conditionals-if-else-if-else-and-switch"></a>라도 `@if, else if, else, and @switch`

`@if`는 코드가 실행되는 시기를 제어합니다.

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` 및 `else if`는 `@` 기호가 필요 없습니다.

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

다음 태그는 switch 문 사용 방법을 보여줍니다.

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>루프로 `@for, @foreach, @while, and @do while`

템플릿 기반 HTML은 반복 제어 문으로 렌더링할 수 있습니다. 사람 목록을 렌더링하려면:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

다음 반복 문이 지원됩니다.

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>복합 `@using`

C#에서 `using` 문은 개체가 삭제되도록 보장하는 데 사용됩니다. 에서 Razor 동일한 메커니즘을 사용 하 여 추가 콘텐츠를 포함 하는 HTML 도우미를 만들 수 있습니다. 다음 코드에서 HTML 도우미는 `@using` 문을 사용하여 `<form>` 태그를 렌더링합니다.

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

예외 처리는 C#과 비슷합니다.

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor 에는 lock 문을 사용 하 여 중요 한 섹션을 보호할 수 있는 기능이 있습니다.

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>의견

Razor c # 및 HTML 주석을 지원 합니다.

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

이 코드는 다음 HTML을 렌더링합니다.

```html
<!-- HTML comment -->
```

Razor 웹 페이지를 렌더링 하기 전에 서버에서 주석을 제거 합니다. Razor 를 사용 하 여 `@*  *@` 주석을 구분 합니다. 다음 코드는 주석 처리되며, 따라서 서버에서 어떤 태그도 렌더링하지 않습니다.

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>지시문

Razor 지시문은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` . 지시문은 일반적으로 보기를 구문 분석하는 방식을 변경하거나 다른 기능을 활성화합니다.

Razor에서 뷰에 대 한 코드를 생성 하는 방법을 이해 하면 지시문이 작동 하는 방식을 보다 쉽게 이해할 수 있습니다.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

이 코드는 다음과 비슷한 클래스를 생성합니다.

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

이 문서의 뒷부분에서 [ Razor 뷰에 대해 생성 된 c # 클래스 검사](#inspect-the-razor-c-class-generated-for-a-view) 섹션에서이 생성 된 클래스를 보는 방법을 설명 합니다.

### `@attribute`

`@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다. 다음 예제에서는 `[Authorize]` 특성을 추가합니다.

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

`@code`블록을 사용 하면 [ Razor 구성](xref:blazor/components/index) 요소에서 c # 멤버 (필드, 속성 및 메서드)를 구성 요소에 추가할 수 있습니다.

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Razor구성 요소의 경우 `@code` 는의 별칭이 [`@functions`](#functions) 며 권장 됩니다 `@functions` . 두 개 이상의 `@code` 블록이 허용됩니다.

::: moniker-end

### `@functions`

`@functions` 지시문을 사용하면 C# 멤버(필드, 속성 및 메서드)를 생성된 클래스에 추가할 수 있습니다.

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

[ Razor 구성 요소](xref:blazor/components/index)에서를 `@code` 사용 `@functions` 하 여 c # 멤버를 추가 합니다.

::: moniker-end

다음은 그 예입니다.

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

이 코드는 다음과 같은 HTML 태그를 생성합니다.

```html
<div>From method: Hello</div>
```

다음 코드는 생성 된 Razor c # 클래스입니다.

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions` 메서드는 태그가 있을 때 템플릿 메서드로 제공됩니다.

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

이 코드는 다음 HTML을 렌더링합니다.

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

`@implements` 지시문은 생성된 클래스의 인터페이스를 구현합니다.

다음 예제에서는 <xref:System.IDisposable.Dispose*> 메서드를 호출할 수 있도록 <xref:System.IDisposable?displayProperty=fullName>을 구현합니다.

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

`@inherits` 지시문은 보기에서 상속하는 클래스에 대한 완전한 제어권을 제공합니다.

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

다음 코드는 사용자 지정 Razor 페이지 형식입니다.

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText`는 보기에 표시됩니다.

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

이 코드는 다음 HTML을 렌더링합니다.

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` 및 `@inherits`는 동일한 보기에 사용할 수 있습니다. `@inherits`는 보기에서 가져오는 *_ViewImports.cshtml* 파일에 있을 수 있습니다.

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

다음 코드는 강력한 형식의 보기 예제입니다.

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

"rick@contoso.com"이 모델에 전달되면 보기에서 다음과 같은 HTML 태그를 생성합니다.

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

`@inject`지시문을 사용 하면 페이지에서 서비스 Razor [컨테이너](xref:fundamentals/dependency-injection) 의 서비스를 뷰에 주입할 수 있습니다. 자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

`@layout`지시문은 구성 요소에 대 한 레이아웃을 지정 합니다 Razor . 레이아웃 구성 요소는 코드 중복 및 불일치를 방지하는 데 사용됩니다. 자세한 내용은 <xref:blazor/layouts>를 참조하세요.

::: moniker-end

### `@model`

*이 시나리오는 MVC 뷰와 Razor 페이지 (cshtml)에만 적용 됩니다.*

`@model` 지시문은 보기 또는 페이지에 전달되는 모델 형식을 지정합니다.

```cshtml
@model TypeNameOfModel
```

Razor개별 사용자 계정을 사용 하 여 만든 ASP.NET CORE MVC 또는 페이지 앱에서 *Views/Account/Login* 은 다음 모델 선언을 포함 합니다.

```cshtml
@model LoginViewModel
```

생성된 클래스는 `RazorPage<dynamic>`에서 상속합니다.

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor`Model`뷰에 전달 된 모델에 액세스 하기 위한 속성을 노출 합니다.

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model` 지시문은 `Model` 속성의 형식을 지정합니다. 이 지시문은 보기가 파생되는 클래스를 생성한 `RazorPage<T>`의 `T`를 지정합니다. `@model` 지시문이 지정되지 않을 경우 `Model` 속성은 `dynamic` 형식입니다. 자세한 내용은 [강력한 형식의 모델 및 @model 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)를 참조 하세요.

### `@namespace`

`@namespace` 지시문:

* 생성 된 Razor 페이지, MVC 뷰 또는 구성 요소의 클래스에 대 한 네임 스페이스를 설정 합니다 Razor .
* 디렉터리 *_ViewImports* 트리의 가장 가까운 imports 파일 (뷰 또는 페이지) 또는 *_Imports razor* ( Razor 구성 요소)에서 페이지, 뷰 또는 구성 요소 클래스의 루트 파생 네임 스페이스를 설정 합니다.

```cshtml
@namespace Your.Namespace.Here
```

Razor다음 표에 표시 된 페이지 예:

* 각 페이지에서는 *Pages/_ViewImports.cshtml* 을 가져옵니다.
* *Pages/_ViewImports.cshtml* 에는 `@namespace Hello.World`가 포함되어 있습니다.
* 각 페이지에는 `Hello.World`가 네임스페이스의 루트로 포함되어 있습니다.

| 페이지                                        | 네임스페이스                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

위의 관계는 MVC 뷰 및 구성 요소에 사용 되는 가져오기 파일에 적용 Razor 됩니다.

여러 가져오기 파일에 `@namespace` 지시문이 있으면 디렉터리 트리의 페이지, 보기 또는 구성 요소에 가장 가까운 파일을 사용하여 루트 네임스페이스를 설정합니다.

이전 예제의 *EvenMorePages* 폴더에 `@namespace Another.Planet`이 포함된 가져오기 파일이 있으면(또는 *Pages/MorePages/EvenMorePages/Page.cshtml* 파일에 `@namespace Another.Planet` 포함), 다음 표에 결과가 표시됩니다.

| 페이지                                        | 네임스페이스               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

`@page` 지시문은 표시되는 파일 형식에 따라 서로 다른 효과를 냅니다. 지시문:

* *. Cshtml* 파일은 파일이 페이지 임을 나타냅니다 Razor . 자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes) 및 <xref:razor-pages/index>를 참조하세요.
* Razor구성 요소가 요청을 직접 처리 하도록 지정 합니다. 자세한 내용은 <xref:blazor/fundamentals/routing>를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`@page` *Cshtml* 파일의 첫 번째 줄에 있는 지시문은 파일이 페이지 임을 나타냅니다 Razor . 자세한 내용은 <xref:razor-pages/index>를 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

*이 시나리오는 Razor 구성 요소 ()에만 적용 됩니다 `.razor` .*

(기본값)로 설정 된 경우 `false` Razor 다음의 경우 구성 요소 ()에서 렌더링 된 태그의 공백이 `.razor` 제거 됩니다.

* 요소 내에서 선행 또는 후행입니다.
* 매개 변수 내에서 선행 또는 후행 `RenderFragment` 입니다. 예를 들어 자식 콘텐츠는 다른 구성 요소로 전달 됩니다.
* 또는와 같은 c # 코드 블록 앞 이나 뒤에 `@if` 나옵니다 `@foreach` .

::: moniker-end

### `@section`

*이 시나리오는 MVC 뷰와 Razor 페이지 (cshtml)에만 적용 됩니다.*

`@section`지시문은 [MVC 및 Razor 페이지 레이아웃](xref:mvc/views/layout) 과 함께 사용 되어 뷰 또는 페이지에서 HTML 페이지의 다른 부분에 콘텐츠를 렌더링할 수 있도록 합니다. 자세한 내용은 <xref:mvc/views/layout>를 참조하세요.

### `@using`

`@using` 지시문은 C# `using` 지시문을 생성된 보기에 추가합니다.

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

[ Razor 구성 요소](xref:blazor/components/index)에서는 `@using` 범위 내에 있는 구성 요소를 제어 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>지시문 특성

Razor 지시문 특성은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` . 지시문 특성은 일반적으로 요소가 구문 분석 되거나 다른 기능을 사용 하도록 설정 하는 방식을 변경 합니다.

### `@attributes`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

`@attributes`를 사용하면 구성 요소가 선언되지 않은 특성을 렌더링할 수 있습니다. 자세한 내용은 <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>를 참조하세요.

### `@bind`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

구성 요소의 데이터 바인딩은 `@bind` 특성을 사용하여 수행됩니다. 자세한 내용은 <xref:blazor/components/data-binding>를 참조하세요.

### `@on{EVENT}`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

Razor 구성 요소에 대 한 이벤트 처리 기능을 제공 합니다. 자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

이벤트의 기본 작업을 방지합니다.

### `@on{EVENT}:stopPropagation`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

이벤트의 이벤트 전파를 중지합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

`@key` 지시어 특성을 사용하면 구성 요소 diff 알고리즘이 키의 값에 따라 요소 또는 구성 요소를 유지할 수 있습니다. 자세한 내용은 <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>를 참조하세요.

### `@ref`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

구성 요소 참조(`@ref`)에서는 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다. 자세한 내용은 <xref:blazor/components/index#capture-references-to-components>를 참조하세요.

### `@typeparam`

*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*

`@typeparam` 지시문은 생성된 구성 요소 클래스에 대한 제네릭 형식 매개 변수를 선언합니다. 자세한 내용은 <xref:blazor/components/templated-components#generic-typed-components>를 참조하세요.

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a>템플릿 Razor 대리자

Razor 템플릿을 사용 하면 다음 형식의 UI 코드 조각을 정의할 수 있습니다.

```cshtml
@<tag>...</tag>
```

다음 예제에서는 템플릿 대리자를로 지정 하는 방법을 보여 줍니다 Razor <xref:System.Func%602> . [dynamic 형식](/dotnet/csharp/programming-guide/types/using-type-dynamic)은 대리자에서 캡슐화하는 메서드의 매개 변수로 지정됩니다. [object 형식](/dotnet/csharp/language-reference/keywords/object)은 대리자의 반환 값으로 지정됩니다. 템플릿은 `Name` 속성이 있는 `Pet`의 <xref:System.Collections.Generic.List%601>에서 사용됩니다.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

템플릿은 `foreach` 문에서 제공하는 `pets`에서 렌더링됩니다.

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

렌더링된 출력:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

메서드에 대 한 인수로 인라인 템플릿을 제공할 수도 있습니다 Razor . 다음 예제에서 `Repeat` 메서드는 Razor 템플릿을 수신 합니다. 이 메서드는 템플릿을 사용하여 목록에서 제공된 항목의 반복이 포함된 HTML 콘텐츠를 생성합니다.

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

이전 예제의 애완 동물 목록을 사용하여 `Repeat` 메서드는 다음 항목과 함께 호출됩니다.

* <xref:System.Collections.Generic.List%601>의 `Pet`입니다.
* 각 애완 동물에 대한 반복 횟수
* 순서가 지정되지 않은 목록의 목록 항목에 사용할 인라인 템플릿

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

렌더링된 출력:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>태그 도우미

*이 시나리오는 MVC 뷰와 Razor 페이지 (cshtml)에만 적용 됩니다.*

[태그 도우미](xref:mvc/views/tag-helpers/intro)와 관련된 세 가지 지시문이 있습니다.

| 지시문 | 기능 |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | 보기에 태그 도우미를 제공합니다. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | 보기에서 이전에 추가된 태그 도우미를 제거합니다. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | 태그 도우미를 지원하고 태그 도우미 사용을 명시적으로 만들어주는 태그 접두사를 지정합니다. |

## <a name="no-locrazor-reserved-keywords"></a>Razor 예약 된 키워드

### <a name="no-locrazor-keywords"></a>Razor 어

* `page` (ASP.NET Core 2.1 이상 필요)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (현재 ASP.NET Core에서 지원 되지 않음)

Razor 키워드는로 이스케이프 됩니다 `@(Razor Keyword)` (예: `@(functions)` ).

### <a name="c-no-locrazor-keywords"></a>C # Razor 키워드

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

C # Razor 키워드는를 사용 하 여 이중 이스케이프 되어야 합니다 `@(@C# Razor Keyword)` (예: `@(@case)` ). 첫 번째는 `@` 파서를 이스케이프 합니다 Razor . 두 번째 `@`은 C# 파서를 이스케이프합니다.

### <a name="reserved-keywords-not-used-by-no-locrazor"></a>에서 사용 되지 않는 예약 된 키워드 Razor

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a>Razor뷰에 대해 생성 된 c # 클래스를 검사 합니다.

::: moniker range=">= aspnetcore-2.1"

.NET Core SDK 2.1 이상에서 [ Razor SDK](xref:razor-pages/sdk) 는 파일의 컴파일을 처리 Razor 합니다. 프로젝트를 빌드할 때 SDK는 Razor 프로젝트 루트에서 *>/<target_framework_moniker>/ Razor 디렉터리 build_configuration obj/<* 를 생성 합니다. 디렉터리 내의 디렉터리 구조는 *Razor* 프로젝트의 디렉터리 구조를 미러링합니다.

.NET Core 2.1를 대상으로 하는 ASP.NET Core 2.1 페이지 프로젝트에서 다음 디렉터리 구조를 고려 합니다 Razor .

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

*Debug* 구성에 프로젝트를 빌드하면 다음 *obj* 디렉터리가 생성됩니다.

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

*Pages/Index. cshtml* 에 대해 생성 된 클래스를 보려면 *obj/Debug/Netcoreapp1.0 2.1/ Razor /Pages/Index.g.cshtml.cs* 를 엽니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core MVC 프로젝트에 다음 클래스를 추가합니다.

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

`Startup.ConfigureServices`에서 MVC가 추가한 `RazorTemplateEngine`을 `CustomTemplateEngine` 클래스로 재정의합니다.

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

`CustomTemplateEngine`의 `return csharpDocument;` 문에서 중단점을 설정합니다. 중단점에서 프로그램 실행이 중지되면 `generatedCode`의 값을 확인합니다.

![generatedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>보기 조회 및 대/소문자 구분

Razor뷰 엔진은 뷰에 대해 대/소문자를 구분 하는 조회를 수행 합니다. 그러나 실제 조회는 기본 파일 시스템에 의해 결정됩니다.

* 파일 기반 원본:
  * 파일 시스템이 대/소문자를 구문하지 않은 운영 체제(예: Windows)에서는 실제 파일 공급자 조회에서 대/소문자를 구분하지 않습니다. 예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml*, */Views/home/test.cshtml* 및 기타 대/소문자 구분 변형과 일치하는 항목을 반환합니다.
  * 대/소문자를 구분하는 파일 시스템(예: Linux, OSX 및 `EmbeddedFileProvider`를 사용하는 파일 시스템)에서는 조회 시 대/소문자를 구분합니다. 예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml* 과 정확히 일치하는 항목을 찾습니다.
* 미리 컴파일된 보기: ASP.NET 2.0 이상을 사용하는 경우 모든 운영 체제에서 미리 컴파일된 보기 조회 시 대/소문자를 구분하지 않습니다. 이 동작은 Windows의 물리적 파일 공급자 동작과 동일합니다. 미리 컴파일된 두 보기의 대소문자만 다른 경우 조회 결과가 불명확합니다.

개발자는 파일 및 디렉터리 이름의 대/소문자를 다음의 대/소문자와 매칭하는 것이 좋습니다.

* 영역, 컨트롤러 및 작업 이름.
* Razor 마주보.

대/소문자를 일치시키면 배포 시 기본 파일 시스템에 관계 없이 해당 보기를 잘 찾습니다.

## <a name="additional-resources"></a>추가 리소스

을 [사용 하는 Razor ASP.NET 웹 프로그래밍 소개 구문은](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) 구문을 사용한 프로그래밍의 많은 예제를 제공 Razor 합니다.
