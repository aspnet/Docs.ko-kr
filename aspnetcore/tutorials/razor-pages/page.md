---
title: '3부: 스캐폴드된 Razor Pages'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 3부입니다.
ms.author: riande
ms.date: 09/25/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: d655be26a794f87a0be07046ae1d6415256d592c
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417632"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="7ec6a-103">3부. ASP.NET Core의 스캐폴드된 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7ec6a-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="7ec6a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ec6a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ec6a-105">이 자습서에서는 [이전 자습서](xref:tutorials/razor-pages/model)에서 스캐폴딩을 통해 만든 Razor 페이지를 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7ec6a-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="7ec6a-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="7ec6a-108">Create, Delete, 세부 정보, 편집 페이지</span><span class="sxs-lookup"><span data-stu-id="7ec6a-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="7ec6a-109">*Pages/Movies/Index.cshtml.cs* 페이지 모델을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="7ec6a-110">Razor Pages는 `PageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="7ec6a-111">일반적으로 `PageModel` 파생 클래스의 이름은 `<PageName>Model`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="7ec6a-112">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 `RazorPagesMovieContext`를 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7ec6a-113">Entity Framework로 비동기 프로그래밍에 대한 자세한 내용은 [비동기 코드](xref:data/ef-rp/intro#asynchronous-code)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="7ec6a-114">페이지에 대한 요청을 만들면 `OnGetAsync` 메서드가 Razor Page에 동영상 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="7ec6a-115">Razor 페이지에서 `OnGetAsync` 또는 `OnGet`을 호출하여 페이지 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="7ec6a-116">이 경우 `OnGetAsync`는 동영상 목록을 가져와 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="7ec6a-117">`OnGet`이 `void`를 반환하거나 `OnGetAsync`가 `Task`를 반환하면 return 문이 사용되지 않은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="7ec6a-118">개인 정보 페이지를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="7ec6a-119">반환 형식이 `IActionResult` 또는 `Task<IActionResult>`이면 반환 문을 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="7ec6a-120">*Pages/Movies/Create.cshtml.cs* `OnPostAsync` 메서드를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="7ec6a-121">*Pages/Movies/Index.cshtml* Razor 페이지를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="7ec6a-122">Razor는 HTML에서 C# 또는 Razor 관련 태그로 전환될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="7ec6a-123">`@` 기호 뒤에 [Razor 예약 키워드](xref:mvc/views/razor#razor-reserved-keywords)가 사용되면 이 기호는 Razor 관련 태그로 전환됩니다. 이외의 경우에는 C#으로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="7ec6a-124">@page 지시문</span><span class="sxs-lookup"><span data-stu-id="7ec6a-124">The @page directive</span></span>

<span data-ttu-id="7ec6a-125">`@page` Razor 지시문은 파일을 MVC 작업으로 만들고, 이것은 요청을 처리할 수 있음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="7ec6a-126">`@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="7ec6a-127">`@page` 및 `@model`은 Razor 관련 태그로 전환되는 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="7ec6a-128">자세한 내용은 [Razor 구문](xref:mvc/views/razor#razor-syntax)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="7ec6a-129">@model 지시문</span><span class="sxs-lookup"><span data-stu-id="7ec6a-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="7ec6a-130">`@model` 지시문은 Razor Page에 전달되는 모델 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="7ec6a-131">위의 예제에서 `@model` 줄은 Razor Page에서 `PageModel` 파생 클래스를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="7ec6a-132">모델은 페이지에서 `@Html.DisplayNameFor` 및 `@Html.DisplayFor` [HTML 도우미](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="7ec6a-133">다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="7ec6a-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="7ec6a-135">람다 식은 계산되는 것이 아니라 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="7ec6a-136">즉, `model`, `model.Movie` 또는 `model.Movie[0]`가 `null`이거나 비어 있을 경우 액세스 위반이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="7ec6a-137">람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)` 사용) 모델의 속성 값이 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="7ec6a-138">레이아웃 페이지</span><span class="sxs-lookup"><span data-stu-id="7ec6a-138">The layout page</span></span>

