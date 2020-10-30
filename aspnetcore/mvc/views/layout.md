---
title: ASP.NET Core의 레이아웃
author: ardalis
description: ASP.NET Core 앱에서 뷰를 렌더링하기 전에 일반적인 레이아웃을 사용하고, 지시문을 공유하고, 공용 코드를 실행하는 방법을 알아봅니다.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 502df268e7f5f33acfffccd5ec0bd65267fa12da
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060978"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="0e7a4-103">ASP.NET Core의 레이아웃</span><span class="sxs-lookup"><span data-stu-id="0e7a4-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="0e7a4-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="0e7a4-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="0e7a4-105">페이지 및 보기는 시각적 개체 및 프로그래밍 요소를 자주 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="0e7a4-106">이 문서에서는 다음을 수행하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="0e7a4-107">일반적인 레이아웃 사용.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-107">Use common layouts.</span></span>
* <span data-ttu-id="0e7a4-108">지시문 공유.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-108">Share directives.</span></span>
* <span data-ttu-id="0e7a4-109">페이지 또는 보기를 렌더링하기 전에 일반적인 코드 실행.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="0e7a4-110">이 문서에서는 MVC: 페이지와 뷰를 사용 하 ASP.NET Core 하는 두 가지 방법에 대 한 레이아웃을 설명 :::no-loc(Razor)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: :::no-loc(Razor)::: Pages and controllers with views.</span></span> <span data-ttu-id="0e7a4-111">이 항목에서는 차이점이 최소화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="0e7a4-112">:::no-loc(Razor)::: 페이지는 *pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-112">:::no-loc(Razor)::: Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="0e7a4-113">보기를 사용하는 컨트롤러는 *Views* 폴더의 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="0e7a4-114">레이아웃이란</span><span class="sxs-lookup"><span data-stu-id="0e7a4-114">What is a Layout</span></span>

<span data-ttu-id="0e7a4-115">대부분의 웹앱에는 사용자가 페이지를 탐색하는 동안 일관된 환경을 제공하는 일반적인 레이아웃이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="0e7a4-116">일반적으로 레이아웃에는 앱 헤더, 탐색 또는 메뉴 요소, 바닥글과 같은 공통 사용자 인터페이스 요소가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![페이지 레이아웃 예제](layout/_static/page-layout.png)

<span data-ttu-id="0e7a4-118">스크립트 및 스타일시트와 같은 일반적인 HTML 구조도 앱 내의 많은 페이지에서 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="0e7a4-119">이러한 공유 요소는 모두 응용 프로그램 내에서 사용 되는 보기에서 참조할 수 있는 *레이아웃* 파일에 정의 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="0e7a4-120">레이아웃은 뷰의 중복 코드를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="0e7a4-121">규칙에 따라, ASP.NET Core 앱의 기본 레이아웃 이름을 *_Layout.cshtml* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml* .</span></span> <span data-ttu-id="0e7a4-122">템플릿을 사용하여 생성된 새로운 ASP.NET Core 프로젝트의 레이아웃 파일:</span><span class="sxs-lookup"><span data-stu-id="0e7a4-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="0e7a4-123">:::no-loc(Razor)::: Pages: *pages/Shared/_Layout cshtml*</span><span class="sxs-lookup"><span data-stu-id="0e7a4-123">:::no-loc(Razor)::: Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![솔루션 탐색기의 Pages 폴더](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="0e7a4-125">보기를 사용하는 컨트롤러: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0e7a4-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![솔루션 탐색기의 Views 폴더](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="0e7a4-127">이 레이아웃은 앱의 뷰에 대한 최상위 수준 템플릿을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="0e7a4-128">앱에는 레이아웃이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-128">Apps don't require a layout.</span></span> <span data-ttu-id="0e7a4-129">앱은 각 뷰에서 다른 레이아웃을 지정하여 두 개 이상의 레이아웃을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="0e7a4-130">다음 코드는 컨트롤러 및 보기를 사용하여 만든 템플릿 프로젝트의 레이아웃 파일을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="0e7a4-131">레이아웃 지정</span><span class="sxs-lookup"><span data-stu-id="0e7a4-131">Specifying a Layout</span></span>

