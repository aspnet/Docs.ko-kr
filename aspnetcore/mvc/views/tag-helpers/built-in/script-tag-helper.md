---
title: ASP.NET Core의 스크립트 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core 스크립트 태그 도우미 특성 및 HTML 스크립트 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- 'appsettings.json'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053503"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="e2645-103">ASP.NET Core의 스크립트 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="e2645-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e2645-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2645-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e2645-105">[스크립트 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)는 기본 또는 대체 스크립트 파일에 대한 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="e2645-106">일반적으로 기본 스크립트 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="e2645-107">스크립트 태그 도우미를 사용하면 스크립트 파일에 CDN 및 CDN을 사용할 수 없는 경우를 대비한 대체를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="e2645-108">스크립트 태그 도우미는 로컬 호스팅의 견고성과 함께 CDN의 성능 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="e2645-109">다음 Razor 태그는 대체 ( `script` fallback)가 있는 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="e2645-110">`<script>` 요소의 [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) 특성을 사용하여 CDN 스크립트 로드를 지연시키지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2645-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="e2645-111">스크립트 태그 도우미는 [asp-fallback-test](#asp-fallback-test) 식을 즉시 실행하는 JavaScript를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="e2645-112">CDN 스크립트 로딩이 지연되면 해당 식이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="e2645-113">일반적으로 사용되는 스크립트 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="e2645-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="e2645-114">스크립트 태그 도우미의 모든 특성, 속성 및 메서드는 [스크립트 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2645-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="e2645-115">asp-fallback-test</span><span class="sxs-lookup"><span data-stu-id="e2645-115">asp-fallback-test</span></span>

<span data-ttu-id="e2645-116">대체 테스트에 사용할 기본 스크립트에 정의된 스크립트 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="e2645-117">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2645-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="e2645-118">asp-fallback-src</span><span class="sxs-lookup"><span data-stu-id="e2645-118">asp-fallback-src</span></span>

<span data-ttu-id="e2645-119">기본 스크립트가 실패할 경우 대체할 Script 태그의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="e2645-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="e2645-120">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2645-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2645-121">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2645-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