<span data-ttu-id="7ec6a-139">메뉴 링크( **RazorPagesMovie**, **홈** 및 **개인 정보**)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="7ec6a-140">각 페이지는 동일한 메뉴 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="7ec6a-141">메뉴 레이아웃은 *Pages/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7ec6a-142">*Pages/Shared/_Layout.cshtml* 파일을 열고 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7ec6a-143">[레이아웃](xref:mvc/views/layout) 템플릿을 사용하여 HTML 컨테이너 레이아웃을 다음과 같이 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="7ec6a-144">한 위치에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-144">Specified in one place.</span></span>
* <span data-ttu-id="7ec6a-145">사이트의 여러 페이지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="7ec6a-146">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="7ec6a-147">`RenderBody`는 사용자가 만드는 모든 페이지 특정 보기가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑* 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="7ec6a-148">예를 들어, **개인 정보** 링크를 선택하는 경우 *Pages/Privacy.cshtml* 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="7ec6a-149">ViewData 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="7ec6a-149">ViewData and layout</span></span>

<span data-ttu-id="7ec6a-150">*Pages/Movies/Index.cshtml* 파일의 다음 태그를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="7ec6a-151">강조 표시된 이전 태그는 C#으로 전환되는 Razor의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="7ec6a-152">`{` 및 `}` 문자로 C# 코드 블록을 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="7ec6a-153">`PageModel` 기본 클래스에는 데이터를 뷰에 전달하는 데 사용할 수 있는 `ViewData` 사전 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="7ec6a-154">**키 값** 패턴을 사용하여 개체가 `ViewData` 사전에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="7ec6a-155">이전 샘플에서는 `Title` 속성이 `ViewData` 사전에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="7ec6a-156">`Title` 속성은 Pages/Shared/_Layout.cshtml 파일에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="7ec6a-157">다음 태그는 *_Layout.cshtml* 파일의 처음 몇 줄을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="7ec6a-158">`@*Markup removed for brevity.*@` 줄은 Razor 주석입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="7ec6a-159">HTML 주석 `<!-- -->`과 달리 Razor 주석은 클라이언트에 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="7ec6a-160">자세한 내용은 [MDN 웹 문서: HTML 시작](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="7ec6a-161">레이아웃 업데이트</span><span class="sxs-lookup"><span data-stu-id="7ec6a-161">Update the layout</span></span>

