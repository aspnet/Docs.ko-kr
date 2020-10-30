---
title: ASP.NET Core의 태그 도우미
author: rick-anderson
description: 태그 도우미란 무엇이며 ASP.NET Core에서 어떻게 사용하는지 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
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
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 781365d99c6d36d8abaec9681128ba712db8cb88
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060666"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="a8787-103">ASP.NET Core의 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a8787-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="a8787-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a8787-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="a8787-105">태그 도우미란?</span><span class="sxs-lookup"><span data-stu-id="a8787-105">What are Tag Helpers</span></span>

<span data-ttu-id="a8787-106">태그 도우미를 사용하면 :::no-loc(Razor)::: 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="a8787-107">예를 들어 기본 제공 `ImageTagHelper`는 이미지 이름에 버전 번호를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="a8787-108">이미지가 변경될 때마다 서버에서 이미지에 대한 고유 버전을 새로 생성하므로 클라이언트는 항상 최신 이미지를 가져옵니다(오래된 캐시된 이미지 대신).</span><span class="sxs-lookup"><span data-stu-id="a8787-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="a8787-109">양식 작성, 링크, 자산 로드 등의 일반적인 작업을 위한 여러 가지 기본 제공 태그 도우미가 있으며, 공용 GitHub 리포지토리 및 NuGet 패키지로도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="a8787-110">태그 도우미는 C#에서 작성되며 요소 이름, 특성 이름 또는 부모 태그 기반의 HTML 요소를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="a8787-111">예를 들어 기본 제공 `LabelTagHelper`는 `LabelTagHelper` 특성이 적용될 때 HTML `<label>` 요소를 대상으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="a8787-112">[Html 도우미](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)에 대해 잘 알고 있는 경우 태그 도우미는 뷰에서 Html과 c # 간의 명시적 전환을 축소 :::no-loc(Razor)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="a8787-113">많은 경우 HTML 도우미는 특정 태그 도우미에 대한 대체 방법을 제공하지만, 태그 도우미는 HTML 도우미를 대체하지 않으며 각 HTML 도우미에 대한 태그 도우미가 없다는 사실을 인지하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="a8787-114">[HTML 도우미와 비교한 태그 도우미](#tag-helpers-compared-to-html-helpers)를 보시면 차이점이 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="a8787-115">태그 도우미가 제공하는 기능</span><span class="sxs-lookup"><span data-stu-id="a8787-115">What Tag Helpers provide</span></span>

<span data-ttu-id="a8787-116">**HTML에 친숙 한 개발 환경** 대부분의 경우 :::no-loc(Razor)::: 태그 도우미를 사용 하는 태그는 표준 HTML 처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-116">**An HTML-friendly development experience** For the most part, :::no-loc(Razor)::: markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="a8787-117">HTML/CSS/JavaScript로 관련 지식이 필요 하는 프런트 엔드 디자이너는 :::no-loc(Razor)::: c # 구문을 학습 하지 않고 편집할 수 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="a8787-117">Front-end designers conversant with HTML/CSS/JavaScript can edit :::no-loc(Razor)::: without learning C# :::no-loc(Razor)::: syntax.</span></span>

