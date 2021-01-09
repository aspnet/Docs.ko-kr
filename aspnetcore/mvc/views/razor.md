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
ms.openlocfilehash: cb9ffab19062bf726dd519c782d502f76e372073
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058287"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="8aaa0-103">Razor ASP.NET Core에 대 한 구문 참조</span><span class="sxs-lookup"><span data-stu-id="8aaa0-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="8aaa0-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mullen](https://twitter.com/ntaylormullen)및 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="8aaa0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="8aaa0-105">Razor 서버 기반 코드를 웹 페이지에 포함 하는 태그 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="8aaa0-106">Razor구문은 Razor 태그, c # 및 HTML로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="8aaa0-107">Razor일반적으로를 포함 하는 파일의 확장명은 *cshtml* 입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="8aaa0-108">Razor는 [ Razor 구성 요소](xref:blazor/components/index) 파일 (*razor*)에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="8aaa0-109">HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="8aaa0-109">Rendering HTML</span></span>

<span data-ttu-id="8aaa0-110">기본 Razor 언어는 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-110">The default Razor language is HTML.</span></span> <span data-ttu-id="8aaa0-111">태그에서 HTML을 렌더링 하 Razor 는 것은 html 파일에서 html을 렌더링 하는 것과는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="8aaa0-112">*Cshtml* 파일의 HTML 태그 Razor 는 서버에서 변경 되지 않은 상태로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="8aaa0-113">Razor 구문</span><span class="sxs-lookup"><span data-stu-id="8aaa0-113">Razor syntax</span></span>

<span data-ttu-id="8aaa0-114">Razor 는 c #을 지원 하 고 기호를 사용 하 여 `@` HTML에서 c #으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="8aaa0-115">Razor c # 식을 평가 하 고 HTML 출력으로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="8aaa0-116">`@`기호 다음에 [ Razor 예약 된 키워드가](#razor-reserved-keywords)오면 Razor 특정 태그를 특정 태그로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="8aaa0-117">그렇지 않으면 C#으로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="8aaa0-118">`@`태그에서 기호를 이스케이프 하려면 Razor 두 번째 기호를 사용 합니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="8aaa0-119">코드는 단일 `@` 기호를 사용하여 HTML로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="8aaa0-120">이메일 주소를 포함하는 HTML 특성 및 콘텐츠는 `@` 기호를 전환 문자로 취급하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="8aaa0-121">다음 예제의 전자 메일 주소는 구문 분석에 의해 영향을 받지 않습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="8aaa0-122">암시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="8aaa0-122">Implicit Razor expressions</span></span>

<span data-ttu-id="8aaa0-123">암시적 Razor 식은로 시작 하 `@` 고 그 다음에 c # 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="8aaa0-124">C# `await` 키워드를 제외하고, 암시적 식에 공백이 있으면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="8aaa0-125">C# 문에 명확한 끝이 있으면 공백을 혼합 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="8aaa0-126">암시적 식은 C# 제네릭을 포함할 수 **없습니다**. 대괄호(`<>`) 안에 있는 문자가 HTML 태그로 해석되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="8aaa0-127">다음 코드는 유효하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="8aaa0-128">위의 코드는 다음 중 하나와 비슷한 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="8aaa0-129">"Int" 요소가 종료되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="8aaa0-130">모든 요소는 하나 있어야 자체적으로 닫히거나 일치하는 끝 태그가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="8aaa0-131">'GenericMethod' 메서드 그룹을 비대리자 형식 '개체'로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="8aaa0-132">메서드를 호출할 생각이었나요?</span><span class="sxs-lookup"><span data-stu-id="8aaa0-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="8aaa0-133">제네릭 메서드 호출은 [명시적 Razor 식](#explicit-razor-expressions) 또는 [ Razor 코드 블록](#razor-code-blocks)으로 래핑해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="8aaa0-134">명시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="8aaa0-134">Explicit Razor expressions</span></span>