<span data-ttu-id="0e7a4-132">:::no-loc(Razor)::: 뷰에는 `Layout` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-132">:::no-loc(Razor)::: views have a `Layout` property.</span></span> <span data-ttu-id="0e7a4-133">이 속성을 설정하여 레이아웃을 지정하는 개별 뷰:</span><span class="sxs-lookup"><span data-stu-id="0e7a4-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="0e7a4-134">지정된 레이아웃은 전체 경로(예: */Pages/Shared/_Layout.cshtml* 또는 */Views/Shared/_Layout.cshtml* ) 또는 부분적인 이름(예: `_Layout`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml* ) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="0e7a4-135">부분 이름이 제공 되 면 :::no-loc(Razor)::: 뷰 엔진은 표준 검색 프로세스를 사용 하 여 레이아웃 파일을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-135">When a partial name is provided, the :::no-loc(Razor)::: view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="0e7a4-136">처리기 메서드(또는 컨트롤러)가 있는 폴더가 먼저 검색된 후 *공유* 폴더가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="0e7a4-137">이 검색 프로세스는 [부분 뷰](xref:mvc/views/partial#partial-view-discovery)를 검색하는 데 사용된 프로세스와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="0e7a4-138">기본적으로 모든 레이아웃에서 `RenderBody`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="0e7a4-139">`RenderBody` 호출이 배치될 때마다 뷰의 내용이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="0e7a4-140">섹션</span><span class="sxs-lookup"><span data-stu-id="0e7a4-140">Sections</span></span>

