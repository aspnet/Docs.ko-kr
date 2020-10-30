---
title: ASP.NET Core에 대 한 razor 구문 참조
author: rick-anderson
description: :::no-loc(Razor):::웹 페이지에 서버 기반 코드를 포함 하는 태그 구문에 대해 알아봅니다.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/razor
ms.openlocfilehash: c1278b0cd3e58814b1c06dca81efd662c3de0c54
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059197"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="02f70-103">:::no-loc(Razor)::: ASP.NET Core에 대 한 구문 참조</span><span class="sxs-lookup"><span data-stu-id="02f70-103">:::no-loc(Razor)::: syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="02f70-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mullen](https://twitter.com/ntaylormullen)및 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="02f70-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="02f70-105">:::no-loc(Razor)::: 서버 기반 코드를 웹 페이지에 포함 하는 태그 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-105">:::no-loc(Razor)::: is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="02f70-106">:::no-loc(Razor):::구문은 :::no-loc(Razor)::: 태그, c # 및 HTML로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-106">The :::no-loc(Razor)::: syntax consists of :::no-loc(Razor)::: markup, C#, and HTML.</span></span> <span data-ttu-id="02f70-107">:::no-loc(Razor):::일반적으로를 포함 하는 파일의 확장명은 *cshtml* 입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-107">Files containing :::no-loc(Razor)::: generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="02f70-108">:::no-loc(Razor):::는 [ :::no-loc(Razor)::: 구성 요소](xref:blazor/components/index) 파일 ( *razor* )에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-108">:::no-loc(Razor)::: is also found in [:::no-loc(Razor)::: components](xref:blazor/components/index) files ( *.razor* ).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="02f70-109">HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="02f70-109">Rendering HTML</span></span>

<span data-ttu-id="02f70-110">기본 :::no-loc(Razor)::: 언어는 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-110">The default :::no-loc(Razor)::: language is HTML.</span></span> <span data-ttu-id="02f70-111">태그에서 HTML을 렌더링 하 :::no-loc(Razor)::: 는 것은 html 파일에서 html을 렌더링 하는 것과는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-111">Rendering HTML from :::no-loc(Razor)::: markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="02f70-112">*Cshtml* 파일의 HTML 태그 :::no-loc(Razor)::: 는 서버에서 변경 되지 않은 상태로 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-112">HTML markup in *.cshtml* :::no-loc(Razor)::: files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="02f70-113">:::no-loc(Razor)::: 구문</span><span class="sxs-lookup"><span data-stu-id="02f70-113">:::no-loc(Razor)::: syntax</span></span>

<span data-ttu-id="02f70-114">:::no-loc(Razor)::: 는 c #을 지원 하 고 기호를 사용 하 여 `@` HTML에서 c #으로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-114">:::no-loc(Razor)::: supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="02f70-115">:::no-loc(Razor)::: c # 식을 평가 하 고 HTML 출력으로 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-115">:::no-loc(Razor)::: evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="02f70-116">`@`기호 다음에 [ :::no-loc(Razor)::: 예약 된 키워드가](#razor-reserved-keywords)오면 :::no-loc(Razor)::: 특정 태그를 특정 태그로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-116">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="02f70-117">그렇지 않으면 C#으로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="02f70-118">`@`태그에서 기호를 이스케이프 하려면 :::no-loc(Razor)::: 두 번째 기호를 사용 합니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="02f70-118">To escape an `@` symbol in :::no-loc(Razor)::: markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="02f70-119">코드는 단일 `@` 기호를 사용하여 HTML로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="02f70-120">이메일 주소를 포함하는 HTML 특성 및 콘텐츠는 `@` 기호를 전환 문자로 취급하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="02f70-121">다음 예제의 전자 메일 주소는 구문 분석에 의해 영향을 받지 않습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-121">The email addresses in the following example are untouched by :::no-loc(Razor)::: parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="02f70-122">암시적 :::no-loc(Razor)::: 식</span><span class="sxs-lookup"><span data-stu-id="02f70-122">Implicit :::no-loc(Razor)::: expressions</span></span>

<span data-ttu-id="02f70-123">암시적 :::no-loc(Razor)::: 식은로 시작 하 `@` 고 그 다음에 c # 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-123">Implicit :::no-loc(Razor)::: expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="02f70-124">C# `await` 키워드를 제외하고, 암시적 식에 공백이 있으면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="02f70-125">C# 문에 명확한 끝이 있으면 공백을 혼합 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="02f70-126">암시적 식은 C# 제네릭을 포함할 수 **없습니다** . 대괄호(`<>`) 안에 있는 문자가 HTML 태그로 해석되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="02f70-127">다음 코드는 유효하지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="02f70-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="02f70-128">위의 코드는 다음 중 하나와 비슷한 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="02f70-129">"Int" 요소가 종료되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="02f70-130">모든 요소는 하나 있어야 자체적으로 닫히거나 일치하는 끝 태그가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="02f70-131">'GenericMethod' 메서드 그룹을 비대리자 형식 '개체'로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="02f70-132">메서드를 호출할 생각이었나요?</span><span class="sxs-lookup"><span data-stu-id="02f70-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="02f70-133">제네릭 메서드 호출은 [명시적 :::no-loc(Razor)::: 식](#explicit-razor-expressions) 또는 [ :::no-loc(Razor)::: 코드 블록](#razor-code-blocks)으로 래핑해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-133">Generic method calls must be wrapped in an [explicit :::no-loc(Razor)::: expression](#explicit-razor-expressions) or a [:::no-loc(Razor)::: code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="02f70-134">명시적 :::no-loc(Razor)::: 식</span><span class="sxs-lookup"><span data-stu-id="02f70-134">Explicit :::no-loc(Razor)::: expressions</span></span>

<span data-ttu-id="02f70-135">명시적 :::no-loc(Razor)::: 식은 짝이 되는 `@` 괄호가 있는 기호로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-135">Explicit :::no-loc(Razor)::: expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="02f70-136">지난 주 시간을 렌더링 하려면 다음 :::no-loc(Razor)::: 태그가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-136">To render last week's time, the following :::no-loc(Razor)::: markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="02f70-137">`@()` 괄호 안의 모든 콘텐츠가 평가되고 출력에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="02f70-138">이전 섹션에서 설명한 암시적 식은 일반적으로 공백을 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="02f70-139">다음 코드에서 일주일은 현재 시간에서 차감되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="02f70-140">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="02f70-141">명시적 식은 텍스트를 식 결과와 연결하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="02f70-142">명시적 식이 없으면 `<p>Age@joe.Age</p>`는 이메일 주소로 처리되고 `<p>Age@joe.Age</p>`가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="02f70-143">명시적 식으로 작성되면 `<p>Age33</p>`이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="02f70-144">명시적 식은 *.cshtml* 파일에 있는 제네릭 메서드의 출력을 렌더링하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="02f70-145">다음 표시는 앞에서 C# 제네릭의 대괄호로 인해 발생한 오류를 해결하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="02f70-146">이 코드는 명시적 식으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="02f70-147">식 인코딩</span><span class="sxs-lookup"><span data-stu-id="02f70-147">Expression encoding</span></span>

<span data-ttu-id="02f70-148">문자열로 확인되는 C# 식은 인코딩된 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="02f70-149">`IHtmlContent`로 확인되는 C# 식은 `IHtmlContent.WriteTo`를 통해 직접 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="02f70-150">`IHtmlContent`로 확인되지 않는 C# 식은 `ToString`을 통해 문자열로 변환되고 인코딩된 후 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="02f70-151">위의 코드는 다음 HTML을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="02f70-152">HTML은 브라우저에서 일반 텍스트로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="02f70-153">&lt;범위 &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="02f70-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="02f70-154">`HtmlHelper.Raw` 출력은 인코딩되지 않지만 HTML 태그로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="02f70-155">삭제되지 않은 사용자 입력에서 `HtmlHelper.Raw`를 사용하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="02f70-156">사용자 입력에 악의적인 JavaScript 또는 다른 악용 기법이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="02f70-157">사용자 입력을 제거하기는 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="02f70-158">사용자 입력에 `HtmlHelper.Raw`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="02f70-159">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="02f70-160">:::no-loc(Razor)::: 코드 블록</span><span class="sxs-lookup"><span data-stu-id="02f70-160">:::no-loc(Razor)::: code blocks</span></span>

<span data-ttu-id="02f70-161">:::no-loc(Razor)::: 코드 블록은로 시작 `@` 하 고로 묶입니다 `{}` .</span><span class="sxs-lookup"><span data-stu-id="02f70-161">:::no-loc(Razor)::: code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="02f70-162">식과는 달리, 코드 블록 내부의 C# 코드는 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="02f70-163">보기의 코드 블록과 식은 같은 범위를 공유하고 순서대로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="02f70-164">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02f70-165">코드 블록에서 템플릿 메서드로 제공되는 태그를 사용하여 [로컬 함수](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="02f70-166">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="02f70-167">암시적 전환</span><span class="sxs-lookup"><span data-stu-id="02f70-167">Implicit transitions</span></span>

<span data-ttu-id="02f70-168">코드 블록의 기본 언어는 c # 이지만 :::no-loc(Razor)::: 페이지를 다시 HTML로 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-168">The default language in a code block is C#, but the :::no-loc(Razor)::: Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="02f70-169">구분 기호로 분리된 명시적 전환</span><span class="sxs-lookup"><span data-stu-id="02f70-169">Explicit delimited transition</span></span>

<span data-ttu-id="02f70-170">HTML을 렌더링 해야 하는 코드 블록의 하위 섹션을 정의 하려면 태그를 사용 하 여 렌더링 하기 위해 문자를 묶습니다 :::no-loc(Razor)::: `<text>` .</span><span class="sxs-lookup"><span data-stu-id="02f70-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the :::no-loc(Razor)::: `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="02f70-171">HTML 태그로 묶이지 않은 HTML을 렌더링하려면 이 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="02f70-172">HTML 또는 태그가 없으면 :::no-loc(Razor)::: :::no-loc(Razor)::: 런타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-172">Without an HTML or :::no-loc(Razor)::: tag, a :::no-loc(Razor)::: runtime error occurs.</span></span>

<span data-ttu-id="02f70-173">`<text>` 태그는 콘텐츠를 렌더링할 때 공백을 제어하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="02f70-174">`<text>` 태그 사이의 콘텐츠만 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="02f70-175">`<text>` 태그 앞 또는 뒤에 있는 공백은 HTML 출력에 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="02f70-176">명시적 줄 전환</span><span class="sxs-lookup"><span data-stu-id="02f70-176">Explicit line transition</span></span>

<span data-ttu-id="02f70-177">코드 블록 내부의 나머지 전체 줄을 HTML로 렌더링하려면 `@:` 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="02f70-178">`@:`코드에이 없으면 :::no-loc(Razor)::: 런타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-178">Without the `@:` in the code, a :::no-loc(Razor)::: runtime error is generated.</span></span>

<span data-ttu-id="02f70-179">파일에 추가 문자를 추가 하면 `@` :::no-loc(Razor)::: 블록의 뒷부분에 있는 문에서 컴파일러 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-179">Extra `@` characters in a :::no-loc(Razor)::: file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="02f70-180">실제 오류가 보고된 오류보다 먼저 발생하기 때문에 이러한 컴파일러 오류를 이해하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="02f70-181">이 오류는 여러 암시적/명시적 식을 단일 코드 블록에 결합한 이후에 자주 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="02f70-182">제어 구조</span><span class="sxs-lookup"><span data-stu-id="02f70-182">Control structures</span></span>

<span data-ttu-id="02f70-183">제어 구조는 코드 블록의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="02f70-184">코드 블록의 모든 측면(태그로 전환, 인라인 C#)은 다음 구조에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="02f70-185">라도 `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="02f70-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="02f70-186">`@if`는 코드가 실행되는 시기를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="02f70-187">`else` 및 `else if`는 `@` 기호가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="02f70-188">다음 태그는 switch 문 사용 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="02f70-189">루프로 `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="02f70-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="02f70-190">템플릿 기반 HTML은 반복 제어 문으로 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="02f70-191">사람 목록을 렌더링하려면:</span><span class="sxs-lookup"><span data-stu-id="02f70-191">To render a list of people:</span></span>

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

<span data-ttu-id="02f70-192">다음 반복 문이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="02f70-193">복합 `@using`</span><span class="sxs-lookup"><span data-stu-id="02f70-193">Compound `@using`</span></span>

<span data-ttu-id="02f70-194">C#에서 `using` 문은 개체가 삭제되도록 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="02f70-195">에서 :::no-loc(Razor)::: 동일한 메커니즘을 사용 하 여 추가 콘텐츠를 포함 하는 HTML 도우미를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-195">In :::no-loc(Razor):::, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="02f70-196">다음 코드에서 HTML 도우미는 `@using` 문을 사용하여 `<form>` 태그를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="02f70-197">예외 처리는 C#과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="02f70-198">:::no-loc(Razor)::: 에는 lock 문을 사용 하 여 중요 한 섹션을 보호할 수 있는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-198">:::no-loc(Razor)::: has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="02f70-199">의견</span><span class="sxs-lookup"><span data-stu-id="02f70-199">Comments</span></span>

<span data-ttu-id="02f70-200">:::no-loc(Razor)::: c # 및 HTML 주석을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-200">:::no-loc(Razor)::: supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="02f70-201">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="02f70-202">:::no-loc(Razor)::: 웹 페이지를 렌더링 하기 전에 서버에서 주석을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-202">:::no-loc(Razor)::: comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="02f70-203">:::no-loc(Razor)::: 를 사용 하 여 `@*  *@` 주석을 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-203">:::no-loc(Razor)::: uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="02f70-204">다음 코드는 주석 처리되며, 따라서 서버에서 어떤 태그도 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="02f70-205">지시문</span><span class="sxs-lookup"><span data-stu-id="02f70-205">Directives</span></span>

<span data-ttu-id="02f70-206">:::no-loc(Razor)::: 지시문은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="02f70-206">:::no-loc(Razor)::: directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="02f70-207">지시문은 일반적으로 보기를 구문 분석하는 방식을 변경하거나 다른 기능을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="02f70-208">:::no-loc(Razor):::에서 뷰에 대 한 코드를 생성 하는 방법을 이해 하면 지시문이 작동 하는 방식을 보다 쉽게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-208">Understanding how :::no-loc(Razor)::: generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="02f70-209">이 코드는 다음과 비슷한 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-209">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : :::no-loc(Razor):::Page<dynamic>
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

<span data-ttu-id="02f70-210">이 문서의 뒷부분에서 [ :::no-loc(Razor)::: 뷰에 대해 생성 된 c # 클래스 검사](#inspect-the-razor-c-class-generated-for-a-view) 섹션에서이 생성 된 클래스를 보는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-210">Later in this article, the section [Inspect the :::no-loc(Razor)::: C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="02f70-211">`@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="02f70-212">다음 예제에서는 `[Authorize]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="02f70-213">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-213">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-214">`@code`블록을 사용 하면 [ :::no-loc(Razor)::: 구성](xref:blazor/components/index) 요소에서 c # 멤버 (필드, 속성 및 메서드)를 구성 요소에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-214">The `@code` block enables a [:::no-loc(Razor)::: component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="02f70-215">:::no-loc(Razor):::구성 요소의 경우 `@code` 는의 별칭이 [`@functions`](#functions) 며 권장 됩니다 `@functions` .</span><span class="sxs-lookup"><span data-stu-id="02f70-215">For :::no-loc(Razor)::: components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="02f70-216">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="02f70-217">`@functions` 지시문을 사용하면 C# 멤버(필드, 속성 및 메서드)를 생성된 클래스에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02f70-218">[ :::no-loc(Razor)::: 구성 요소](xref:blazor/components/index)에서를 `@code` 사용 `@functions` 하 여 c # 멤버를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-218">In [:::no-loc(Razor)::: components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="02f70-219">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="02f70-220">이 코드는 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="02f70-221">다음 코드는 생성 된 :::no-loc(Razor)::: c # 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-221">The following code is the generated :::no-loc(Razor)::: C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02f70-222">`@functions` 메서드는 태그가 있을 때 템플릿 메서드로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="02f70-223">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="02f70-224">`@implements` 지시문은 생성된 클래스의 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="02f70-225">다음 예제에서는 <xref:System.IDisposable.Dispose*> 메서드를 호출할 수 있도록 <xref:System.IDisposable?displayProperty=fullName>을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="02f70-226">`@inherits` 지시문은 보기에서 상속하는 클래스에 대한 완전한 제어권을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="02f70-227">다음 코드는 사용자 지정 :::no-loc(Razor)::: 페이지 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-227">The following code is a custom :::no-loc(Razor)::: page type:</span></span>

[!code-csharp[](razor/sample/Classes/Custom:::no-loc(Razor):::Page.cs)]

<span data-ttu-id="02f70-228">`CustomText`는 보기에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="02f70-229">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="02f70-230">`@model` 및 `@inherits`는 동일한 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="02f70-231">`@inherits`는 보기에서 가져오는 *_ViewImports.cshtml* 파일에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="02f70-232">다음 코드는 강력한 형식의 보기 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="02f70-233">"rick@contoso.com"이 모델에 전달되면 보기에서 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="02f70-234">`@inject`지시문을 사용 하면 페이지에서 서비스 :::no-loc(Razor)::: [컨테이너](xref:fundamentals/dependency-injection) 의 서비스를 뷰에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-234">The `@inject` directive enables the :::no-loc(Razor)::: Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="02f70-235">자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="02f70-236">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-236">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-237">`@layout`지시문은 구성 요소에 대 한 레이아웃을 지정 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-237">The `@layout` directive specifies a layout for a :::no-loc(Razor)::: component.</span></span> <span data-ttu-id="02f70-238">레이아웃 구성 요소는 코드 중복 및 불일치를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="02f70-239">자세한 내용은 <xref:blazor/layouts>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="02f70-240">*이 시나리오는 MVC 뷰와 :::no-loc(Razor)::: 페이지 (cshtml)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-240">*This scenario only applies to MVC views and :::no-loc(Razor)::: Pages (.cshtml).*</span></span>

<span data-ttu-id="02f70-241">`@model` 지시문은 보기 또는 페이지에 전달되는 모델 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="02f70-242">:::no-loc(Razor):::개별 사용자 계정을 사용 하 여 만든 ASP.NET CORE MVC 또는 페이지 앱에서 *Views/Account/Login* 은 다음 모델 선언을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-242">In an ASP.NET Core MVC or :::no-loc(Razor)::: Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="02f70-243">생성된 클래스는 `:::no-loc(Razor):::Page<dynamic>`에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-243">The class generated inherits from `:::no-loc(Razor):::Page<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : :::no-loc(Razor):::Page<LoginViewModel>
```

<span data-ttu-id="02f70-244">:::no-loc(Razor):::`Model`뷰에 전달 된 모델에 액세스 하기 위한 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-244">:::no-loc(Razor)::: exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="02f70-245">`@model` 지시문은 `Model` 속성의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="02f70-246">이 지시문은 보기가 파생되는 클래스를 생성한 `:::no-loc(Razor):::Page<T>`의 `T`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-246">The directive specifies the `T` in `:::no-loc(Razor):::Page<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="02f70-247">`@model` 지시문이 지정되지 않을 경우 `Model` 속성은 `dynamic` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="02f70-248">자세한 내용은 [강력한 형식의 모델 및 @model 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="02f70-249">`@namespace` 지시문:</span><span class="sxs-lookup"><span data-stu-id="02f70-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="02f70-250">생성 된 :::no-loc(Razor)::: 페이지, MVC 뷰 또는 구성 요소의 클래스에 대 한 네임 스페이스를 설정 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-250">Sets the namespace of the class of the generated :::no-loc(Razor)::: page, MVC view, or :::no-loc(Razor)::: component.</span></span>
* <span data-ttu-id="02f70-251">디렉터리 *_ViewImports* 트리의 가장 가까운 imports 파일 (뷰 또는 페이지) 또는 *_Imports razor* ( :::no-loc(Razor)::: 구성 요소)에서 페이지, 뷰 또는 구성 요소 클래스의 루트 파생 네임 스페이스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (:::no-loc(Razor)::: components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="02f70-252">:::no-loc(Razor):::다음 표에 표시 된 페이지 예:</span><span class="sxs-lookup"><span data-stu-id="02f70-252">For the :::no-loc(Razor)::: Pages example shown in the following table:</span></span>

* <span data-ttu-id="02f70-253">각 페이지에서는 *Pages/_ViewImports.cshtml* 을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-253">Each page imports *Pages/_ViewImports.cshtml* .</span></span>
* <span data-ttu-id="02f70-254">*Pages/_ViewImports.cshtml* 에는 `@namespace Hello.World`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="02f70-255">각 페이지에는 `Hello.World`가 네임스페이스의 루트로 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="02f70-256">호출</span><span class="sxs-lookup"><span data-stu-id="02f70-256">Page</span></span>                                        | <span data-ttu-id="02f70-257">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="02f70-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="02f70-258">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="02f70-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="02f70-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="02f70-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="02f70-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="02f70-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="02f70-261">위의 관계는 MVC 뷰 및 구성 요소에 사용 되는 가져오기 파일에 적용 :::no-loc(Razor)::: 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-261">The preceding relationships apply to import files used with MVC views and :::no-loc(Razor)::: components.</span></span>

<span data-ttu-id="02f70-262">여러 가져오기 파일에 `@namespace` 지시문이 있으면 디렉터리 트리의 페이지, 보기 또는 구성 요소에 가장 가까운 파일을 사용하여 루트 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="02f70-263">이전 예제의 *EvenMorePages* 폴더에 `@namespace Another.Planet`이 포함된 가져오기 파일이 있으면(또는 *Pages/MorePages/EvenMorePages/Page.cshtml* 파일에 `@namespace Another.Planet` 포함), 다음 표에 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="02f70-264">호출</span><span class="sxs-lookup"><span data-stu-id="02f70-264">Page</span></span>                                        | <span data-ttu-id="02f70-265">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="02f70-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="02f70-266">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="02f70-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="02f70-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="02f70-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="02f70-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="02f70-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02f70-269">`@page` 지시문은 표시되는 파일 형식에 따라 서로 다른 효과를 냅니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="02f70-270">지시문:</span><span class="sxs-lookup"><span data-stu-id="02f70-270">The directive:</span></span>

* <span data-ttu-id="02f70-271">*. Cshtml* 파일은 파일이 페이지 임을 나타냅니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-271">In a *.cshtml* file indicates that the file is a :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="02f70-272">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes) 및 <xref:razor-pages/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="02f70-273">:::no-loc(Razor):::구성 요소가 요청을 직접 처리 하도록 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-273">Specifies that a :::no-loc(Razor)::: component should handle requests directly.</span></span> <span data-ttu-id="02f70-274">자세한 내용은 <xref:blazor/fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02f70-275">`@page` *Cshtml* 파일의 첫 번째 줄에 있는 지시문은 파일이 페이지 임을 나타냅니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="02f70-276">자세한 내용은 <xref:razor-pages/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="02f70-277">*이 시나리오는 MVC 뷰와 :::no-loc(Razor)::: 페이지 (cshtml)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-277">*This scenario only applies to MVC views and :::no-loc(Razor)::: Pages (.cshtml).*</span></span>

<span data-ttu-id="02f70-278">`@section`지시문은 [MVC 및 :::no-loc(Razor)::: 페이지 레이아웃](xref:mvc/views/layout) 과 함께 사용 되어 뷰 또는 페이지에서 HTML 페이지의 다른 부분에 콘텐츠를 렌더링할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-278">The `@section` directive is used in conjunction with [MVC and :::no-loc(Razor)::: Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="02f70-279">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-279">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="02f70-280">`@using` 지시문은 C# `using` 지시문을 생성된 보기에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-280">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02f70-281">[ :::no-loc(Razor)::: 구성 요소](xref:blazor/components/index)에서는 `@using` 범위 내에 있는 구성 요소를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-281">In [:::no-loc(Razor)::: components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="02f70-282">지시문 특성</span><span class="sxs-lookup"><span data-stu-id="02f70-282">Directive attributes</span></span>

<span data-ttu-id="02f70-283">:::no-loc(Razor)::: 지시문 특성은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="02f70-283">:::no-loc(Razor)::: directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="02f70-284">지시문 특성은 일반적으로 요소가 구문 분석 되거나 다른 기능을 사용 하도록 설정 하는 방식을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-284">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="02f70-285">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-285">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-286">`@attributes`를 사용하면 구성 요소가 선언되지 않은 특성을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-286">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="02f70-287">자세한 내용은 <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-287">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="02f70-288">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-288">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-289">구성 요소의 데이터 바인딩은 `@bind` 특성을 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-289">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="02f70-290">자세한 내용은 <xref:blazor/components/data-binding>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-290">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="02f70-291">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-291">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-292">:::no-loc(Razor)::: 구성 요소에 대 한 이벤트 처리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-292">:::no-loc(Razor)::: provides event handling features for components.</span></span> <span data-ttu-id="02f70-293">자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-293">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="02f70-294">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-294">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-295">이벤트의 기본 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-295">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="02f70-296">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-296">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-297">이벤트의 이벤트 전파를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-297">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="02f70-298">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-298">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-299">`@key` 지시어 특성을 사용하면 구성 요소 diff 알고리즘이 키의 값에 따라 요소 또는 구성 요소를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-299">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="02f70-300">자세한 내용은 <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-300">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="02f70-301">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-301">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-302">구성 요소 참조(`@ref`)에서는 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-302">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="02f70-303">자세한 내용은 <xref:blazor/components/index#capture-references-to-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-303">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="02f70-304">*이 시나리오는 :::no-loc(Razor)::: 구성 요소 (razor)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-304">*This scenario only applies to :::no-loc(Razor)::: components (.razor).*</span></span>

<span data-ttu-id="02f70-305">`@typeparam` 지시문은 생성된 구성 요소 클래스에 대한 제네릭 형식 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-305">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="02f70-306">자세한 내용은 <xref:blazor/components/templated-components#generic-typed-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02f70-306">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="02f70-307">템플릿 :::no-loc(Razor)::: 대리자</span><span class="sxs-lookup"><span data-stu-id="02f70-307">Templated :::no-loc(Razor)::: delegates</span></span>

<span data-ttu-id="02f70-308">:::no-loc(Razor)::: 템플릿을 사용 하면 다음 형식의 UI 코드 조각을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-308">:::no-loc(Razor)::: templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="02f70-309">다음 예제에서는 템플릿 대리자를로 지정 하는 방법을 보여 줍니다 :::no-loc(Razor)::: <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="02f70-309">The following example illustrates how to specify a templated :::no-loc(Razor)::: delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="02f70-310">[dynamic 형식](/dotnet/csharp/programming-guide/types/using-type-dynamic)은 대리자에서 캡슐화하는 메서드의 매개 변수로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-310">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="02f70-311">[object 형식](/dotnet/csharp/language-reference/keywords/object)은 대리자의 반환 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-311">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="02f70-312">템플릿은 `Name` 속성이 있는 `Pet`의 <xref:System.Collections.Generic.List%601>에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-312">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="02f70-313">템플릿은 `foreach` 문에서 제공하는 `pets`에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-313">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="02f70-314">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="02f70-314">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="02f70-315">메서드에 대 한 인수로 인라인 템플릿을 제공할 수도 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-315">You can also supply an inline :::no-loc(Razor)::: template as an argument to a method.</span></span> <span data-ttu-id="02f70-316">다음 예제에서 `Repeat` 메서드는 :::no-loc(Razor)::: 템플릿을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-316">In the following example, the `Repeat` method receives a :::no-loc(Razor)::: template.</span></span> <span data-ttu-id="02f70-317">이 메서드는 템플릿을 사용하여 목록에서 제공된 항목의 반복이 포함된 HTML 콘텐츠를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-317">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="02f70-318">이전 예제의 애완 동물 목록을 사용하여 `Repeat` 메서드는 다음 항목과 함께 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-318">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="02f70-319"><xref:System.Collections.Generic.List%601>의 `Pet`입니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-319"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="02f70-320">각 애완 동물에 대한 반복 횟수</span><span class="sxs-lookup"><span data-stu-id="02f70-320">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="02f70-321">순서가 지정되지 않은 목록의 목록 항목에 사용할 인라인 템플릿</span><span class="sxs-lookup"><span data-stu-id="02f70-321">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="02f70-322">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="02f70-322">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="02f70-323">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="02f70-323">Tag Helpers</span></span>

<span data-ttu-id="02f70-324">*이 시나리오는 MVC 뷰와 :::no-loc(Razor)::: 페이지 (cshtml)에만 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="02f70-324">*This scenario only applies to MVC views and :::no-loc(Razor)::: Pages (.cshtml).*</span></span>

<span data-ttu-id="02f70-325">[태그 도우미](xref:mvc/views/tag-helpers/intro)와 관련된 세 가지 지시문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-325">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="02f70-326">지시문</span><span class="sxs-lookup"><span data-stu-id="02f70-326">Directive</span></span> | <span data-ttu-id="02f70-327">함수</span><span class="sxs-lookup"><span data-stu-id="02f70-327">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="02f70-328">보기에 태그 도우미를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-328">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="02f70-329">보기에서 이전에 추가된 태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-329">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="02f70-330">태그 도우미를 지원하고 태그 도우미 사용을 명시적으로 만들어주는 태그 접두사를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-330">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="02f70-331">:::no-loc(Razor)::: 예약 된 키워드</span><span class="sxs-lookup"><span data-stu-id="02f70-331">:::no-loc(Razor)::: reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="02f70-332">:::no-loc(Razor)::: 어</span><span class="sxs-lookup"><span data-stu-id="02f70-332">:::no-loc(Razor)::: keywords</span></span>

* <span data-ttu-id="02f70-333">`page` (ASP.NET Core 2.1 이상 필요)</span><span class="sxs-lookup"><span data-stu-id="02f70-333">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="02f70-334">`helper` (현재 ASP.NET Core에서 지원 되지 않음)</span><span class="sxs-lookup"><span data-stu-id="02f70-334">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="02f70-335">:::no-loc(Razor)::: 키워드는로 이스케이프 됩니다 `@(:::no-loc(Razor)::: Keyword)` (예: `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="02f70-335">:::no-loc(Razor)::: keywords are escaped with `@(:::no-loc(Razor)::: Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="02f70-336">C # :::no-loc(Razor)::: 키워드</span><span class="sxs-lookup"><span data-stu-id="02f70-336">C# :::no-loc(Razor)::: keywords</span></span>

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

<span data-ttu-id="02f70-337">C # :::no-loc(Razor)::: 키워드는를 사용 하 여 이중 이스케이프 되어야 합니다 `@(@C# :::no-loc(Razor)::: Keyword)` (예: `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="02f70-337">C# :::no-loc(Razor)::: keywords must be double-escaped with `@(@C# :::no-loc(Razor)::: Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="02f70-338">첫 번째는 `@` 파서를 이스케이프 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-338">The first `@` escapes the :::no-loc(Razor)::: parser.</span></span> <span data-ttu-id="02f70-339">두 번째 `@`은 C# 파서를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-339">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="02f70-340">에서 사용 되지 않는 예약 된 키워드 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="02f70-340">Reserved keywords not used by :::no-loc(Razor):::</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="02f70-341">:::no-loc(Razor):::뷰에 대해 생성 된 c # 클래스를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-341">Inspect the :::no-loc(Razor)::: C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="02f70-342">.NET Core SDK 2.1 이상에서 [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk) 는 파일의 컴파일을 처리 :::no-loc(Razor)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-342">With .NET Core SDK 2.1 or later, the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk) handles compilation of :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="02f70-343">프로젝트를 빌드할 때 SDK는 :::no-loc(Razor)::: 프로젝트 루트에서 *>/<target_framework_moniker>/ :::no-loc(Razor)::: 디렉터리 build_configuration obj/<* 를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-343">When building a project, the :::no-loc(Razor)::: SDK generates an *obj/<build_configuration>/<target_framework_moniker>/:::no-loc(Razor):::* directory in the project root.</span></span> <span data-ttu-id="02f70-344">디렉터리 내의 디렉터리 구조는 *:::no-loc(Razor):::* 프로젝트의 디렉터리 구조를 미러링합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-344">The directory structure within the *:::no-loc(Razor):::* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="02f70-345">.NET Core 2.1를 대상으로 하는 ASP.NET Core 2.1 페이지 프로젝트에서 다음 디렉터리 구조를 고려 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="02f70-345">Consider the following directory structure in an ASP.NET Core 2.1 :::no-loc(Razor)::: Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="02f70-346">*Debug* 구성에 프로젝트를 빌드하면 다음 *obj* 디렉터리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-346">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       :::no-loc(Razor):::/
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

<span data-ttu-id="02f70-347">*Pages/Index. cshtml* 에 대해 생성 된 클래스를 보려면 *obj/Debug/Netcoreapp1.0 2.1/ :::no-loc(Razor)::: /Pages/Index.g.cshtml.cs* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-347">To view the generated class for *Pages/Index.cshtml* , open *obj/Debug/netcoreapp2.1/:::no-loc(Razor):::/Pages/Index.g.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="02f70-348">ASP.NET Core MVC 프로젝트에 다음 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-348">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="02f70-349">`Startup.ConfigureServices`에서 MVC가 추가한 `:::no-loc(Razor):::TemplateEngine`을 `CustomTemplateEngine` 클래스로 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-349">In `Startup.ConfigureServices`, override the `:::no-loc(Razor):::TemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="02f70-350">`CustomTemplateEngine`의 `return csharpDocument;` 문에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-350">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="02f70-351">중단점에서 프로그램 실행이 중지되면 `generatedCode`의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-351">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="02f70-353">보기 조회 및 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="02f70-353">View lookups and case sensitivity</span></span>

<span data-ttu-id="02f70-354">:::no-loc(Razor):::뷰 엔진은 뷰에 대해 대/소문자를 구분 하는 조회를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-354">The :::no-loc(Razor)::: view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="02f70-355">그러나 실제 조회는 기본 파일 시스템에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-355">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="02f70-356">파일 기반 원본:</span><span class="sxs-lookup"><span data-stu-id="02f70-356">File based source:</span></span>
  * <span data-ttu-id="02f70-357">파일 시스템이 대/소문자를 구문하지 않은 운영 체제(예: Windows)에서는 실제 파일 공급자 조회에서 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-357">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="02f70-358">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml* , */Views/home/test.cshtml* 및 기타 대/소문자 구분 변형과 일치하는 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-358">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml* , */Views/home/test.cshtml* , and any other casing variant.</span></span>
  * <span data-ttu-id="02f70-359">대/소문자를 구분하는 파일 시스템(예: Linux, OSX 및 `EmbeddedFileProvider`를 사용하는 파일 시스템)에서는 조회 시 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-359">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="02f70-360">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml* 과 정확히 일치하는 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-360">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml* .</span></span>
* <span data-ttu-id="02f70-361">미리 컴파일된 보기: ASP.NET 2.0 이상을 사용하는 경우 모든 운영 체제에서 미리 컴파일된 보기 조회 시 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-361">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="02f70-362">이 동작은 Windows의 물리적 파일 공급자 동작과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-362">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="02f70-363">미리 컴파일된 두 보기의 대소문자만 다른 경우 조회 결과가 불명확합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-363">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="02f70-364">개발자는 파일 및 디렉터리 이름의 대/소문자를 다음의 대/소문자와 매칭하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-364">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="02f70-365">영역, 컨트롤러 및 작업 이름.</span><span class="sxs-lookup"><span data-stu-id="02f70-365">Area, controller, and action names.</span></span>
* <span data-ttu-id="02f70-366">:::no-loc(Razor)::: 마주보.</span><span class="sxs-lookup"><span data-stu-id="02f70-366">:::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="02f70-367">대/소문자를 일치시키면 배포 시 기본 파일 시스템에 관계 없이 해당 보기를 잘 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-367">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02f70-368">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="02f70-368">Additional resources</span></span>

<span data-ttu-id="02f70-369">을 [사용 하는 :::no-loc(Razor)::: ASP.NET 웹 프로그래밍 소개 구문은](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) 구문을 사용한 프로그래밍의 많은 예제를 제공 :::no-loc(Razor)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="02f70-369">[Introduction to ASP.NET Web Programming Using the :::no-loc(Razor)::: Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with :::no-loc(Razor)::: syntax.</span></span>
