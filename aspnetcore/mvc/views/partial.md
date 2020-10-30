---
title: ASP.NET Core의 부분 보기
author: ardalis
description: 부분 보기를 사용하여 큰 태그 파일을 나누고 ASP.NET Core 앱의 웹 페이지에서 공통 태그의 중복을 줄이는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 01fb87205f7855f0995cbcd135b6b01f15835e3b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060601"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="7c34f-103">ASP.NET Core의 부분 보기</span><span class="sxs-lookup"><span data-stu-id="7c34f-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="7c34f-104">작성자: [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="7c34f-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="7c34f-105">부분 뷰는 [:::no-loc(Razor):::](xref:mvc/views/razor) *.cshtml* [`@page`](xref:mvc/views/razor#page) 다른 태그 파일의 렌더링 된 출력 *내에서* HTML 출력을 렌더링 하는 지시문이 없는 태그 파일 (cshtml)입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-105">A partial view is a [:::no-loc(Razor):::](xref:mvc/views/razor) markup file ( *.cshtml* ) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c34f-106">용어 *부분 보기* 는 태그 파일을 *뷰* 라고 하는 MVC 앱 이나 :::no-loc(Razor)::: 태그 파일을 *페이지* 라고 하는 pages 앱을 개발할 때 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views* , or a :::no-loc(Razor)::: Pages app, where markup files are called *pages* .</span></span> <span data-ttu-id="7c34f-107">이 항목에서는 일반적으로 MVC 뷰 및 :::no-loc(Razor)::: 페이지 페이지를 *태그 파일로* 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-107">This topic generically refers to MVC views and :::no-loc(Razor)::: Pages pages as *markup files* .</span></span>

::: moniker-end

<span data-ttu-id="7c34f-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c34f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="7c34f-109">부분 보기를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7c34f-109">When to use partial views</span></span>

<span data-ttu-id="7c34f-110">부분 보기는 다음을 수행하는 효과적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="7c34f-111">큰 태그 파일을 작은 구성 요소로 나눕니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="7c34f-112">여러 개의 논리적 부분으로 구성된 크고 복잡한 태그 파일은 부분 보기로 분리된 각 부분으로 작업하는 것이 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="7c34f-113">태그에는 전체 페이지 구조와 부분 보기에 대한 참조만 포함되므로 태그 파일의 코드를 관리하기가 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="7c34f-114">태그 파일들 간의 공통 태그 콘텐츠 중복을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="7c34f-115">태그 파일들 간에 동일한 태그 요소가 사용될 때 부분 보기가 태그 콘텐츠의 중복을 하나의 부분 보기 파일로 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="7c34f-116">부분 보기에서 태그가 변경되면 부분 보기를 사용하는 태그 파일들의 렌더링된 출력이 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="7c34f-117">공통 레이아웃 요소를 유지 관리하기 위해서 부분 보기를 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="7c34f-118">공통 레이아웃 요소는 [_Layout.cshtml](xref:mvc/views/layout) 파일에 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="7c34f-119">태그를 렌더링하기 위해 복잡한 렌더링 논리 또는 코드 실행이 필요한 경우 부분 보기를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7c34f-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="7c34f-120">부분 보기 대신 [보기 구성 요소](xref:mvc/views/view-components)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="7c34f-121">부분 보기 선언</span><span class="sxs-lookup"><span data-stu-id="7c34f-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7c34f-122">부분 뷰는 *.cshtml* [`@page`](xref:mvc/views/razor#page) *Views* 폴더 (MVC) 또는 *pages* 폴더 (페이지) 내에서 지시문을 유지 하지 않고. s t a t 마크업 파일입니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="7c34f-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (:::no-loc(Razor)::: Pages).</span></span>

<span data-ttu-id="7c34f-123">ASP.NET Core MVC에서 컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.ViewResult>는 보기 또는 부분 보기를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="7c34f-124">:::no-loc(Razor):::페이지에서는 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> 개체로 표시 된 부분 뷰를 반환할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> .</span><span class="sxs-lookup"><span data-stu-id="7c34f-124">In :::no-loc(Razor)::: Pages, a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="7c34f-125">부분 보기 참조 및 렌더링은 [부분 보기 참조](#reference-a-partial-view) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="7c34f-126">MVC 보기 또는 페이지 렌더링과 달리 부분 보기는 *_ViewStart.cshtml* 을 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="7c34f-127">*_ViewStart.cshtml* 에 대한 자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7c34f-127">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="7c34f-128">부분 보기 파일 이름은 종종 밑줄(`_`)로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="7c34f-129">이 명명 규칙은 필수는 아니지만 부분 보기를 보기 및 페이지와 시각적으로 구분하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7c34f-130">부분 보기는 ‘보기’ 폴더에서 유지 관리되는 </span><span class="sxs-lookup"><span data-stu-id="7c34f-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="7c34f-131">컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.ViewResult>는 보기 또는 부분 보기를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="7c34f-132">부분 보기 참조 및 렌더링은 [부분 보기 참조](#reference-a-partial-view) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="7c34f-133">MVC 보기 렌더링과 달리 부분 보기는 *_ViewStart.cshtml* 을 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="7c34f-134">*_ViewStart.cshtml* 에 대한 자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7c34f-134">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="7c34f-135">부분 보기 파일 이름은 종종 밑줄(`_`)로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="7c34f-136">이 명명 규칙은 필수는 아니지만 부분 보기를 보기와 시각적으로 구분하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="7c34f-137">부분 보기 참조</span><span class="sxs-lookup"><span data-stu-id="7c34f-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="7c34f-138">PageModel 페이지에서 부분 보기 사용 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="7c34f-138">Use a partial view in a :::no-loc(Razor)::: Pages PageModel</span></span>

<span data-ttu-id="7c34f-139">ASP.NET Core 2.0 또는 2.1에서 다음 처리기 메서드는 응답에 대 한 다음과 같은 작업을 수행 *\_ 합니다.*</span><span class="sxs-lookup"><span data-stu-id="7c34f-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7c34f-140">ASP.NET Core 2.2 이상에서 처리기 메서드는 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Partial*> 메서드를 대신 호출하여 `PartialViewResult` 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="7c34f-141">태그 파일에서 부분 보기 사용</span><span class="sxs-lookup"><span data-stu-id="7c34f-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c34f-142">태그 파일 내에서 부분 보기를 참조할 수 있는 여러 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="7c34f-143">앱에서 다음과 같은 비동기 렌더링 방법 중 하나를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="7c34f-144">부분 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="7c34f-145">비동기 HTML 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="7c34f-146">태그 파일 내에서 부분 보기를 참조할 수 있는 두 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="7c34f-147">비동기 HTML 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="7c34f-148">동기 HTML 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="7c34f-149">앱에서 [비동기 HTML 도우미](#asynchronous-html-helper)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="7c34f-150">부분 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-150">Partial Tag Helper</span></span>

<span data-ttu-id="7c34f-151">[부분 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)에는 ASP.NET Core 2.1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="7c34f-152">부분 태그 도우미는 콘텐츠를 비동기식으로 렌더링하며 HTML과 비슷한 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="7c34f-153">파일 확장자가 있는 경우 태그 도우미는 부분 보기를 호출하는 태그 파일과 동일한 폴더에 있어야 하는 부분 보기를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="7c34f-154">다음 예제에서는 앱 루트의 부분 보기를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="7c34f-155">물결표-슬래시(`~/`) 또는 슬래시(`/`)로 시작되는 경로는 앱 루트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="7c34f-156">**:::no-loc(Razor)::: 마주보**</span><span class="sxs-lookup"><span data-stu-id="7c34f-156">**:::no-loc(Razor)::: Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="7c34f-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="7c34f-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="7c34f-158">다음 예제는 상대 경로가 있는 부분 보기를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="7c34f-159">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7c34f-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="7c34f-160">비동기 HTML 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="7c34f-161">HTML 도우미를 사용할 때 가장 좋은 방법은 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="7c34f-162">`PartialAsync`는 <xref:System.Threading.Tasks.Task%601>에 래핑된 <xref:Microsoft.AspNetCore.Html.IHtmlContent> 형식을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="7c34f-163">이 메서드는 대기된 호출에 접두사로 `@` 문자를 사용하여 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="7c34f-164">파일 확장자가 있는 경우 HTML 도우미는 부분 보기를 호출하는 태그 파일과 동일한 폴더에 있어야 하는 부분 보기를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="7c34f-165">다음 예제에서는 앱 루트의 부분 보기를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="7c34f-166">물결표-슬래시(`~/`) 또는 슬래시(`/`)로 시작되는 경로는 앱 루트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c34f-167">**:::no-loc(Razor)::: 마주보**</span><span class="sxs-lookup"><span data-stu-id="7c34f-167">**:::no-loc(Razor)::: Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="7c34f-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="7c34f-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="7c34f-169">다음 예제는 상대 경로가 있는 부분 보기를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="7c34f-170">또는 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>를 사용하여 부분 보기를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="7c34f-171">이 메서드는 <xref:Microsoft.AspNetCore.Html.IHtmlContent>를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="7c34f-172">렌더링된 출력을 응답에 직접 스트리밍합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="7c34f-173">이 메서드는 결과를 반환 하지 않으므로 코드 블록 내에서 호출 해야 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="7c34f-173">Because the method doesn't return a result, it must be called within a :::no-loc(Razor)::: code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="7c34f-174">`RenderPartialAsync` 스트림은 콘텐츠를 렌더링했으므로 일부 시나리오에서 더 나은 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="7c34f-175">성능이 중요한 상황에서는 두 가지 방법을 모두 사용하여 페이지를 벤치마크하고 빠른 응답을 생성하는 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="7c34f-176">동기 HTML 도우미</span><span class="sxs-lookup"><span data-stu-id="7c34f-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="7c34f-177">`PartialAsync` 및 `RenderPartialAsync`의 해당하는 동기 항목은 각각 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> 및 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*>입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="7c34f-178">교착 상태가 발생하는 시나리오가 있으므로 동기 해당 항목은 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="7c34f-179">동기 메서드는 이후 릴리스에서 제거 대상으로 지정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7c34f-180">코드를 실행해야 하는 경우 부분 보기 대신 [보기 구성 요소](xref:mvc/views/view-components)를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7c34f-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c34f-181">`Partial` 또는 `RenderPartial` 호출 시 Visual Studio 분석기 경고가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="7c34f-182">예를 들어 `Partial`의 존재는 다음 경고 메시지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="7c34f-183">IHtmlHelper.Partial 사용 시 애플리케이션이 교착 상태가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="7c34f-184">&lt;부분&gt; 태그 도우미 또는 IHtmlHelper.PartialAsync를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7c34f-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="7c34f-185">`@Html.Partial` 호출을 `@await Html.PartialAsync` 또는 [부분 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="7c34f-186">부분 태그 도우미 마이그레이션에 대한 자세한 내용은 [HTML 도우미에서 마이그레이션](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper)을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="7c34f-187">부분 보기 검색</span><span class="sxs-lookup"><span data-stu-id="7c34f-187">Partial view discovery</span></span>

<span data-ttu-id="7c34f-188">파일 확장자 없는 이름으로 부분 보기를 참조하는 경우, 다음 위치가 명시된 순서로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c34f-189">**:::no-loc(Razor)::: 마주보**</span><span class="sxs-lookup"><span data-stu-id="7c34f-189">**:::no-loc(Razor)::: Pages**</span></span>

1. <span data-ttu-id="7c34f-190">현재 페이지의 폴더를 실행 중</span><span class="sxs-lookup"><span data-stu-id="7c34f-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="7c34f-191">페이지의 폴더 위에 있는 디렉터리 그래프</span><span class="sxs-lookup"><span data-stu-id="7c34f-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="7c34f-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="7c34f-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="7c34f-193">다음 규칙이 부분 보기 검색에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="7c34f-194">부분 보기가 다른 폴더에 존재할 경우 파일 이름이 같은 다른 부분 보기가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="7c34f-195">파일 확장명 없이 이름으로 부분 보기를 참조하면서 부분 보기가 호출자의 폴더와 *Shared* 폴더 모두에 존재할 경우 호출자 폴더에 위치해 있는 부분 보기가 부분 보기를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="7c34f-196">호출자의 폴더에 부분 보기가 존재하지 않으면 부분 보기는 *Shared* 폴더에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="7c34f-197">*Shared* 폴더의 부분 보기를 *공유 부분 보기* 또는 *기본 부분 보기* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views* .</span></span>
* <span data-ttu-id="7c34f-198">부분 뷰를 연결할 *수* 있습니다 &mdash; . 순환 참조가 호출로 구성 되지 않은 경우 부분 보기는 다른 부분 뷰를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="7c34f-199">상대 경로는 항상, 파일의 루트 또는 부모가 아닌 현재 파일에 상대적입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="7c34f-200">[:::no-loc(Razor):::](xref:mvc/views/razor) `section` 부분 보기에 정의 된는 부모 태그 파일에 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-200">A [:::no-loc(Razor):::](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="7c34f-201">`section`만 정의되어 있는 부분 보기에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="7c34f-202">부분 보기에서 데이터 액세스</span><span class="sxs-lookup"><span data-stu-id="7c34f-202">Access data from partial views</span></span>

<span data-ttu-id="7c34f-203">부분 보기가 인스턴스화되면 부모의 `ViewData` 사전의 ‘사본’을 수신합니다. </span><span class="sxs-lookup"><span data-stu-id="7c34f-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="7c34f-204">부분 보기 내에서 데이터에 대한 업데이트는 부모 보기에 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="7c34f-205">부분 보기에서 변경된 `ViewData`는 부분 보기가 반환될 때 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="7c34f-206">다음 예는 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 인스턴스를 부분 보기에 전달하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="7c34f-207">모델을 부분 보기에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="7c34f-208">모델은 사용자 지정 개체일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-208">The model can be a custom object.</span></span> <span data-ttu-id="7c34f-209">`PartialAsync`(콘텐츠 블록을 호출자에게 렌더링함) 또는 `RenderPartialAsync`(콘텐츠를 출력으로 스트리밍함)를 사용하여 모델을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7c34f-210">**:::no-loc(Razor)::: 마주보**</span><span class="sxs-lookup"><span data-stu-id="7c34f-210">**:::no-loc(Razor)::: Pages**</span></span>

<span data-ttu-id="7c34f-211">샘플 앱의 다음 태그는 *Pages/ArticlesRP/ReadRP.cshtml* 페이지에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="7c34f-212">페이지에는 두 개의 부분 보기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-212">The page contains two partial views.</span></span> <span data-ttu-id="7c34f-213">두 번째 부분 보기는 모델 및 `ViewData`를 부분 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="7c34f-214">`ViewDataDictionary` 생성자 오버로드를 사용하여 기존 `ViewData` 사전을 유지하면서 새로운 `ViewData` 사전을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="7c34f-215">*Pages/Shared/_AuthorPartialRP.cshtml* 은 *ReadRP.cshtml* 태그 파일에서 참조하는 첫 번째 부분 보기입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="7c34f-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* 은 *ReadRP.cshtml* 태그 파일에서 참조하는 두 번째 부분 보기입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="7c34f-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="7c34f-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="7c34f-218">샘플 앱의 다음 태그는 *Views/Articles/Read.cshtml* 보기를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="7c34f-219">보기에는 두 개의 부분 보기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-219">The view contains two partial views.</span></span> <span data-ttu-id="7c34f-220">두 번째 부분 보기는 모델 및 `ViewData`를 부분 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="7c34f-221">`ViewDataDictionary` 생성자 오버로드를 사용하여 기존 `ViewData` 사전을 유지하면서 새로운 `ViewData` 사전을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="7c34f-222">*Views/Shared/_AuthorPartial.cshtml* 은 *Read.cshtml* 태그 파일에서 참조하는 첫 번째 부분 보기입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="7c34f-223">*Views/Articles/_ArticleSection.cshtml* 은 *Read.cshtml* 태그 파일에서 참조하는 두 번째 부분 보기입니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="7c34f-224">런타임 시 부분은 부모 태그 파일의 렌더링된 출력에 렌더링되며, 그 자체는 공유 *_Layout.cshtml* 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml* .</span></span> <span data-ttu-id="7c34f-225">첫 번째 부분 보기는 문서 작성자의 이름과 게시 날짜를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="7c34f-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="7c34f-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="7c34f-227">This partial view from &lt;shared partial view file path&gt;.</span><span class="sxs-lookup"><span data-stu-id="7c34f-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="7c34f-228">1863/11/19 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="7c34f-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="7c34f-229">두 번째 부분 보기는 문서의 섹션을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="7c34f-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="7c34f-230">섹션 1 인덱스: 0</span><span class="sxs-lookup"><span data-stu-id="7c34f-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="7c34f-231">4개의 점수, 7년 전 ...</span><span class="sxs-lookup"><span data-stu-id="7c34f-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="7c34f-232">섹션 2 인덱스: 1</span><span class="sxs-lookup"><span data-stu-id="7c34f-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="7c34f-233">지금 멋진 전투에 참여하고 있습니다. 테스트 중...</span><span class="sxs-lookup"><span data-stu-id="7c34f-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="7c34f-234">섹션 3 인덱스: 2</span><span class="sxs-lookup"><span data-stu-id="7c34f-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="7c34f-235">그러나 크게 볼 때 우리는 전념할 수 없습 ...</span><span class="sxs-lookup"><span data-stu-id="7c34f-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c34f-236">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7c34f-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