<span data-ttu-id="8aaa0-135">명시적 Razor 식은 짝이 되는 `@` 괄호가 있는 기호로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="8aaa0-136">지난 주 시간을 렌더링 하려면 다음 Razor 태그가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="8aaa0-137">`@()` 괄호 안의 모든 콘텐츠가 평가되고 출력에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="8aaa0-138">이전 섹션에서 설명한 암시적 식은 일반적으로 공백을 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="8aaa0-139">다음 코드에서 일주일은 현재 시간에서 차감되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="8aaa0-140">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="8aaa0-141">명시적 식은 텍스트를 식 결과와 연결하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="8aaa0-142">명시적 식이 없으면 `<p>Age@joe.Age</p>`는 이메일 주소로 처리되고 `<p>Age@joe.Age</p>`가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="8aaa0-143">명시적 식으로 작성되면 `<p>Age33</p>`이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="8aaa0-144">명시적 식은 *.cshtml* 파일에 있는 제네릭 메서드의 출력을 렌더링하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="8aaa0-145">다음 표시는 앞에서 C# 제네릭의 대괄호로 인해 발생한 오류를 해결하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="8aaa0-146">이 코드는 명시적 식으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="8aaa0-147">식 인코딩</span><span class="sxs-lookup"><span data-stu-id="8aaa0-147">Expression encoding</span></span>

<span data-ttu-id="8aaa0-148">문자열로 확인되는 C# 식은 인코딩된 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="8aaa0-149">`IHtmlContent`로 확인되는 C# 식은 `IHtmlContent.WriteTo`를 통해 직접 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="8aaa0-150">`IHtmlContent`로 확인되지 않는 C# 식은 `ToString`을 통해 문자열로 변환되고 인코딩된 후 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="8aaa0-151">위의 코드는 다음 HTML을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="8aaa0-152">HTML은 브라우저에서 일반 텍스트로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="8aaa0-153">&lt;범위 &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="8aaa0-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="8aaa0-154">`HtmlHelper.Raw` 출력은 인코딩되지 않지만 HTML 태그로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="8aaa0-155">삭제되지 않은 사용자 입력에서 `HtmlHelper.Raw`를 사용하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="8aaa0-156">사용자 입력에 악의적인 JavaScript 또는 다른 악용 기법이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="8aaa0-157">사용자 입력을 제거하기는 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="8aaa0-158">사용자 입력에 `HtmlHelper.Raw`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="8aaa0-159">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="8aaa0-160">Razor 코드 블록</span><span class="sxs-lookup"><span data-stu-id="8aaa0-160">Razor code blocks</span></span>

<span data-ttu-id="8aaa0-161">Razor 코드 블록은로 시작 `@` 하 고로 묶입니다 `{}` .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="8aaa0-162">식과는 달리, 코드 블록 내부의 C# 코드는 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="8aaa0-163">보기의 코드 블록과 식은 같은 범위를 공유하고 순서대로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="8aaa0-164">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8aaa0-165">코드 블록에서 템플릿 메서드로 제공되는 태그를 사용하여 [로컬 함수](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="8aaa0-166">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="8aaa0-167">암시적 전환</span><span class="sxs-lookup"><span data-stu-id="8aaa0-167">Implicit transitions</span></span>

<span data-ttu-id="8aaa0-168">코드 블록의 기본 언어는 c # 이지만 Razor 페이지를 다시 HTML로 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="8aaa0-169">구분 기호로 분리된 명시적 전환</span><span class="sxs-lookup"><span data-stu-id="8aaa0-169">Explicit delimited transition</span></span>

<span data-ttu-id="8aaa0-170">HTML을 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 태그를 사용 하 여 렌더링 하기 위해 문자를 묶습니다 Razor `<text>` .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="8aaa0-171">HTML 태그로 묶이지 않은 HTML을 렌더링하려면 이 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="8aaa0-172">HTML 또는 태그가 없으면 Razor Razor 런타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="8aaa0-173">`<text>` 태그는 콘텐츠를 렌더링할 때 공백을 제어하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="8aaa0-174">`<text>` 태그 사이의 콘텐츠만 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="8aaa0-175">`<text>` 태그 앞 또는 뒤에 있는 공백은 HTML 출력에 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="8aaa0-176">명시적 줄 전환</span><span class="sxs-lookup"><span data-stu-id="8aaa0-176">Explicit line transition</span></span>

<span data-ttu-id="8aaa0-177">코드 블록 내부의 나머지 전체 줄을 HTML로 렌더링하려면 `@:` 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="8aaa0-178">`@:`코드에이 없으면 Razor 런타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="8aaa0-179">파일에 추가 문자를 추가 하면 `@` Razor 블록의 뒷부분에 있는 문에서 컴파일러 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="8aaa0-180">실제 오류가 보고된 오류보다 먼저 발생하기 때문에 이러한 컴파일러 오류를 이해하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="8aaa0-181">이 오류는 여러 암시적/명시적 식을 단일 코드 블록에 결합한 이후에 자주 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="8aaa0-182">제어 구조</span><span class="sxs-lookup"><span data-stu-id="8aaa0-182">Control structures</span></span>

