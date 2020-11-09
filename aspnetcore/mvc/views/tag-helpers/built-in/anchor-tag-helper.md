---
title: ASP.NET Core의 앵커 태그 도우미
author: pkellner
description: ASP.NET Core 앵커 태그 도우미 특성 및 HTML 앵커 태그의 작업을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: d39db59b0fc273fe4193a4864f302ecd3f4ad348
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060913"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="e536b-103">ASP.NET Core의 앵커 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="e536b-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e536b-104">작성자: [Peter Kellner](https://peterkellner.net) 및 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e536b-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e536b-105">[앵커 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper)는 표준 HTML 앵커(`<a ... ></a>`) 태그에 새로운 특성을 추가하여 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="e536b-106">규칙에 따라 해당 특성들의 이름은 `asp-` 접두사로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="e536b-107">렌더링 되는 앵커 요소의 `href` 특성 값은 `asp-` 특성들의 값에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="e536b-108">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="e536b-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e536b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e536b-110">이 문서의 예제 전반에서는 다음의 *SpeakerController* 가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="e536b-111">앵커 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="e536b-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="e536b-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="e536b-112">asp-controller</span></span>

<span data-ttu-id="e536b-113">[asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) 특성은 URL 생성에 사용되는 컨트롤러를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="e536b-114">다음 태그는 모든 스피커를 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="e536b-115">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="e536b-116">`asp-controller` 특성이 지정되고 `asp-action`이 지정되지 않으면, 기본 `asp-action` 값은 현재 실행 중인 보기와 연결된 컨트롤러 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="e536b-117">*HomeController* 의 *Index* 뷰( */Home* )에서 위의 태그에서 `asp-action`을 생략한 앵커 태그 도우미를 사용할 경우, 생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController* 's *Index* view ( */Home* ), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="e536b-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="e536b-118">asp-action</span></span>

<span data-ttu-id="e536b-119">[asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) 특성 값은 생성되는 `href` 특성에 포함되는 컨트롤러 작업의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="e536b-120">다음 태그는 생성되는 `href` 특성 값을 스피커 평가 페이지로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="e536b-121">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="e536b-122">`asp-controller` 특성이 지정되지 않으면 현재 보기를 실행하는 보기를 호출하는 기본 컨트롤러가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="e536b-123">`asp-action` 특성 값이 `Index`이면 URL에 아무 작업도 추가되지 않으므로 기본 `Index` 작업이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="e536b-124">지정된(또는 기본값으로 설정된) 작업은 `asp-controller`에서 참조되는 컨트롤러에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="e536b-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="e536b-125">asp-route-{value}</span></span>