<span data-ttu-id="a8787-118">**Html 및 :::no-loc(Razor)::: 태그를 만들기 위한 풍부한 IntelliSense 환경은** 뷰에서 태그를 서버 쪽에서 만드는 이전 방법인 html 도우미와 선명 하 게 대조 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="a8787-118">**A rich IntelliSense environment for creating HTML and :::no-loc(Razor)::: markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="a8787-119">[HTML 도우미와 비교한 태그 도우미](#tag-helpers-compared-to-html-helpers)를 보시면 차이점이 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="a8787-120">[태그 도우미에 IntelliSense 지원](#intellisense-support-for-tag-helpers)을 보시면 IntelliSense 환경에 대해 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="a8787-121">C # :::no-loc(Razor)::: 구문을 사용 하는 개발자는 c # 태그를 작성 하는 것 보다 태그 도우미를 사용 하 여 생산성이 향상 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="a8787-121">Even developers experienced with :::no-loc(Razor)::: C# syntax are more productive using Tag Helpers than writing C# :::no-loc(Razor)::: markup.</span></span>

<span data-ttu-id="a8787-122">**생산성을 높이고 서버에만 제공되는 정보를 사용하여 보다 강력하고 안정적이고 유지 가능한 코드를 작성하는 방법** 예를 들어 지금까지는 이미지를 변경하면 이미지 이름도 변경하는 것이 이미지 업데이트의 원칙이었습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="a8787-123">성능상의 이유로 이미지를 적극적으로 캐시해야 하며, 이미지 이름을 변경하지 않는 한 클라이언트가 오래된 복사본으로 전락할 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="a8787-124">지금까지는 이미지가 편집되면 이름을 변경하고 웹앱의 이미지에 대한 각 참조를 업데이트해야 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="a8787-125">이는 매우 많은 노력을 발생 시킬 뿐만 아니라 오류가 발생 하기 쉽습니다 (참조를 누락 했거나 실수로 잘못 된 문자열을 입력 하는 등). 기본 제공에서 `ImageTagHelper` 자동으로이 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="a8787-126">`ImageTagHelper`는 이미지 이름에 버전 번호를 추가할 수 있으며, 따라서 이미지가 변경될 때마다 서버에서 자동으로 이미지의 새 고유 버전을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="a8787-127">클라이언트는 항상 최신 이미지를 가져오게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="a8787-128">`ImageTagHelper`를 사용하면 이와 같은 견고함과 노동력 절감 효과를 근본적으로 무료로 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="a8787-129">대부분의 기본 제공 태그 도우미는 표준 HTML 요소를 대상으로 하며 요소에 대한 서버 쪽 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="a8787-130">예를 들어 *보기/계정* 폴더의 여러 보기에서 사용되는 `<input>` 요소에는 `asp-for` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="a8787-131">이 특성은 지정된 모델 속성의 이름을 렌더링된 HTML로 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="a8787-132">:::no-loc(Razor):::다음 모델을 사용 하는 뷰를 생각해 보세요.</span><span class="sxs-lookup"><span data-stu-id="a8787-132">Consider a :::no-loc(Razor)::: view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="a8787-133">태그는 다음과 :::no-loc(Razor)::: 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-133">The following :::no-loc(Razor)::: markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="a8787-134">다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="a8787-135">`asp-for` 특성은 [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0)의 `For` 속성에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="a8787-136">자세한 내용은 [태그 도우미 작성](xref:mvc/views/tag-helpers/authoring)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8787-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="a8787-137">태그 도우미 범위 관리</span><span class="sxs-lookup"><span data-stu-id="a8787-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="a8787-138">태그 도우미 범위는 `@addTagHelper`, `@removeTagHelper` 및 "!" 옵트아웃 문자를 조합하여 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="a8787-139">`@addTagHelper`는 태그 도우미를 사용할 수 있도록 설정</span><span class="sxs-lookup"><span data-stu-id="a8787-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="a8787-140">*AuthoringTagHelpers* 라는 새 ASP.NET Core 웹앱을 만들면 다음과 같은 *Views/_ViewImports.cshtml* 파일이 프로젝트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers* , the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="a8787-141">`@addTagHelper` 지시문은 태그 도우미를 보기에 사용할 수 있게 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="a8787-142">이 경우 뷰 파일은 pages */_ViewImports입니다.* 이 파일은 기본적으로 *pages* 폴더와 하위 폴더의 모든 파일에 상속 됩니다. 태그 도우미를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-142">In this case, the view file is *Pages/_ViewImports.cshtml* , which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="a8787-143">위의 코드는 와일드 카드 구문 (" \* ")을 사용 하 여 지정 된 어셈블리의 모든 태그 도우미 ( *AspNetCore. TagHelpers* )를 *Views* 디렉터리 또는 하위 디렉터리의 모든 뷰 파일에 사용할 수 있도록 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly ( *Microsoft.AspNetCore.Mvc.TagHelpers* ) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="a8787-144">`@addTagHelper` 뒤에 나오는 첫 번째 매개 변수는 로드할 태그 도우미를 지정하고(여기서는 모든 태그 도우미에 "\*" 사용), 두 번째 매개 변수 "Microsoft.AspNetCore.Mvc.TagHelpers"는 태그 도우미를 포함하는 어셈블리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="a8787-145">*Microsoft.AspNetCore.Mvc.TagHelpers* 는 기본 제공 ASP.NET Core 태그 도우미에 대한 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="a8787-146">이 프로젝트의 모든 태그 도우미( *AuthoringTagHelpers* 라는 어셈블리를 만드는)를 노출하려면 다음을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers* ), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="a8787-147">기본 네임스페이스(`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)가 있는 `EmailTagHelper`가 프로젝트에 포함된 경우 태그 도우미의 FQN(정규화된 이름)을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="a8787-148">FQN을 사용하여 보기에 태그 도우미를 추가하려면 먼저 FQN(`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)을 추가한 후 어셈블리 이름( *AuthoringTagHelpers* )을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name ( *AuthoringTagHelpers* ).</span></span> <span data-ttu-id="a8787-149">대부분의 개발자는 "\*" 와일드카드 구문을 사용하는 방법을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="a8787-150">와일드카드 구문을 사용하면 "\*" 와일드카드 문자를 FQN에 접미사로 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="a8787-151">예를 들어 다음 지시문은 `EmailTagHelper`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="a8787-152">앞에서 설명한 것 처럼 `@addTagHelper` *views/_ViewImports cshtml* 파일에 지시문을 추가 하면 *views* 디렉터리 및 하위 디렉터리의 모든 뷰 파일에서 태그 도우미를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="a8787-153">특정 보기에만 태그 도우미를 노출하도록 옵트인하려면 특정 보기 파일에서 `@addTagHelper` 지시문을 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="a8787-154">`@removeTagHelper`로 태그 도우미 제거</span><span class="sxs-lookup"><span data-stu-id="a8787-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="a8787-155">`@removeTagHelper`는 `@addTagHelper`와 동일한 두 개의 매개 변수를 갖고 있으며, 이전에 추가된 태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="a8787-156">예를 들어 특정 보기에 적용된 `@removeTagHelper`는 보기에서 지정된 태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="a8787-157">*Views/Folder/_ViewImports.cshtml* 파일에 `@removeTagHelper`를 사용하면 *폴더* 에 있는 모든 보기에서 지정된 태그 도우미가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder* .</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="a8787-158">*_ViewImports.cshtml* 파일을 사용하여 태그 도우미 범위 제어</span><span class="sxs-lookup"><span data-stu-id="a8787-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="a8787-159">보기 폴더에 *_ViewImports.cshtml* 을 추가할 수 있습니다. 그러면 보기 엔진이 해당 파일과 *Views/_ViewImports.cshtml* 파일의 지시문을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="a8787-160">*홈* 보기에 대한 비어 있는 *Views/Home/_ViewImports.cshtml* 파일을 추가한 경우 변경되는 내용이 없습니다. *_ViewImports.cshtml* 파일은 추가 파일이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="a8787-161">*Views/Home/_ViewImports.cshtml* 파일(기본 *Views/_ViewImports.cshtml* 파일에 없음)에 추가되는 `@addTagHelper` 지시문은 *홈* 폴더에 있는 보기에만 태그 도우미를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="a8787-162">개별 요소 옵트아웃</span><span class="sxs-lookup"><span data-stu-id="a8787-162">Opting out of individual elements</span></span>

