---
title: ASP.NET Core의 링크 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core 링크 태그 도우미 특성 및 HTML 링크 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059119"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="f0d87-103">ASP.NET Core의 링크 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="f0d87-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f0d87-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f0d87-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f0d87-105">[링크 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)는 기본 또는 대체 CSS 파일에 대한 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="f0d87-106">일반적으로 기본 CSS 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="f0d87-107">링크 태그 도우미를 사용하면 CSS 파일에 CDN 및 CDN을 사용할 수 없는 경우를 대비한 대체를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="f0d87-108">링크 태그 도우미는 로컬 호스팅의 견고성과 함께 CDN의 성능 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="f0d87-109">다음 Razor 태그는 `head` ASP.NET Core 웹 앱 템플릿으로 만든 레이아웃 파일의 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="f0d87-110">다음은 이전 코드로부터 렌더링된 HTML입니다(개발 이외의 환경에서).</span><span class="sxs-lookup"><span data-stu-id="f0d87-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="f0d87-111">이전 코드에서 링크 태그 도우미는 `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 요소와 *bootstrap.min.css* 파일을 CDN에서 사용할 수 있는지 확인하는 데 사용되는 이어지는 JavaScript를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="f0d87-112">이 경우에는 CSS 파일을 사용할 수 있었기 때문에 태그 도우미가 CDN CSS 파일을 사용하여 `<link />` 요소를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="f0d87-113">일반적으로 사용되는 링크 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="f0d87-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="f0d87-114">링크 태그 도우미의 모든 특성, 속성 및 메서드는 [링크 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f0d87-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="f0d87-115">href</span><span class="sxs-lookup"><span data-stu-id="f0d87-115">href</span></span>

<span data-ttu-id="f0d87-116">링크된 리소스의 기본 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="f0d87-117">이 주소는 모든 경우에 생성된 HTML로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="f0d87-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="f0d87-118">asp-fallback-href</span></span>

<span data-ttu-id="f0d87-119">기본 URL이 실패할 경우 대체할 CSS 스타일시트의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="f0d87-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="f0d87-120">asp-fallback-test-class</span></span>

<span data-ttu-id="f0d87-121">대체 테스트에 사용할 스타일시트에 정의된 클래스 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="f0d87-122">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f0d87-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="f0d87-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="f0d87-123">asp-fallback-test-property</span></span>

<span data-ttu-id="f0d87-124">대체 테스트에 사용할 CSS 속성 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="f0d87-125">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f0d87-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="f0d87-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="f0d87-126">asp-fallback-test-value</span></span>

<span data-ttu-id="f0d87-127">대체 테스트에 사용할 CSS 속성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="f0d87-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="f0d87-128">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f0d87-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0d87-129">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f0d87-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