<span data-ttu-id="8aaa0-183">제어 구조는 코드 블록의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="8aaa0-184">코드 블록의 모든 측면(태그로 전환, 인라인 C#)은 다음 구조에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="8aaa0-185">라도 `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="8aaa0-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="8aaa0-186">`@if`는 코드가 실행되는 시기를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="8aaa0-187">`else` 및 `else if`는 `@` 기호가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="8aaa0-188">다음 태그는 switch 문 사용 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="8aaa0-189">루프로 `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="8aaa0-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="8aaa0-190">템플릿 기반 HTML은 반복 제어 문으로 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="8aaa0-191">사람 목록을 렌더링하려면:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-191">To render a list of people:</span></span>

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

<span data-ttu-id="8aaa0-192">다음 반복 문이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="8aaa0-193">복합 `@using`</span><span class="sxs-lookup"><span data-stu-id="8aaa0-193">Compound `@using`</span></span>

<span data-ttu-id="8aaa0-194">C#에서 `using` 문은 개체가 삭제되도록 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="8aaa0-195">에서 Razor 동일한 메커니즘을 사용 하 여 추가 콘텐츠를 포함 하는 HTML 도우미를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="8aaa0-196">다음 코드에서 HTML 도우미는 `@using` 문을 사용하여 `<form>` 태그를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="8aaa0-197">예외 처리는 C#과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="8aaa0-198">Razor 에는 lock 문을 사용 하 여 중요 한 섹션을 보호할 수 있는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="8aaa0-199">설명</span><span class="sxs-lookup"><span data-stu-id="8aaa0-199">Comments</span></span>

<span data-ttu-id="8aaa0-200">Razor c # 및 HTML 주석을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="8aaa0-201">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="8aaa0-202">Razor 웹 페이지를 렌더링 하기 전에 서버에서 주석을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="8aaa0-203">Razor 를 사용 하 여 `@*  *@` 주석을 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="8aaa0-204">다음 코드는 주석 처리되며, 따라서 서버에서 어떤 태그도 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="8aaa0-205">지시문</span><span class="sxs-lookup"><span data-stu-id="8aaa0-205">Directives</span></span>

<span data-ttu-id="8aaa0-206">Razor 지시문은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="8aaa0-207">지시문은 일반적으로 보기를 구문 분석하는 방식을 변경하거나 다른 기능을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="8aaa0-208">Razor에서 뷰에 대 한 코드를 생성 하는 방법을 이해 하면 지시문이 작동 하는 방식을 보다 쉽게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="8aaa0-209">이 코드는 다음과 비슷한 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="8aaa0-210">이 문서의 뒷부분에서 [ Razor 뷰에 대해 생성 된 c # 클래스 검사](#inspect-the-razor-c-class-generated-for-a-view) 섹션에서이 생성 된 클래스를 보는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="8aaa0-211">`@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="8aaa0-212">다음 예제에서는 `[Authorize]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="8aaa0-213">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-214">`@code`블록을 사용 하면 [ Razor 구성](xref:blazor/components/index) 요소에서 c # 멤버 (필드, 속성 및 메서드)를 구성 요소에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="8aaa0-215">Razor구성 요소의 경우 `@code` 는의 별칭이 [`@functions`](#functions) 며 권장 됩니다 `@functions` .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="8aaa0-216">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="8aaa0-217">`@functions` 지시문을 사용하면 C# 멤버(필드, 속성 및 메서드)를 생성된 클래스에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8aaa0-218">[ Razor 구성 요소](xref:blazor/components/index)에서를 `@code` 사용 `@functions` 하 여 c # 멤버를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="8aaa0-219">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="8aaa0-220">이 코드는 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="8aaa0-221">다음 코드는 생성 된 Razor c # 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8aaa0-222">`@functions` 메서드는 태그가 있을 때 템플릿 메서드로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="8aaa0-223">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="8aaa0-224">`@implements` 지시문은 생성된 클래스의 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="8aaa0-225">다음 예제에서는 <xref:System.IDisposable.Dispose*> 메서드를 호출할 수 있도록 <xref:System.IDisposable?displayProperty=fullName>을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="8aaa0-226">`@inherits` 지시문은 보기에서 상속하는 클래스에 대한 완전한 제어권을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="8aaa0-227">다음 코드는 사용자 지정 Razor 페이지 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="8aaa0-228">`CustomText`는 보기에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="8aaa0-229">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="8aaa0-230">`@model` 및 `@inherits`는 동일한 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="8aaa0-231">`@inherits`는 보기에서 가져오는 *_ViewImports.cshtml* 파일에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="8aaa0-232">다음 코드는 강력한 형식의 보기 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="8aaa0-233">"rick@contoso.com"이 모델에 전달되면 보기에서 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="8aaa0-234">`@inject`지시문을 사용 하면 페이지에서 서비스 Razor [컨테이너](xref:fundamentals/dependency-injection) 의 서비스를 뷰에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="8aaa0-235">자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="8aaa0-236">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-237">`@layout`지시문은 Razor 지시문을 포함 하는 라우팅 가능한 구성 요소에 대 한 레이아웃을 지정 합니다 [`@page`](#page) .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-237">The `@layout` directive specifies a layout for routable Razor components that have an [`@page`](#page) directive.</span></span> <span data-ttu-id="8aaa0-238">레이아웃 구성 요소는 코드 중복 및 불일치를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="8aaa0-239">자세한 내용은 <xref:blazor/layouts>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="8aaa0-240">*이 시나리오는 MVC 뷰와 Razor 페이지 (cshtml)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="8aaa0-241">`@model` 지시문은 보기 또는 페이지에 전달되는 모델 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="8aaa0-242">Razor개별 사용자 계정을 사용 하 여 만든 ASP.NET CORE MVC 또는 페이지 앱에서 *Views/Account/Login* 은 다음 모델 선언을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="8aaa0-243">생성된 클래스는 `RazorPage<dynamic>`에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="8aaa0-244">Razor`Model`뷰에 전달 된 모델에 액세스 하기 위한 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="8aaa0-245">`@model` 지시문은 `Model` 속성의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="8aaa0-246">이 지시문은 보기가 파생되는 클래스를 생성한 `RazorPage<T>`의 `T`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="8aaa0-247">`@model` 지시문이 지정되지 않을 경우 `Model` 속성은 `dynamic` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="8aaa0-248">자세한 내용은 [강력한 형식의 모델 및 @model 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="8aaa0-249">`@namespace` 지시문:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="8aaa0-250">생성 된 Razor 페이지, MVC 뷰 또는 구성 요소의 클래스에 대 한 네임 스페이스를 설정 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="8aaa0-251">디렉터리 *_ViewImports* 트리의 가장 가까운 imports 파일 (뷰 또는 페이지) 또는 *_Imports razor* ( Razor 구성 요소)에서 페이지, 뷰 또는 구성 요소 클래스의 루트 파생 네임 스페이스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="8aaa0-252">Razor다음 표에 표시 된 페이지 예:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="8aaa0-253">각 페이지에서는 *Pages/_ViewImports.cshtml* 을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="8aaa0-254">*Pages/_ViewImports.cshtml* 에는 `@namespace Hello.World`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="8aaa0-255">각 페이지에는 `Hello.World`가 네임스페이스의 루트로 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="8aaa0-256">페이지</span><span class="sxs-lookup"><span data-stu-id="8aaa0-256">Page</span></span>                                        | <span data-ttu-id="8aaa0-257">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="8aaa0-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="8aaa0-258">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="8aaa0-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="8aaa0-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="8aaa0-261">위의 관계는 MVC 뷰 및 구성 요소에 사용 되는 가져오기 파일에 적용 Razor 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="8aaa0-262">여러 가져오기 파일에 `@namespace` 지시문이 있으면 디렉터리 트리의 페이지, 보기 또는 구성 요소에 가장 가까운 파일을 사용하여 루트 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="8aaa0-263">이전 예제의 *EvenMorePages* 폴더에 `@namespace Another.Planet`이 포함된 가져오기 파일이 있으면(또는 *Pages/MorePages/EvenMorePages/Page.cshtml* 파일에 `@namespace Another.Planet` 포함), 다음 표에 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="8aaa0-264">페이지</span><span class="sxs-lookup"><span data-stu-id="8aaa0-264">Page</span></span>                                        | <span data-ttu-id="8aaa0-265">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="8aaa0-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="8aaa0-266">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="8aaa0-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="8aaa0-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8aaa0-269">`@page` 지시문은 표시되는 파일 형식에 따라 서로 다른 효과를 냅니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="8aaa0-270">지시문:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-270">The directive:</span></span>

* <span data-ttu-id="8aaa0-271">*. Cshtml* 파일은 파일이 페이지 임을 나타냅니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="8aaa0-272">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes) 및 <xref:razor-pages/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="8aaa0-273">Razor구성 요소가 요청을 직접 처리 하도록 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="8aaa0-274">자세한 내용은 <xref:blazor/fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8aaa0-275">`@page` *Cshtml* 파일의 첫 번째 줄에 있는 지시문은 파일이 페이지 임을 나타냅니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="8aaa0-276">자세한 내용은 <xref:razor-pages/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

<span data-ttu-id="8aaa0-277">*이 시나리오는 Razor 구성 요소 ()에만 적용 됩니다 `.razor` .*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-277">*This scenario only applies to Razor components (`.razor`).*</span></span>

<span data-ttu-id="8aaa0-278">(기본값)로 설정 된 경우 `false` Razor 다음의 경우 구성 요소 ()에서 렌더링 된 태그의 공백이 `.razor` 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-278">When set to `false` (default), whitespace in the rendered markup from Razor components (`.razor`) is removed if:</span></span>

* <span data-ttu-id="8aaa0-279">요소 내의 선행 또는 후행 공백.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-279">Leading or trailing within an element.</span></span>
* <span data-ttu-id="8aaa0-280">`RenderFragment` 매개 변수 내의 선행 또는 후행 공백.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-280">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="8aaa0-281">예: 자식 콘텐츠가 다른 구성 요소로 전달됨.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-281">For example, child content passed to another component.</span></span>
* <span data-ttu-id="8aaa0-282">C# 코드 블록(예: `@if` 또는 `@foreach`)의 앞 또는 뒤에 있는 공백.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-282">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="8aaa0-283">*이 시나리오는 MVC 뷰와 Razor 페이지 (cshtml)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-283">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="8aaa0-284">`@section`지시문은 [MVC 및 Razor 페이지 레이아웃](xref:mvc/views/layout) 과 함께 사용 되어 뷰 또는 페이지에서 HTML 페이지의 다른 부분에 콘텐츠를 렌더링할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-284">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="8aaa0-285">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-285">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="8aaa0-286">`@using` 지시문은 C# `using` 지시문을 생성된 보기에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-286">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8aaa0-287">[ Razor 구성 요소](xref:blazor/components/index)에서는 `@using` 범위 내에 있는 구성 요소를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-287">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="8aaa0-288">지시문 특성</span><span class="sxs-lookup"><span data-stu-id="8aaa0-288">Directive attributes</span></span>

<span data-ttu-id="8aaa0-289">Razor 지시문 특성은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-289">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="8aaa0-290">지시문 특성은 일반적으로 요소가 구문 분석 되거나 다른 기능을 사용 하도록 설정 하는 방식을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-290">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="8aaa0-291">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-292">`@attributes`를 사용하면 구성 요소가 선언되지 않은 특성을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-292">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="8aaa0-293">자세한 내용은 <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-293">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="8aaa0-294">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-295">구성 요소의 데이터 바인딩은 `@bind` 특성을 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-295">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="8aaa0-296">자세한 내용은 <xref:blazor/components/data-binding>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-296">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="8aaa0-297">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-298">Razor 구성 요소에 대 한 이벤트 처리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-298">Razor provides event handling features for components.</span></span> <span data-ttu-id="8aaa0-299">자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-299">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="8aaa0-300">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-301">이벤트의 기본 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-301">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="8aaa0-302">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-302">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-303">이벤트의 이벤트 전파를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-303">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="8aaa0-304">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-305">`@key` 지시어 특성을 사용하면 구성 요소 diff 알고리즘이 키의 값에 따라 요소 또는 구성 요소를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-305">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="8aaa0-306">자세한 내용은 <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-306">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="8aaa0-307">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-308">구성 요소 참조(`@ref`)에서는 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-308">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="8aaa0-309">자세한 내용은 <xref:blazor/components/index#capture-references-to-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-309">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="8aaa0-310">*이 시나리오는 Razor 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-310">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8aaa0-311">`@typeparam` 지시문은 생성된 구성 요소 클래스에 대한 제네릭 형식 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-311">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="8aaa0-312">자세한 내용은 <xref:blazor/components/templated-components#generic-typed-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-312">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="8aaa0-313">템플릿 Razor 대리자</span><span class="sxs-lookup"><span data-stu-id="8aaa0-313">Templated Razor delegates</span></span>

<span data-ttu-id="8aaa0-314">Razor 템플릿을 사용 하면 다음 형식의 UI 코드 조각을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-314">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="8aaa0-315">다음 예제에서는 템플릿 대리자를로 지정 하는 방법을 보여 줍니다 Razor <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-315">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="8aaa0-316">[dynamic 형식](/dotnet/csharp/programming-guide/types/using-type-dynamic)은 대리자에서 캡슐화하는 메서드의 매개 변수로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-316">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="8aaa0-317">[object 형식](/dotnet/csharp/language-reference/keywords/object)은 대리자의 반환 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-317">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="8aaa0-318">템플릿은 `Name` 속성이 있는 `Pet`의 <xref:System.Collections.Generic.List%601>에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-318">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="8aaa0-319">템플릿은 `foreach` 문에서 제공하는 `pets`에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-319">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="8aaa0-320">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-320">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="8aaa0-321">메서드에 대 한 인수로 인라인 템플릿을 제공할 수도 있습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-321">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="8aaa0-322">다음 예제에서 `Repeat` 메서드는 Razor 템플릿을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-322">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="8aaa0-323">이 메서드는 템플릿을 사용하여 목록에서 제공된 항목의 반복이 포함된 HTML 콘텐츠를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-323">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="8aaa0-324">이전 예제의 애완 동물 목록을 사용하여 `Repeat` 메서드는 다음 항목과 함께 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-324">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="8aaa0-325"><xref:System.Collections.Generic.List%601>의 `Pet`입니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-325"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="8aaa0-326">각 애완 동물에 대한 반복 횟수</span><span class="sxs-lookup"><span data-stu-id="8aaa0-326">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="8aaa0-327">순서가 지정되지 않은 목록의 목록 항목에 사용할 인라인 템플릿</span><span class="sxs-lookup"><span data-stu-id="8aaa0-327">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="8aaa0-328">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-328">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="8aaa0-329">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="8aaa0-329">Tag Helpers</span></span>

<span data-ttu-id="8aaa0-330">*이 시나리오는 MVC 뷰와 Razor 페이지 (cshtml)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="8aaa0-330">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="8aaa0-331">[태그 도우미](xref:mvc/views/tag-helpers/intro)와 관련된 세 가지 지시문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-331">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="8aaa0-332">지시문</span><span class="sxs-lookup"><span data-stu-id="8aaa0-332">Directive</span></span> | <span data-ttu-id="8aaa0-333">함수</span><span class="sxs-lookup"><span data-stu-id="8aaa0-333">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="8aaa0-334">보기에 태그 도우미를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-334">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="8aaa0-335">보기에서 이전에 추가된 태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-335">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="8aaa0-336">태그 도우미를 지원하고 태그 도우미 사용을 명시적으로 만들어주는 태그 접두사를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-336">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="8aaa0-337">Razor 예약 된 키워드</span><span class="sxs-lookup"><span data-stu-id="8aaa0-337">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="8aaa0-338">Razor 어</span><span class="sxs-lookup"><span data-stu-id="8aaa0-338">Razor keywords</span></span>

* <span data-ttu-id="8aaa0-339">`page` (ASP.NET Core 2.1 이상 필요)</span><span class="sxs-lookup"><span data-stu-id="8aaa0-339">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="8aaa0-340">`helper` (현재 ASP.NET Core에서 지원 되지 않음)</span><span class="sxs-lookup"><span data-stu-id="8aaa0-340">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="8aaa0-341">Razor 키워드는로 이스케이프 됩니다 `@(Razor Keyword)` (예: `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="8aaa0-341">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="8aaa0-342">C # Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="8aaa0-342">C# Razor keywords</span></span>

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

<span data-ttu-id="8aaa0-343">C # Razor 키워드는를 사용 하 여 이중 이스케이프 되어야 합니다 `@(@C# Razor Keyword)` (예: `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="8aaa0-343">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="8aaa0-344">첫 번째는 `@` 파서를 이스케이프 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-344">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="8aaa0-345">두 번째 `@`은 C# 파서를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-345">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="8aaa0-346">에서 사용 되지 않는 예약 된 키워드 Razor</span><span class="sxs-lookup"><span data-stu-id="8aaa0-346">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="8aaa0-347">Razor뷰에 대해 생성 된 c # 클래스를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-347">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8aaa0-348">.NET Core SDK 2.1 이상에서 [ Razor SDK](xref:razor-pages/sdk) 는 파일의 컴파일을 처리 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-348">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="8aaa0-349">프로젝트를 빌드할 때 SDK는 Razor 프로젝트 루트에서 *>/<target_framework_moniker>/ Razor 디렉터리 build_configuration obj/<* 를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-349">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="8aaa0-350">디렉터리 내의 디렉터리 구조는 *Razor* 프로젝트의 디렉터리 구조를 미러링합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-350">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="8aaa0-351">.NET Core 2.1를 대상으로 하는 ASP.NET Core 2.1 페이지 프로젝트에서 다음 디렉터리 구조를 고려 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8aaa0-351">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="8aaa0-352">*Debug* 구성에 프로젝트를 빌드하면 다음 *obj* 디렉터리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-352">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="8aaa0-353">*Pages/Index. cshtml* 에 대해 생성 된 클래스를 보려면 *obj/Debug/Netcoreapp1.0 2.1/ Razor /Pages/Index.g.cshtml.cs* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-353">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8aaa0-354">ASP.NET Core MVC 프로젝트에 다음 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-354">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="8aaa0-355">`Startup.ConfigureServices`에서 MVC가 추가한 `RazorTemplateEngine`을 `CustomTemplateEngine` 클래스로 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-355">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="8aaa0-356">`CustomTemplateEngine`의 `return csharpDocument;` 문에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-356">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="8aaa0-357">중단점에서 프로그램 실행이 중지되면 `generatedCode`의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-357">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="8aaa0-359">보기 조회 및 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="8aaa0-359">View lookups and case sensitivity</span></span>

<span data-ttu-id="8aaa0-360">Razor뷰 엔진은 뷰에 대해 대/소문자를 구분 하는 조회를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-360">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="8aaa0-361">그러나 실제 조회는 기본 파일 시스템에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-361">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="8aaa0-362">파일 기반 원본:</span><span class="sxs-lookup"><span data-stu-id="8aaa0-362">File based source:</span></span>
  * <span data-ttu-id="8aaa0-363">파일 시스템이 대/소문자를 구문하지 않은 운영 체제(예: Windows)에서는 실제 파일 공급자 조회에서 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-363">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="8aaa0-364">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml*, */Views/home/test.cshtml* 및 기타 대/소문자 구분 변형과 일치하는 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-364">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="8aaa0-365">대/소문자를 구분하는 파일 시스템(예: Linux, OSX 및 `EmbeddedFileProvider`를 사용하는 파일 시스템)에서는 조회 시 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-365">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="8aaa0-366">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml* 과 정확히 일치하는 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-366">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="8aaa0-367">미리 컴파일된 보기: ASP.NET 2.0 이상을 사용하는 경우 모든 운영 체제에서 미리 컴파일된 보기 조회 시 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-367">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="8aaa0-368">이 동작은 Windows의 물리적 파일 공급자 동작과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-368">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="8aaa0-369">미리 컴파일된 두 보기의 대소문자만 다른 경우 조회 결과가 불명확합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-369">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="8aaa0-370">개발자는 파일 및 디렉터리 이름의 대/소문자를 다음의 대/소문자와 매칭하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-370">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="8aaa0-371">영역, 컨트롤러 및 작업 이름.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-371">Area, controller, and action names.</span></span>
* <span data-ttu-id="8aaa0-372">Razor 마주보.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-372">Razor Pages.</span></span>

<span data-ttu-id="8aaa0-373">대/소문자를 일치시키면 배포 시 기본 파일 시스템에 관계 없이 해당 보기를 잘 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-373">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8aaa0-374">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8aaa0-374">Additional resources</span></span>

<span data-ttu-id="8aaa0-375">을 [사용 하는 Razor ASP.NET 웹 프로그래밍 소개 구문은](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) 구문을 사용한 프로그래밍의 많은 예제를 제공 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="8aaa0-375">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