<span data-ttu-id="a8787-163">태그 도우미 옵트아웃 문자("!")를 사용하여 요소 수준에서 태그 도우미를 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="a8787-164">예를 들어 다음과 같은 태그 도우미 옵트아웃 문자를 사용하여 `<span>`에서 `Email` 유효성 검사가 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="a8787-165">여는 태그와 닫는 태그에 태그 도우미 옵트아웃 문자를 적용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="a8787-166">(여는 태그에 옵트아웃 문자를 추가하면 Visual Studio 편집기가 자동으로 닫는 태그에 옵트아웃 문자를 추가합니다).</span><span class="sxs-lookup"><span data-stu-id="a8787-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="a8787-167">옵트아웃 문자를 추가하면 요소 및 태그 도우미 특성이 더 이상 구별되는 글꼴로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="a8787-168">`@tagHelperPrefix`를 사용하여 태그 도우미 사용을 명시적으로 만들기</span><span class="sxs-lookup"><span data-stu-id="a8787-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="a8787-169">`@tagHelperPrefix` 지시문을 사용하면 태그 도우미를 지원하고 태그 도우미 사용을 명시적으로 만들도록 태그 접두사 문자열을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="a8787-170">예를 들어 *Views/_ViewImports.cshtml* 파일에 다음 태그를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="a8787-171">아래의 코드 이미지에서 태그 도우미 접두사가 `th:`로 지정되고, 따라서 `th:` 접두사를 사용하는 요소만 태그 도우미를 지원합니다(태그 도우미 지원 요소에는 고유한 글꼴이 있음).</span><span class="sxs-lookup"><span data-stu-id="a8787-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="a8787-172">`<label>` 및 `<input>` 요소는 태그 도우미 접두사가 있고 태그 도우미를 사용할 수 있지만 `<span>` 요소는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![이미지](intro/_static/thp.png)