<span data-ttu-id="0e7a4-141">레이아웃은 `RenderSection`을 호출하여 필요에 따라 하나 이상의 *섹션* 을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-141">A layout can optionally reference one or more *sections* , by calling `RenderSection`.</span></span> <span data-ttu-id="0e7a4-142">섹션에서는 특정 페이지 요소를 배치할 위치를 구성하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="0e7a4-143">`RenderSection` 호출 때마다 섹션이 필수 또는 옵션인지 여부를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="0e7a4-144">필수 섹션이 없는 경우 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="0e7a4-145">개별 뷰는 구문을 사용 하 여 섹션 내에서 렌더링할 콘텐츠를 지정 합니다 `@section` :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="0e7a4-145">Individual views specify the content to be rendered within a section using the `@section` :::no-loc(Razor)::: syntax.</span></span> <span data-ttu-id="0e7a4-146">페이지 또는 보기에서 섹션을 정의하는 경우 렌더링되어야 합니다(그렇지 않은 경우 오류 발생).</span><span class="sxs-lookup"><span data-stu-id="0e7a4-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="0e7a4-147">`@section`페이지 보기의 예제 정의는 :::no-loc(Razor)::: 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-147">An example `@section` definition in :::no-loc(Razor)::: Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="0e7a4-148">이전 코드에서는 *scripts/main.js* 가 페이지 또는 보기의 `scripts` 섹션에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="0e7a4-149">동일한 앱의 다른 페이지 또는 보기는 이 스크립트가 필요하지 않을 수 있으며, 스크립트 섹션을 정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="0e7a4-150">다음 태그는 [부분 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)를 사용하여 *_ValidationScriptsPartial.cshtml* 을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml* :</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="0e7a4-151">이전 태그가 [스 캐 폴딩 :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity)에 의해 생성 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-151">The preceding markup was generated by [scaffolding :::no-loc(Identity):::](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="0e7a4-152">페이지 또는 보기에 정의된 섹션은 즉시 레이아웃 페이지에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="0e7a4-153">이들은 부분 뷰, 뷰 구성 요소 또는 뷰 시스템의 다른 부분에서 참조할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="0e7a4-154">섹션 무시</span><span class="sxs-lookup"><span data-stu-id="0e7a4-154">Ignoring sections</span></span>

<span data-ttu-id="0e7a4-155">기본적으로 콘텐츠 페이지 본문 및 모든 섹션은 레이아웃 페이지에서 모두 렌더링되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="0e7a4-156">:::no-loc(Razor):::뷰 엔진은 본문과 각 섹션이 렌더링 되었는지 여부를 추적 하 여이를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-156">The :::no-loc(Razor)::: view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="0e7a4-157">뷰 엔진이 본문 또는 섹션을 무시하도록 지시하려면 `IgnoreBody` 및 `IgnoreSection` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="0e7a4-158">페이지의 본문 및 모든 섹션은 :::no-loc(Razor)::: 렌더링 되거나 무시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-158">The body and every section in a :::no-loc(Razor)::: page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="0e7a4-159">공유 지시문 가져오기</span><span class="sxs-lookup"><span data-stu-id="0e7a4-159">Importing Shared Directives</span></span>

<span data-ttu-id="0e7a4-160">뷰 및 페이지는 :::no-loc(Razor)::: 지시문을 사용 하 여 네임 스페이스를 가져오고 [종속성 주입](dependency-injection.md)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-160">Views and pages can use :::no-loc(Razor)::: directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="0e7a4-161">여러 보기에서 공유하는 지시문을 공용 *_ViewImports.cshtml* 파일에 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0e7a4-162">`_ViewImports` 파일은 다음 지시문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="0e7a4-163">이 파일은 :::no-loc(Razor)::: 함수 및 섹션 정의와 같은 다른 기능을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-163">The file doesn't support other :::no-loc(Razor)::: features, such as functions and section definitions.</span></span>

<span data-ttu-id="0e7a4-164">샘플 `_ViewImports.cshtml` 파일:</span><span class="sxs-lookup"><span data-stu-id="0e7a4-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="0e7a4-165">ASP.NET Core MVC 앱에 대한 *_ViewImports.cshtml* 파일은 일반적으로 *Pages* (또는 *Views* ) 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="0e7a4-166">*_ViewImports.cshtml* 파일은 모든 폴더 내에 배치할 수 있으며, 이 경우 해당 폴더 및 해당 하위 폴더 내의 페이지 또는 보기에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="0e7a4-167">`_ViewImports` 파일은 루트 수준부터 처리된 후 각 폴더의 페이지 또는 보기 자체의 위치까지 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="0e7a4-168">루트 수준에 지정된 `_ViewImports` 설정은 폴더 수준에서 재정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="0e7a4-169">예를 들어 다음을 가정해 보세요.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-169">For example, suppose:</span></span>

* <span data-ttu-id="0e7a4-170">루트 수준 *_ViewImports.cshtml* 파일에 `@model MyModel1` 및 `@addTagHelper *, MyTagHelper1`이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="0e7a4-171">하위 폴더 *_ViewImports.cshtml* 파일에 `@model MyModel2` 및 `@addTagHelper *, MyTagHelper2`이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="0e7a4-172">하위 폴더의 페이지 및 보기는 태그 도우미와 `MyModel2` 모델에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="0e7a4-173">파일 계층 구조에 *_ViewImports.cshtml* 파일이 여러 개 있을 경우 지시문의 결합된 동작은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="0e7a4-174">`@addTagHelper`, `@removeTagHelper`: 순서대로 모두 실행</span><span class="sxs-lookup"><span data-stu-id="0e7a4-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="0e7a4-175">`@tagHelperPrefix`: 뷰에 가장 가까운 것이 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="0e7a4-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="0e7a4-176">`@model`: 뷰에 가장 가까운 것이 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="0e7a4-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="0e7a4-177">`@inherits`: 뷰에 가장 가까운 것이 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="0e7a4-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="0e7a4-178">`@using`: 모두 포함됨. 중복 항목은 무시됨</span><span class="sxs-lookup"><span data-stu-id="0e7a4-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="0e7a4-179">`@inject`: 각 속성에 대해 뷰에 가장 가까운 것이 같은 속성 이름의 다른 것보다 우선함</span><span class="sxs-lookup"><span data-stu-id="0e7a4-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="0e7a4-180">각 뷰 이전에 코드 실행</span><span class="sxs-lookup"><span data-stu-id="0e7a4-180">Running Code Before Each View</span></span>

<span data-ttu-id="0e7a4-181">*_ViewStart.cshtml* 파일에 각 보기 또는 페이지를 배치하기 전에 실행되어야 하는 코드.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="0e7a4-182">규칙에 따라, *_ViewStart.cshtml* 파일은 *Pages* (또는 *Views* ) 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="0e7a4-183">*_ViewStart.cshtml* 에 나열된 문은 모든 전체 뷰(레이아웃 및 부분 뷰가 아님) 이전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="0e7a4-184">[ViewImports.cshtml](xref:mvc/views/layout#viewimports)처럼 *_ViewStart.cshtml* 은 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="0e7a4-185">*_ViewStart.cshtml* 파일이 보기 또는 페이지 폴더에 정의된 경우 *Pages* (또는 *Views* ) 폴더의 루트에 정의된 항목 뒤에 실행됩니다(있는 경우).</span><span class="sxs-lookup"><span data-stu-id="0e7a4-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views* ) folder (if any).</span></span>

<span data-ttu-id="0e7a4-186">샘플 *_ViewStart.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="0e7a4-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="0e7a4-187">위의 파일은 모든 뷰가 *_Layout.cshtml* 레이아웃을 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="0e7a4-188">*_ViewStart.cshtml* 및 *_ViewImports.cshtml* 은 일반적으로 */Pages/Shared* (또는 */Views/Shared* ) 폴더에 배치되지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="0e7a4-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared* ) folder.</span></span> <span data-ttu-id="0e7a4-189">이러한 파일의 앱 수준 버전은 */Pages* (또는 */Views* ) 폴더에 직접 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0e7a4-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views* ) folder.</span></span>