<span data-ttu-id="e536b-126">[asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 특성은 와일드카드 경로 접두사를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="e536b-127">`{value}` 자리 표시자에 위치하는 모든 값은 잠재적인 경로 매개 변수로 해석됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="e536b-128">기본 경로가 발견되지 않으면 이 경로 접두사는 생성되는 `href` 특성에 요청 매개 변수 및 값으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="e536b-129">그렇지 않으면 경로 템플릿에서 이 경로 접두사가 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="e536b-130">다음 컨트롤러 작업을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="e536b-131">*Startup.Configure* 에 정의된 기본 경로 템플릿을 사용할 경우:</span><span class="sxs-lookup"><span data-stu-id="e536b-131">With a default route template defined in *Startup.Configure* :</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="e536b-132">다음과 같이 MVC 보기가 작업을 통해서 제공되는 모델을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="e536b-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="e536b-133">기본 경로의 `{id?}` 자리 표시자가 일치했습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="e536b-134">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="e536b-135">다음 MVC 보기와 같이 경로 접두사가 라우팅 템플릿의 일부와 일치하지 않는다고 가정해보세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="e536b-136">일치하는 경로에서 `speakerid`를 찾을 수 없기 때문에 생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="e536b-137">`asp-controller` 또는 `asp-action`이 지정되지 않으면 `asp-route` 특성과 동일한 기본 처리가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="e536b-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="e536b-138">asp-route</span></span>

<span data-ttu-id="e536b-139">[asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) 특성은 명명된 경로에 직접 연결되는 URL을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="e536b-140">[라우팅 특성](xref:mvc/controllers/routing#attribute-routing)을 사용하면 경로에 `SpeakerController`에서 볼 수 있는 것과 같이 이름을 지정할 수 있고 `Evaluations` 작업에서처럼 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="e536b-141">다음 태그에서 `asp-route` 특성은 명명된 경로를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="e536b-142">앵커 태그 도우미는 */Speaker/Evaluations* URL을 사용하여 해당 컨트롤러 작업을 직접 가리키는 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations* .</span></span> <span data-ttu-id="e536b-143">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="e536b-144">`asp-route`에 더해 `asp-controller` 또는 `asp-action`을 함께 지정하면 기대하는 것과 다른 경로가 생성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="e536b-145">경로 충돌을 방지하려면 `asp-route`를 `asp-controller` 및 `asp-action` 특성과 함께 사용하면 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="e536b-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="e536b-146">asp-all-route-data</span></span>

<span data-ttu-id="e536b-147">[asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 특성은 키-값 쌍 사전을 만들 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="e536b-148">키는 매개 변수 이름이고, 값은 매개 변수 값입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="e536b-149">다음 예제에서는 사전이 초기화 되어 뷰에 전달 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="e536b-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="e536b-150">또는 모델을 사용하여 데이터를 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="e536b-151">이전 코드로 생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="e536b-152">`asp-all-route-data` 사전은 병합되어 오버로드된 `Evaluations` 작업의 요구 사항을 충족하는 쿼리 문자열을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="e536b-153">사전에 존재하는 키가 경로 매개 변수와 일치하면 경로에서 해당 값이 적절하게 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="e536b-154">나머지 일치하지 않는 다른 값들은 요청 매개 변수로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="e536b-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="e536b-155">asp-fragment</span></span>

<span data-ttu-id="e536b-156">[asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) 특성은 URL에 추가할 URL 조각을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="e536b-157">앵커 태그 도우미는 해시 문자(#)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="e536b-158">다음 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="e536b-159">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="e536b-160">해시 태그는 클라이언트 쪽 앱을 만들 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="e536b-161">다음과 같이 JavaScript에서 손쉽게 만들고 검색하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="e536b-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="e536b-162">asp-area</span></span>

<span data-ttu-id="e536b-163">[asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) 특성은 적절한 경로를 설정하는 데 사용되는 영역 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="e536b-164">이어지는 예제는 `asp-area` 특성이 경로를 다시 매핑하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-no-locrazor-pages"></a><span data-ttu-id="e536b-165">페이지에서 사용 Razor</span><span class="sxs-lookup"><span data-stu-id="e536b-165">Usage in Razor Pages</span></span>

<span data-ttu-id="e536b-166">Razor 페이지 영역은 ASP.NET Core 2.1 이상에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="e536b-167">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="e536b-168">**{프로젝트 이름}**</span><span class="sxs-lookup"><span data-stu-id="e536b-168">**{Project name}**</span></span>
  * <span data-ttu-id="e536b-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e536b-169">**wwwroot**</span></span>
  * <span data-ttu-id="e536b-170">**Areas**</span><span class="sxs-lookup"><span data-stu-id="e536b-170">**Areas**</span></span>
    * <span data-ttu-id="e536b-171">**세션**</span><span class="sxs-lookup"><span data-stu-id="e536b-171">**Sessions**</span></span>
      * <span data-ttu-id="e536b-172">**페이지**</span><span class="sxs-lookup"><span data-stu-id="e536b-172">**Pages**</span></span>
        * <span data-ttu-id="e536b-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e536b-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="e536b-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e536b-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="e536b-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e536b-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="e536b-176">**페이지**</span><span class="sxs-lookup"><span data-stu-id="e536b-176">**Pages**</span></span>

<span data-ttu-id="e536b-177">*세션* 영역 *인덱스* 페이지를 참조할 태그는 Razor 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="e536b-178">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="e536b-179">페이지 앱에서 영역을 지원 하려면 Razor 에서 다음 중 하나를 수행 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e536b-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="e536b-180">[호환성 버전](xref:mvc/compatibility-version)을 2.1 이상으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="e536b-181">작업 [ Razor 옵션. allowareas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) 속성을 다음으로 설정 합니다. `true`</span><span class="sxs-lookup"><span data-stu-id="e536b-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="e536b-182">MVC에서 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="e536b-182">Usage in MVC</span></span>

<span data-ttu-id="e536b-183">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="e536b-184">**{프로젝트 이름}**</span><span class="sxs-lookup"><span data-stu-id="e536b-184">**{Project name}**</span></span>
  * <span data-ttu-id="e536b-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e536b-185">**wwwroot**</span></span>
  * <span data-ttu-id="e536b-186">**Areas**</span><span class="sxs-lookup"><span data-stu-id="e536b-186">**Areas**</span></span>
    * <span data-ttu-id="e536b-187">**Blogs**</span><span class="sxs-lookup"><span data-stu-id="e536b-187">**Blogs**</span></span>
      * <span data-ttu-id="e536b-188">**컨트롤러**</span><span class="sxs-lookup"><span data-stu-id="e536b-188">**Controllers**</span></span>
        * <span data-ttu-id="e536b-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="e536b-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="e536b-190">**Views**</span><span class="sxs-lookup"><span data-stu-id="e536b-190">**Views**</span></span>
        * <span data-ttu-id="e536b-191">**홈**</span><span class="sxs-lookup"><span data-stu-id="e536b-191">**Home**</span></span>
          * <span data-ttu-id="e536b-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e536b-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="e536b-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e536b-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="e536b-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e536b-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="e536b-195">**컨트롤러**</span><span class="sxs-lookup"><span data-stu-id="e536b-195">**Controllers**</span></span>

<span data-ttu-id="e536b-196">`asp-area`를 "Blogs"로 설정하면 해당 앵커 태그에 연결된 컨트롤러 및 보기의 경로에 *Areas/Blogs* 디렉터리가 접두사로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="e536b-197">*AboutBlog* 보기를 참조하는 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="e536b-198">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="e536b-199">MVC 앱에서 영역을 지원하려면 경로 템플릿에 해당 영역에 대한 참조가 포함되어야 합니다(존재할 경우).</span><span class="sxs-lookup"><span data-stu-id="e536b-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="e536b-200">해당 템플릿은 *Startup.Configure* 에서 `routes.MapRoute` 메서드 호출의 두 번째 매개 변수에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure* :</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="e536b-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="e536b-201">asp-protocol</span></span>

<span data-ttu-id="e536b-202">[asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) 특성은 URL에서 프로토콜(예: `https`)을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="e536b-203">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="e536b-204">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="e536b-205">이 예제의 호스트 이름은 localhost입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-205">The host name in the example is localhost.</span></span> <span data-ttu-id="e536b-206">앵커 태그 도우미는 URL을 생성할 때 웹 사이트의 공용 도메인을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="e536b-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="e536b-207">asp-host</span></span>

<span data-ttu-id="e536b-208">[asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) 특성은 URL에 호스트 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="e536b-209">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="e536b-210">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="e536b-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="e536b-211">asp-page</span></span>

<span data-ttu-id="e536b-212">[Asp 페이지](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) 특성은 페이지와 함께 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="e536b-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="e536b-213">이 특성은 앵커 태그의 `href` 특성 값을 특정 페이지로 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="e536b-214">페이지 이름 앞에 슬래시("/")를 접두사로 사용해서 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="e536b-215">다음 샘플에서는 참석자 페이지를 가리킵니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="e536b-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="e536b-216">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="e536b-217">`asp-page` 특성은 `asp-route`, `asp-controller` 및 `asp-action` 특성과 상호 배타적입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="e536b-218">그러나 다음 태그에서 보여 주듯이 `asp-page`는 `asp-route-{value}`와 함께 사용하여 라우팅을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="e536b-219">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="e536b-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="e536b-220">asp-page-handler</span></span>

<span data-ttu-id="e536b-221">[Asp-페이지 처리기](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) 특성은 페이지와 함께 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="e536b-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="e536b-222">이 특성은 특정 페이지 처리기에 연결하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="e536b-223">다음 페이지 처리기를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="e536b-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="e536b-224">페이지 모델의 관련 태그는 `OnGetProfile` 페이지 처리기에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="e536b-225">페이지 처리기 메서드 이름의 `On<Verb>` 접두사는 `asp-page-handler` 특성 값에서 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="e536b-226">비동기 메서드인 경우 `Async` 접미사도 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="e536b-227">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e536b-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="e536b-228">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e536b-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