<span data-ttu-id="a8787-174">`@addTagHelper`에 적용되는 동일한 계층 규칙이 `@tagHelperPrefix`에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="a8787-175">자체 닫는 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a8787-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="a8787-176">여러 태그 도우미를 자체 닫는 태그로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="a8787-177">일부 태그 도우미는 자체 닫는 태그로 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="a8787-178">자체 닫는 태그로 설계되지 않은 태그 도우미를 사용하면 렌더링된 출력이 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="a8787-179">태그 도우미를 자체적으로 닫으면 렌더링된 출력에 자체 닫는 태그가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="a8787-180">자세한 내용은 [태그 도우미 작성](xref:mvc/views/tag-helpers/authoring)에서 [이 메모](xref:mvc/views/tag-helpers/authoring#self-closing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a8787-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="a8787-181">태그 도우미 특성/선언의 C#</span><span class="sxs-lookup"><span data-stu-id="a8787-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="a8787-182">태그 도우미는 요소의 특성 또는 태그 선언 영역에서 C#을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="a8787-183">예를 들어 다음 코드는 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="a8787-184">위 코드는 다음과 같이 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="a8787-185">태그 도우미에 대한 IntelliSense 지원</span><span class="sxs-lookup"><span data-stu-id="a8787-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="a8787-186">Visual Studio에서 새 ASP.NET Core 웹 앱을 만들면 NuGet 패키지 "AspNetCore"가 :::no-loc(Razor)::: 추가 됩니다. 도구 ".</span><span class="sxs-lookup"><span data-stu-id="a8787-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.:::no-loc(Razor):::.Tools".</span></span> <span data-ttu-id="a8787-187">이 패키지는 태그 도우미 도구를 추가하는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="a8787-188">HTML `<label>` 요소를 작성하는 방안을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="a8787-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="a8787-189">Visual Studio 편집기에서 `<l`를 입력하는 즉시 IntelliSense는 일치하는 요소를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![이미지](intro/_static/label.png)

<span data-ttu-id="a8787-191">HTML 도움말뿐 아니라 아이콘(그 아래에 있는 "@" symbol with "<>")도 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![이미지](intro/_static/tagSym.png)

<span data-ttu-id="a8787-193">태그 도우미의 대상으로 요소를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="a8787-194">순수 HTML 요소(예: `fieldset`)는 "<>" 아이콘을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="a8787-195">순수 HTML `<label>` 태그는 HTML 태그를(기본 Visual Studio 색 테마가 있는)를 갈색 글꼴로, 특성을 빨간색으로, 특성 값을 파란색으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![이미지](intro/_static/LableHtmlTag.png)

<span data-ttu-id="a8787-197">`<label`을 입력하면 IntelliSense가 사용 가능한 HTML/CSS 특성 및 태그 도우미 대상 특성을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![이미지](intro/_static/labelattr.png)

<span data-ttu-id="a8787-199">IntelliSense 문을 완성하면 탭 키를 입력하여 선택한 값으로 문을 완성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![이미지](intro/_static/stmtcomplete.png)

<span data-ttu-id="a8787-201">태그 도우미 특성을 입력하는 즉시 태그 및 특성 글꼴이 변합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="a8787-202">기본 Visual Studio "파란색" 또는 "밝은" 색 테마를 사용하면 글꼴은 자주색 볼드입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="a8787-203">"어두운" 테마를 사용하면 글꼴은 청록색 볼드입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="a8787-204">이 문서의 이미지는 기본 테마를 사용하여 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-204">The images in this document were taken using the default theme.</span></span>

![이미지](intro/_static/labelaspfor2.png)

<span data-ttu-id="a8787-206">Visual Studio *CompleteWord* 바로 가기를 입력할 수 있으며(큰따옴표 안에서는 Ctrl + 스페이스가 [기본](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio)), 이제 C# 클래스와 마찬가지로 C#에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="a8787-207">IntelliSense는 페이지 모델에 모든 메서드 및 속성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="a8787-208">속성 형식이 `ModelExpression`이므로 메서드 및 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="a8787-209">아래 이미지에서는 `RegisterViewModel`을 사용할 수 있도록 `Register` 보기를 편집하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![이미지](intro/_static/intellemail.png)

<span data-ttu-id="a8787-211">IntelliSense는 페이지에서 모델에 사용할 수 있는 속성과 메서드를 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="a8787-212">다양한 IntelliSense 환경에서 CSS 클래스를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![이미지](intro/_static/iclass.png)

![이미지](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="a8787-215">HTML 도우미와 비교한 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a8787-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="a8787-216">태그 도우미는 뷰에서 HTML 요소에 연결 :::no-loc(Razor)::: 하는 반면 [html 도우미](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) 는 뷰에서 html로 포함 된 메서드로 호출 됩니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="a8787-216">Tag Helpers attach to HTML elements in :::no-loc(Razor)::: views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="a8787-217">:::no-loc(Razor):::CSS 클래스 "caption"을 사용 하 여 HTML 레이블을 만드는 다음 태그를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-217">Consider the following :::no-loc(Razor)::: markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="a8787-218">At ( `@` ) 기호는 :::no-loc(Razor)::: 코드의 시작 임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-218">The at (`@`) symbol tells :::no-loc(Razor)::: this is the start of code.</span></span> <span data-ttu-id="a8787-219">다음 두 매개 변수("FirstName" 및 "First Name:")는 문자열이므로 [IntelliSense](/visualstudio/ide/using-intellisense)가 도움이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="a8787-220">마지막 인수:</span><span class="sxs-lookup"><span data-stu-id="a8787-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="a8787-221">특성을 표시하는 데 사용되는 익명 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="a8787-222">`class`는 C#에서 예약된 키워드이므로 `@` 기호를 사용하여 C#이 `@class=`를 기호(속성 이름)로 해석하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="a8787-223">프런트 엔드 디자이너 (HTML/CSS/JavaScript 및 기타 클라이언트 기술에 대해 잘 알고 있지만 c # 및)에 익숙하지 않은 사용자의 경우 :::no-loc(Razor)::: 대부분의 줄은 외부에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and :::no-loc(Razor):::), most of the line is foreign.</span></span> <span data-ttu-id="a8787-224">IntelliSense의 도움 없이 전체 줄을 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="a8787-225">`LabelTagHelper`를 사용하면 동일한 태그를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="a8787-226">태그 도우미 버전을 사용하면 Visual Studio 편집기에서 `<l`를 입력하는 즉시 IntelliSense는 일치하는 요소를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![이미지](intro/_static/label.png)

<span data-ttu-id="a8787-228">IntelliSense의 도움을 받아 전체 줄을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="a8787-229">다음 코드 이미지는 Visual Studio에 포함 된 ASP.NET 4.5. x MVC 템플릿에서 생성 된 *Views/Account/Register* 의 Form 부분을 보여 줍니다. :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="a8787-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* :::no-loc(Razor)::: view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![이미지](intro/_static/regCS.png)

<span data-ttu-id="a8787-231">Visual Studio 편집기는 회색 배경에 C# 코드를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="a8787-232">예를 들어 `AntiForgeryToken` HTML 도우미의 경우:</span><span class="sxs-lookup"><span data-stu-id="a8787-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="a8787-233">회색 배경과 함께 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-233">is displayed with a grey background.</span></span> <span data-ttu-id="a8787-234">레지스터 보기의 태그는 대부분 C#입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="a8787-235">태그 도우미를 사용하는 동급의 방법과 비교해 보세요.</span><span class="sxs-lookup"><span data-stu-id="a8787-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![이미지](intro/_static/regTH.png)

<span data-ttu-id="a8787-237">태그는 HTML 도우미 방식보다 훨씬 깔끔하며 읽기, 편집 및 유지 관리가 훨씬 용이합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="a8787-238">C# 코드는 서버에서 알아야 하는 최소 수준으로 축소됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="a8787-239">Visual Studio 편집기는 태그 도우미의 대상 태그를 고유의 글꼴로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="a8787-240">*이메일* 그룹을 떠올려 보세요.</span><span class="sxs-lookup"><span data-stu-id="a8787-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="a8787-241">각 "asp-" 특성은 "Email" 값을 갖지만 "Email"은 문자열이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="a8787-242">이 컨텍스트에서 "Email"은 `RegisterViewModel`에 대한 C# 모델 식 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="a8787-243">Visual Studio 편집기를 사용하면 레지스터 양식의 태그 도우미 접근 방식에서 사용되는 **모든** 태그를 작성할 수 있지만, Visual Studio는 HTML 도우미 접근 방식에서 대부분의 코드에 도움을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="a8787-244">[태그 도우미에 대한 IntelliSense 지원](#intellisense-support-for-tag-helpers)에서는 Visual Studio 편집기에서 태그 도우미를 사용하는 것에 대해 자세히 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="a8787-245">웹 서버 컨트롤과 비교한 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="a8787-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="a8787-246">태그 도우미는 연결된 요소가 없고, 단순히 요소 및 콘텐츠 렌더링에 참여할 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="a8787-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> 는 페이지에서 선언 및 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> are declared and invoked on a page.</span></span>

* <span data-ttu-id="a8787-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> 개발 및 디버깅을 어렵게 만들 수 있는 특수 한 수명 주기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="a8787-249">웹 서버 컨트롤을 사용하면 클라이언트 컨트롤을 사용하여 클라이언트 DOM(문서 개체 모델) 요소에 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="a8787-250">태그 도우미에는 DOM이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="a8787-251">웹 서버 컨트롤은 자동 브라우저 검색 기능을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="a8787-252">태그 도우미는 브라우저를 모릅니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="a8787-253">여러 태그 도우미가 같은 요소에서 작동할 수 있으며([태그 도우미 충돌 방지](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) 참조) 일반적으로 웹 서버 컨트롤을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="a8787-254">태그 도우미는 범위가 지정된 HTML 요소의 태그와 콘텐츠를 수정할 수 있지만, 페이지에 있는 다른 항목은 직접 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="a8787-255">웹 서버 컨트롤은 범위가 덜 구체적이고 페이지의 다른 부분에 영향을 주는 작업을 수행할 수 있으며, 이로 인해 의도하지 않은 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="a8787-256">웹 서버 컨트롤은 형식 변환기를 사용하여 문자열을 개체로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="a8787-257">태그 도우미를 사용하면 기본적으로 C#에서 작업하게 되므로 형식을 변환할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="a8787-258">웹 서버 컨트롤은 [System.ComponentModel](/dotnet/api/system.componentmodel)을 사용하여 구성 요소와 컨트롤의 런타임 및 디자인 타임 동작을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="a8787-259">`System.ComponentModel`에는 특성 및 형식 변환기를 구현하고, 데이터 소스에 바인딩하고, 구성 요소 사용을 허가하기 위한 기본 클래스 및 인터페이스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="a8787-260">태그 도우미와는 달리, 일반적으로 `TagHelper`에서 파생되고 `TagHelper` 기본 클래스가 `Process` 및 `ProcessAsync` 메서드만 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="a8787-261">태그 도우미 요소 글꼴 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="a8787-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="a8787-262">**도구**  >  **옵션**  >  **환경**  >  **글꼴 및 색** 에서 글꼴 및 색 지정을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors** :</span></span>

![이미지](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="a8787-264">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="a8787-264">Additional resources</span></span>

* [<span data-ttu-id="a8787-265">태그 도우미 작성</span><span class="sxs-lookup"><span data-stu-id="a8787-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="a8787-266">양식 사용</span><span class="sxs-lookup"><span data-stu-id="a8787-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="a8787-267">[GitHub의 TagHelperSamples](https://github.com/dpaquette/TagHelperSamples)에는 [부트스트랩](https://getbootstrap.com/)을 사용하기 위한 태그 도우미 샘플이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a8787-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