1. <span data-ttu-id="7ec6a-162">*Pages/Shared/_Layout.cshtml* 파일의 `<title>` 요소를 변경하여 **RazorPagesMovie** 대신 **Movie** 를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="7ec6a-163">*Pages/Shared/_Layout.cshtml* 파일에서 다음 앵커 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="7ec6a-164">이전 요소를 다음 태그로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="7ec6a-165">이전 앵커 요소는 [태그 도우미](xref:mvc/views/tag-helpers/intro)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7ec6a-166">이 경우에는 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="7ec6a-167">`asp-page="/Movies/Index"` 태그 도우미 특성 및 값으로 `/Movies/Index` Razor Page의 링크를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="7ec6a-168">`asp-area` 특성 값이 비어 있으므로 영역은 링크에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="7ec6a-169">자세한 내용은 [영역](xref:mvc/controllers/areas)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="7ec6a-170">변경 내용을 저장하고 **RpMovie** 링크를 선택하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="7ec6a-171">문제가 있는 경우 GitHub에서 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="7ec6a-172">**홈**, **RpMovie**, **Create** , **편집**, **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="7ec6a-173">각 페이지에서 설정되는 제목은 브라우저 탭에서 확인할 수 있습니다. 페이지의 책갈피를 지정하면 제목이 책갈피에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="7ec6a-174">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7ec6a-175">소수점으로 쉼표(",")를 사용하는 비영어 로캘 및 비미국 영어 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화하는 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="7ec6a-176">소수점 추가에 대한 지침은 이 [GitHub 문제 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="7ec6a-177">`Layout` 속성은 *Pages/_ViewStart.cshtml* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="7ec6a-178">이전 태그는 *Pages* 폴더 아래에 있는 모든 Razor 파일에 대한 레이아웃 파일을 *Pages/Shared/_Layout.cshtml* 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="7ec6a-179">자세한 내용은 [레이아웃](xref:razor-pages/index#layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="7ec6a-180">Create 페이지 모델</span><span class="sxs-lookup"><span data-stu-id="7ec6a-180">The Create page model</span></span>

<span data-ttu-id="7ec6a-181">*Pages/Movies/Create.cshtml.cs* 페이지 모델을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="7ec6a-182">`OnGet` 메서드는 페이지에 필요한 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="7ec6a-183">Create 페이지에는 초기화할 상태가 없으므로 `Page`가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="7ec6a-184">자습서의 뒷부분에서 상태를 초기화하는 `OnGet`의 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="7ec6a-185">`Page` 메서드는 *Create.cshtml* 페이지를 렌더링하는 `PageResult` 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="7ec6a-186">`Movie` 속성은 [BindProperty](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 특성을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 옵트인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7ec6a-187">Create 양식이 양식 값을 게시하면 ASP.NET Core 런타임이 게시된 값을 `Movie` 모델에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="7ec6a-188">페이지에 폼 데이터가 게시되면 `OnPostAsync` 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="7ec6a-189">모델 오류가 있는 경우 폼과 게시된 모든 폼 데이터가 다시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="7ec6a-190">대부분의 모델 오류는 폼이 게시되기 전에 클라이언트 쪽에서 catch할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="7ec6a-191">예를 들어 데이터로 변환될 수 없는 날짜 필드에 대한 값을 게시하는 모델 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="7ec6a-192">클라이언트 쪽 유효성 검사 및 모델 유효성 검사는 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="7ec6a-193">모델 오류가 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="7ec6a-193">If there are no model errors:</span></span>

* <span data-ttu-id="7ec6a-194">데이터가 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-194">The data is saved.</span></span>
* <span data-ttu-id="7ec6a-195">브라우저가 Index 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-195">The browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="7ec6a-196">Razor Page Create</span><span class="sxs-lookup"><span data-stu-id="7ec6a-196">The Create Razor Page</span></span>

<span data-ttu-id="7ec6a-197">*Pages/Movies/Create.cshtml* Razor 페이지 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="7ec6a-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ec6a-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ec6a-199">Visual Studio에서는 다음 태그를 태그 도우미에 사용되는 독특한 굵은 글꼴로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml 페이지의 VS17 보기](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7ec6a-201">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ec6a-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7ec6a-202">다음 태그 도우미는 위의 태그에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="7ec6a-203">`<form method="post">` 요소는 [폼 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="7ec6a-204">폼 태그 도우미에는 [위조 방지 토큰](xref:security/anti-request-forgery)이 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="7ec6a-205">스캐폴딩 엔진은 ID를 제외하고 다음과 비슷한 모델에서 필드마다 Razor 태그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="7ec6a-206">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` 및 `<span asp-validation-for`) 는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="7ec6a-207">유효성 검사는 이 시리즈의 뒷부분에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="7ec6a-208">[레이블 태그 도우미](xref:mvc/views/working-with-forms#the-label-tag-helper)(`<label asp-for="Movie.Title" class="control-label"></label>`)는 `Title` 속성에 대한 레이블 캡션 및 `[for]` 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="7ec6a-209">[입력 태그 도우미](xref:mvc/views/working-with-forms)(`<input asp-for="Movie.Title" class="form-control">`)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="7ec6a-210">태그 도우미(예: `<form method="post">`)에 대한 자세한 내용은 [ASP.NET Core의 태그 도우미](xref:mvc/views/tag-helpers/intro)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ec6a-211">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ec6a-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7ec6a-212">[이전: 모델 추가](xref:tutorials/razor-pages/model)
> [다음: 데이터베이스 작업](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="7ec6a-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="7ec6a-213">Create, Delete, 세부 정보, 편집 페이지</span><span class="sxs-lookup"><span data-stu-id="7ec6a-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="7ec6a-214">*Pages/Movies/Index.cshtml.cs* 페이지 모델을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="7ec6a-215">Razor Pages는 `PageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="7ec6a-216">일반적으로 `PageModel` 파생 클래스의 이름은 `<PageName>Model`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="7ec6a-217">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 `RazorPagesMovieContext`를 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="7ec6a-218">스캐폴드된 페이지는 이 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="7ec6a-219">Entity Framework로 비동기 프로그래밍에 대한 자세한 내용은 [비동기 코드](xref:data/ef-rp/intro#asynchronous-code)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="7ec6a-220">페이지에 대한 요청을 만들면 `OnGetAsync` 메서드가 Razor Page에 동영상 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="7ec6a-221">`OnGetAsync` 또는 `OnGet`은 페이지 상태를 초기화하기 위해 Razor Page에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="7ec6a-222">이 경우 `OnGetAsync`는 동영상 목록을 가져와 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="7ec6a-223">`OnGet`에서 `void`를 반환하거나 `OnGetAsync`에서 `Task`를 반환하면 반환 메서드가 사용되지 않은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="7ec6a-224">반환 형식이 `IActionResult` 또는 `Task<IActionResult>`이면 반환 문을 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="7ec6a-225">*Pages/Movies/Create.cshtml.cs* `OnPostAsync` 메서드를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="7ec6a-226">*Pages/Movies/Index.cshtml* Razor 페이지를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="7ec6a-227">Razor는 HTML에서 C# 또는 Razor 관련 태그로 전환될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="7ec6a-228">`@` 기호 뒤에 [Razor 예약 키워드](xref:mvc/views/razor#razor-reserved-keywords)가 사용되면 이 기호는 Razor 관련 태그로 전환됩니다. 이외의 경우에는 C#으로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="7ec6a-229">`@page` Razor 지시문은 파일을 MVC 작업으로 만들고, 이것은 요청을 처리할 수 있음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="7ec6a-230">`@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="7ec6a-231">`@page`는 Razor 관련 태그로 전환되는 하나의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="7ec6a-232">자세한 내용은 [Razor 구문](xref:mvc/views/razor#razor-syntax)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="7ec6a-233">@model 지시문</span><span class="sxs-lookup"><span data-stu-id="7ec6a-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="7ec6a-234">`@model` 지시문은 Razor Page에 전달되는 모델 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="7ec6a-235">위의 예제에서 `@model` 줄은 Razor Page에서 `PageModel` 파생 클래스를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="7ec6a-236">모델은 페이지에서 `@Html.DisplayNameFor` 및 `@Html.DisplayFor` [HTML 도우미](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="7ec6a-237">HTML 도우미</span><span class="sxs-lookup"><span data-stu-id="7ec6a-237">HTML Helpers</span></span>

<span data-ttu-id="7ec6a-238">다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="7ec6a-239"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="7ec6a-240">람다 식은 계산되는 것이 아니라 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="7ec6a-241">즉, `model`, `model.Movie` 또는 `model.Movie[0]`가 `null`이거나 비어 있을 경우 액세스 위반이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="7ec6a-242">람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)` 사용) 모델의 속성 값이 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="7ec6a-243">레이아웃 페이지</span><span class="sxs-lookup"><span data-stu-id="7ec6a-243">The layout page</span></span>

<span data-ttu-id="7ec6a-244">메뉴 링크( **RazorPagesMovie**, **홈** 및 **개인 정보**)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="7ec6a-245">각 페이지는 동일한 메뉴 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="7ec6a-246">메뉴 레이아웃은 *Pages/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7ec6a-247">*Pages/Shared/_Layout.cshtml* 파일을 열고 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7ec6a-248">[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트의 여러 페이지에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="7ec6a-249">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="7ec6a-250">`RenderBody`는 사용자가 만드는 모든 페이지 특정 보기가 표시되는 자리 표시자이며 레이아웃 페이지에서 ‘래핑됩니다’.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="7ec6a-251">예를 들어 **개인 정보** 링크를 선택하는 경우 **Pages/Privacy.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="7ec6a-252">ViewData 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="7ec6a-252">ViewData and layout</span></span>

<span data-ttu-id="7ec6a-253">*Pages/Movies/Index.cshtml* 파일의 다음 코드를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="7ec6a-254">이전 강조 표시된 코드는 C#으로 전환되는 Razor의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="7ec6a-255">`{` 및 `}` 문자로 C# 코드 블록을 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="7ec6a-256">`PageModel` 기본 클래스에는 뷰에 전달할 데이터를 추가하는 데 사용될 수 있는 `ViewData` 사전 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="7ec6a-257">키/쌍 패턴을 사용하여 개체를 `ViewData` 사전에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="7ec6a-258">이전 샘플에서는 `Title` 속성이 `ViewData` 사전에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="7ec6a-259">`Title` 속성은 *Pages/Shared/_Layout.cshtml* 파일에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="7ec6a-260">다음 태그는 *_Layout.cshtml* 파일의 처음 몇 줄을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="7ec6a-261">`@*Markup removed for brevity.*@` 줄은 Razor 주석입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="7ec6a-262">HTML 주석 `<!-- -->`과 달리 Razor 주석은 클라이언트에 전송되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="7ec6a-263">자세한 내용은 [MDN 웹 문서: HTML 시작](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="7ec6a-264">레이아웃 업데이트</span><span class="sxs-lookup"><span data-stu-id="7ec6a-264">Update the layout</span></span>

<span data-ttu-id="7ec6a-265">*Pages/Shared/_Layout.cshtml* 파일의 `<title>` 요소를 변경하여 **RazorPagesMovie** 대신 **Movie** 를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="7ec6a-266">*Pages/Shared/_Layout.cshtml* 파일에서 다음 앵커 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="7ec6a-267">이전 요소를 다음 태그로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="7ec6a-268">이전 앵커 요소는 [태그 도우미](xref:mvc/views/tag-helpers/intro)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7ec6a-269">이 경우에는 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="7ec6a-270">`asp-page="/Movies/Index"` 태그 도우미 특성 및 값으로 `/Movies/Index` Razor Page의 링크를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="7ec6a-271">`asp-area` 특성 값이 비어 있으므로 영역은 링크에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="7ec6a-272">자세한 내용은 [영역](xref:mvc/controllers/areas)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="7ec6a-273">변경 내용을 저장하고 **RpMovie** 링크를 클릭하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="7ec6a-274">문제가 있는 경우 GitHub에서 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="7ec6a-275">다른 링크(**홈**, **RpMovie**, **Create** , **편집**, **Delete** )를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="7ec6a-276">각 페이지에서 설정되는 제목은 브라우저 탭에서 확인할 수 있습니다. 페이지의 책갈피를 지정하면 제목이 책갈피에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="7ec6a-277">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7ec6a-278">소수점으로 쉼표(",")를 사용하는 비영어 로캘 및 비미국 영어 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화하는 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="7ec6a-279">소수점 추가에 대한 지침은 이 [GitHub 문제 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="7ec6a-280">`Layout` 속성은 *Pages/_ViewStart.cshtml* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="7ec6a-281">이전 태그는 *Pages* 폴더 아래에 있는 모든 Razor 파일에 대한 레이아웃 파일을 *Pages/Shared/_Layout.cshtml* 로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="7ec6a-282">자세한 내용은 [레이아웃](xref:razor-pages/index#layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="7ec6a-283">페이지 모델 Create</span><span class="sxs-lookup"><span data-stu-id="7ec6a-283">The Create page model</span></span>

<span data-ttu-id="7ec6a-284">*Pages/Movies/Create.cshtml.cs* 페이지 모델을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="7ec6a-285">`OnGet` 메서드는 페이지에 필요한 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="7ec6a-286">Create 페이지에는 초기화할 상태가 없으므로 `Page`가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="7ec6a-287">이 자습서의 뒷부분에서 `OnGet` 메서드 초기화 상태를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="7ec6a-288">`Page` 메서드는 *Create.cshtml* 페이지를 렌더링하는 `PageResult` 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="7ec6a-289">`Movie` 속성은 [BindProperty]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> 특성을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 옵트인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7ec6a-290">Create 양식이 양식 값을 게시하면 ASP.NET Core 런타임이 게시된 값을 `Movie` 모델에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="7ec6a-291">페이지에 폼 데이터가 게시되면 `OnPostAsync` 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="7ec6a-292">모델 오류가 있는 경우 폼과 게시된 모든 폼 데이터가 다시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="7ec6a-293">대부분의 모델 오류는 폼이 게시되기 전에 클라이언트 쪽에서 catch할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="7ec6a-294">예를 들어 데이터로 변환될 수 없는 날짜 필드에 대한 값을 게시하는 모델 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="7ec6a-295">클라이언트 쪽 유효성 검사 및 모델 유효성 검사는 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="7ec6a-296">모델 오류가 없는 경우 데이터가 저장되고 브라우저가 Index 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="7ec6a-297">Razor Page Create</span><span class="sxs-lookup"><span data-stu-id="7ec6a-297">The Create Razor Page</span></span>

<span data-ttu-id="7ec6a-298">*Pages/Movies/Create.cshtml* Razor 페이지 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="7ec6a-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ec6a-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ec6a-300">Visual Studio에서는 `<form method="post">` 태그를 태그 도우미에 사용되는 독특한 굵은 글꼴로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml 페이지의 VS17 보기](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7ec6a-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ec6a-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7ec6a-303">태그 도우미(예: `<form method="post">`)에 대한 자세한 내용은 [ASP.NET Core의 태그 도우미](xref:mvc/views/tag-helpers/intro)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7ec6a-304">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ec6a-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7ec6a-305">Mac용 Visual Studio에서는 `<form method="post">` 태그를 태그 도우미에 사용되는 독특한 굵은 글꼴로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="7ec6a-306">`<form method="post">` 요소는 [폼 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="7ec6a-307">폼 태그 도우미에는 [위조 방지 토큰](xref:security/anti-request-forgery)이 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="7ec6a-308">스캐폴딩 엔진은 ID를 제외하고 다음과 비슷한 모델에서 필드마다 Razor 태그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="7ec6a-309">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` 및 `<span asp-validation-for`) 는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="7ec6a-310">유효성 검사는 이 시리즈의 뒷부분에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="7ec6a-311">[레이블 태그 도우미](xref:mvc/views/working-with-forms#the-label-tag-helper)(`<label asp-for="Movie.Title" class="control-label"></label>`)는 `Title` 속성에 대한 레이블 캡션 및 `[for]` 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="7ec6a-312">[입력 태그 도우미](xref:mvc/views/working-with-forms)(`<input asp-for="Movie.Title" class="form-control">`)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ec6a-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ec6a-313">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ec6a-313">Additional resources</span></span>

* [<span data-ttu-id="7ec6a-314">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="7ec6a-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="7ec6a-315">[이전: 모델 추가](xref:tutorials/razor-pages/model)
> [다음: 데이터베이스 작업](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="7ec6a-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
