---
title: ASP.NET Core Blazor 레이아웃
author: guardrex
description: Blazor 앱의 재사용 가능한 레이아웃 구성 요소를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2021
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
uid: blazor/layouts
ms.openlocfilehash: 9f3400b766a043fdf3872838bffe392eddba4049
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102394951"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="f9158-103">ASP.NET Core Blazor 레이아웃</span><span class="sxs-lookup"><span data-stu-id="f9158-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="f9158-104">메뉴, 저작권 메시지, 회사 로고 등의 일부 앱 요소는 일반적으로 앱의 전반적인 표현의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall presentation.</span></span> <span data-ttu-id="f9158-105">이러한 요소의 태그 복사본을 앱의 모든 구성 요소에 배치하는 것은 효율적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-105">Placing a copy of the markup for these elements into all of the components of an app isn't efficient.</span></span> <span data-ttu-id="f9158-106">요소 중 하나가 업데이트될 때마다 해당 요소를 사용하는 모든 구성 요소를 업데이트해야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-106">Every time that one of these elements is updated, every component that uses the element must be updated.</span></span> <span data-ttu-id="f9158-107">이 방법은 유지 관리 비용이 많이 들고 업데이트가 누락된 경우 일관성 없는 콘텐츠가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-107">This approach is costly to maintain and can lead to inconsistent content if an update is missed.</span></span> <span data-ttu-id="f9158-108">‘레이아웃’은 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-108">*Layouts* solve these problems.</span></span>

<span data-ttu-id="f9158-109">Blazor 레이아웃은 Razor 구성 요소이며 태그와 태그를 참조하는 구성 요소를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-109">A Blazor layout is a Razor component that shares markup with components that reference it.</span></span> <span data-ttu-id="f9158-110">레이아웃은 [데이터 바인딩](xref:blazor/components/data-binding), [종속성 주입](xref:blazor/fundamentals/dependency-injection), 구성 요소의 기타 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-110">Layouts can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other features of components.</span></span>

## <a name="layout-components"></a><span data-ttu-id="f9158-111">레이아웃 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f9158-111">Layout components</span></span>

### <a name="create-a-layout-component"></a><span data-ttu-id="f9158-112">레이아웃 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="f9158-112">Create a layout component</span></span>

<span data-ttu-id="f9158-113">레이아웃 구성 요소를 만들려면:</span><span class="sxs-lookup"><span data-stu-id="f9158-113">To create a layout component:</span></span>

* <span data-ttu-id="f9158-114">Razor 템플릿 또는 C# 코드로 정의된 Razor 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-114">Create a Razor component defined by a Razor template or C# code.</span></span> <span data-ttu-id="f9158-115">Razor 템플릿을 기반으로 하는 레이아웃 구성 요소는 일반 Razor 구성 요소와 마찬가지로 `.razor` 파일 확장자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-115">Layout components based on a Razor template use the `.razor` file extension just like ordinary Razor components.</span></span> <span data-ttu-id="f9158-116">레이아웃 구성 요소는 앱의 구성 요소 전체에서 공유되기 때문에 일반적으로 앱의 `Shared` 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-116">Because layout components are shared across an app's components, they're usually placed in the app's `Shared` folder.</span></span> <span data-ttu-id="f9158-117">그러나 레이아웃은 이를 사용하는 구성 요소에 액세스할 수 있는 곳이면 어디에나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-117">However, layouts can be placed in any location accessible to the components that use it.</span></span> <span data-ttu-id="f9158-118">예를 들어 레이아웃을 사용하는 구성 요소와 동일한 폴더에 레이아웃을 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-118">For example, a layout can be placed in the same folder as the components that use it.</span></span>
* <span data-ttu-id="f9158-119"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>에서 구성 요소를 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-119">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="f9158-120"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>는 레이아웃 내부에 렌더링된 콘텐츠의 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 속성(<xref:Microsoft.AspNetCore.Components.RenderFragment> 형식)을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-120">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property (<xref:Microsoft.AspNetCore.Components.RenderFragment> type) for the rendered content inside the layout.</span></span>
* <span data-ttu-id="f9158-121">Razor 구문 `@Body`를 사용하여 콘텐츠가 렌더링되는 위치를 레이아웃 태그에 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-121">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="f9158-122">다음 `DoctorWhoLayout` 구성 요소는 레이아웃 구성 요소의 Razor 템플릿을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-122">The following `DoctorWhoLayout` component shows the Razor template of a layout component.</span></span> <span data-ttu-id="f9158-123">레이아웃은 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>를 상속하고 탐색 모음(`<nav>...</nav>`)과 바닥글(`<footer>...</footer>`) 사이에 `@Body`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-123">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar (`<nav>...</nav>`) and the footer (`<footer>...</footer>`).</span></span>

<span data-ttu-id="f9158-124">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-124">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a><span data-ttu-id="f9158-125">`MainLayout` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f9158-125">`MainLayout` component</span></span>

<span data-ttu-id="f9158-126">[Blazor 프로젝트 템플릿](xref:blazor/project-structure)에서 만든 앱에서 `MainLayout` 구성 요소는 앱의 [기본 레이아웃](#apply-a-default-layout-to-an-app)입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-126">In an app created from a [Blazor project template](xref:blazor/project-structure), the `MainLayout` component is the app's [default layout](#apply-a-default-layout-to-an-app).</span></span>

<span data-ttu-id="f9158-127">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-127">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

<span data-ttu-id="f9158-128">[Blazor의 CSS 격리 기능](xref:blazor/components/css-isolation)은 격리된 CSS 스타일을 `MainLayout` 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-128">[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component.</span></span> <span data-ttu-id="f9158-129">규칙에 따라 스타일은 동일한 이름의 동반 스타일시트 `Shared/MainLayout.razor.css`에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-129">By convention, the styles are provided by the accompanying stylesheet of the same name, `Shared/MainLayout.razor.css`.</span></span> <span data-ttu-id="f9158-130">스타일시트의 ASP.NET Core 프레임워크 구현은 [ASP.NET Core 참조 원본(dotnet/aspnetcore GitHub 리포지토리)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css)에서 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-130">The ASP.NET Core framework implementation of the stylesheet is available for inspection in the [ASP.NET Core reference source (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span></span>

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a><span data-ttu-id="f9158-131">레이아웃 적용</span><span class="sxs-lookup"><span data-stu-id="f9158-131">Apply a layout</span></span>

### <a name="apply-a-layout-to-a-component"></a><span data-ttu-id="f9158-132">구성 요소에 레이아웃 적용</span><span class="sxs-lookup"><span data-stu-id="f9158-132">Apply a layout to a component</span></span>

<span data-ttu-id="f9158-133">[`@layout`](xref:mvc/views/razor#layout) Razor 지시문을 사용하여 [`@page`](xref:mvc/views/razor#page) 지시문을 포함하는 라우팅 가능한 Razor 구성 요소에 레이아웃을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-133">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="f9158-134">컴파일러는 `@layout`을 <xref:Microsoft.AspNetCore.Components.LayoutAttribute>로 변환하고 구성 요소 클래스에 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-134">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute> and applies the attribute to the component class.</span></span>

<span data-ttu-id="f9158-135">다음 `Episodes` 구성 요소의 콘텐츠는 `DoctorWhoLayout`의 `@Body` 위치에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-135">The content of the following `Episodes` component is inserted into the `DoctorWhoLayout` at the position of `@Body`.</span></span>

<span data-ttu-id="f9158-136">`Pages/Episodes.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-136">`Pages/Episodes.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="f9158-137">다음의 렌더링된 HTML 태그는 이전 `DoctorWhoLayout` 및 `Episodes` 구성 요소에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-137">The following rendered HTML markup is produced by the preceding `DoctorWhoLayout` and `Episodes` component.</span></span> <span data-ttu-id="f9158-138">관련된 두 가지 구성 요소에서 제공하는 콘텐츠에 초점을 맞추기 위해 관련되지 않은 태그는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-138">Extraneous markup doesn't appear in order to focus on the content provided by the two components involved:</span></span>

* <span data-ttu-id="f9158-139">머리글(`<header>...</header>`)의 **Doctor Who&trade; Episode Database** 제목(`<h1>...</h1>`), 탐색 모음(`<nav>...</nav>`), 바닥글(`<footer>...</footer>`)의 상표 정보 요소(`<div>...</div>`)는 `DoctorWhoLayout` 구성 요소에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-139">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`) in the header (`<header>...</header>`), navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) in the footer (`<footer>...</footer>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="f9158-140">**Episodes** 제목(`<h2>...</h2>`)과 에피소드 목록(`<ul>...</ul>`)은 `Episodes` 구성 요소에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-140">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

```html
<body>
    <div id="app">
        <header>
            <h1>Doctor Who&trade; Episode Database</h1>
        </header>

        <nav>
            <a href="masterlist">Master Episode List</a>
            <a href="search">Search</a>
            <a href="new">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <footer>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </footer>
    </div>
</body>
```

<span data-ttu-id="f9158-141">구성 요소에서 직접 레이아웃을 지정하면 ‘기본 레이아웃’이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-141">Specifying the layout directly in a component overrides a *default layout*:</span></span>

* <span data-ttu-id="f9158-142">다음의 [구성 요소 폴더에 레이아웃 적용](#apply-a-layout-to-a-folder-of-components) 섹션에 설명된 대로 `_Imports` 구성 요소(`_Imports.razor`)에서 가져온 `@layout` 지시문으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-142">Set by an `@layout` directive imported from an `_Imports` component (`_Imports.razor`), as described in the following [Apply a layout to a folder of components](#apply-a-layout-to-a-folder-of-components) section.</span></span>
* <span data-ttu-id="f9158-143">이 문서의 뒷부분에 있는 [앱에 기본 레이아웃 적용](#apply-a-default-layout-to-an-app) 섹션에 설명된 대로 앱의 기본 레이아웃으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-143">Set as the app's default layout, as described in the [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section later in this article.</span></span>

### <a name="apply-a-layout-to-a-folder-of-components"></a><span data-ttu-id="f9158-144">구성 요소 폴더에 레이아웃 적용</span><span class="sxs-lookup"><span data-stu-id="f9158-144">Apply a layout to a folder of components</span></span>

<span data-ttu-id="f9158-145">앱의 모든 폴더에 이름이 `_Imports.razor`인 템플릿 파일을 선택적으로 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-145">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="f9158-146">컴파일러는 imports 파일에 지정된 지시문을 동일한 폴더와 모든 하위 폴더의 모든 Razor 템플릿에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-146">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="f9158-147">따라서 `_Imports.razor` 파일에 `@layout DoctorWhoLayout`을 포함하면 폴더의 모든 구성 요소가 `DoctorWhoLayout` 구성 요소를 사용하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-147">Therefore, an `_Imports.razor` file containing `@layout DoctorWhoLayout` ensures that all of the components in a folder use the `DoctorWhoLayout` component.</span></span> <span data-ttu-id="f9158-148">폴더와 하위 폴더에 있는 모든 Razor 구성 요소(`.razor`)에 `@layout DoctorWhoLayout`을 반복적으로 추가할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-148">There's no need to repeatedly add `@layout DoctorWhoLayout` to all of the Razor components (`.razor`) within the folder and subfolders.</span></span>

<span data-ttu-id="f9158-149">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-149">`_Imports.razor`:</span></span>

```razor
@layout DoctorWhoLayout
...
```

<span data-ttu-id="f9158-150">`_Imports.razor` 파일은 [Razor 뷰 및 Pages용 _ViewImports.cshtml 파일](xref:mvc/views/layout#importing-shared-directives)과 유사하지만, 구체적으로 Razor 구성 요소 파일에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-150">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="f9158-151">`_Imports.razor`에서 레이아웃을 지정하면 라우터의 [기본 앱 레이아웃](#apply-a-default-layout-to-an-app)으로 지정된 레이아웃이 재정의됩니다. 자세한 내용은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-151">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's [default app layout](#apply-a-default-layout-to-an-app), which is described in the following section.</span></span>

> [!WARNING]
> <span data-ttu-id="f9158-152">Razor `@layout` 지시문을 루트 `_Imports.razor` 파일에 추가하지 **마세요**. 레이아웃의 무한 루프가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-152">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts.</span></span> <span data-ttu-id="f9158-153">기본 앱 레이아웃을 제어하려면 `Router` 구성 요소에서 레이아웃을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-153">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="f9158-154">자세한 내용은 다음의 [앱에 기본 레이아웃 적용](#apply-a-default-layout-to-an-app) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9158-154">For more information, see the following [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section.</span></span>

> [!NOTE]
> <span data-ttu-id="f9158-155">[`@layout`](xref:mvc/views/razor#layout) Razor 지시문은 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 라우팅할 수 있는 Razor 구성 요소에만 레이아웃을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-155">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>

### <a name="apply-a-default-layout-to-an-app"></a><span data-ttu-id="f9158-156">앱에 기본 레이아웃 적용</span><span class="sxs-lookup"><span data-stu-id="f9158-156">Apply a default layout to an app</span></span>

<span data-ttu-id="f9158-157">`App` 구성 요소의 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에서 기본 앱 레이아웃을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-157">Specify the default app layout in the in the `App` component's <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="f9158-158">[Blazor 프로젝트 템플릿](xref:blazor/project-structure)을 기반으로 하는 앱의 다음 예제는 `MainLayout` 구성 요소에 대한 기본 레이아웃을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-158">The following example from an app based on a [Blazor project template](xref:blazor/project-structure) sets the default layout to the `MainLayout` component.</span></span>

<span data-ttu-id="f9158-159">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-159">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="f9158-160"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대한 자세한 내용은 <xref:blazor/fundamentals/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9158-160">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="f9158-161">이 문서의 이전 섹션에서 설명한 대로, 구성 요소 또는 폴더별로 레이아웃을 재정의할 수 있으므로 `Router` 구성 요소에서 레이아웃을 기본 레이아웃으로 지정하는 것은 유용한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-161">Specifying the layout as a default layout in the `Router` component is a useful practice because you can override the layout on a per-component or per-folder basis, as described in the preceding sections of this article.</span></span> <span data-ttu-id="f9158-162">`Router` 구성 요소를 사용하여 앱의 기본 레이아웃을 설정하는 것이 좋습니다. 이것이 레이아웃을 사용하는 가장 일반적이고 유연한 접근 방식이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-162">We recommend using the `Router` component to set the app's default layout because it's the most general and flexible approach for using layouts.</span></span>

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a><span data-ttu-id="f9158-163">임의의 콘텐츠에 레이아웃 적용(`LayoutView` 구성 요소)</span><span class="sxs-lookup"><span data-stu-id="f9158-163">Apply a layout to arbitrary content (`LayoutView` component)</span></span>

<span data-ttu-id="f9158-164">임의의 Razor 템플릿 콘텐츠에 대한 레이아웃을 설정하려면 <xref:Microsoft.AspNetCore.Components.LayoutView> 구성 요소를 사용하여 레이아웃을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-164">To set a layout for arbitrary Razor template content, specify the layout with a <xref:Microsoft.AspNetCore.Components.LayoutView> component.</span></span> <span data-ttu-id="f9158-165">모든 Razor 구성 요소에서 <xref:Microsoft.AspNetCore.Components.LayoutView>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-165">You can use a <xref:Microsoft.AspNetCore.Components.LayoutView> in any Razor component.</span></span> <span data-ttu-id="f9158-166">다음 예제에서는 `MainLayout` 구성 요소의 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 템플릿(`<NotFound>...</NotFound>`)에 대해 이름이 `ErrorLayout`인 레이아웃 구성 요소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-166">The following example sets a layout component named `ErrorLayout` for the `MainLayout` component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template (`<NotFound>...</NotFound>`).</span></span>

<span data-ttu-id="f9158-167">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-167">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a><span data-ttu-id="f9158-168">중첩된 레이아웃</span><span class="sxs-lookup"><span data-stu-id="f9158-168">Nested layouts</span></span>

<span data-ttu-id="f9158-169">구성 요소는 레이아웃을 참조할 수 있고 레이아웃은 다른 레이아웃을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-169">A component can reference a layout that in turn references another layout.</span></span> <span data-ttu-id="f9158-170">예를 들어 중첩된 레이아웃은 여러 수준의 메뉴 구조를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-170">For example, nested layouts are used to create a multi-level menu structures.</span></span>

<span data-ttu-id="f9158-171">다음 예제에서는 중첩된 레이아웃을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-171">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="f9158-172">[구성 요소에 레이아웃 적용](#apply-a-layout-to-a-component) 섹션에 표시된 `Episodes` 구성 요소는 표시할 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-172">The `Episodes` component shown in the [Apply a layout to a component](#apply-a-layout-to-a-component) section is the component to display.</span></span> <span data-ttu-id="f9158-173">이 구성 요소는 `DoctorWhoLayout` 구성 요소를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-173">The component references the `DoctorWhoLayout` component.</span></span>

<span data-ttu-id="f9158-174">다음 `DoctorWhoLayout` 구성 요소는 이 문서의 앞부분에 표시된 예제의 수정된 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-174">The following `DoctorWhoLayout` component is a modified version of the example shown earlier in this article.</span></span> <span data-ttu-id="f9158-175">머리글 및 바닥글 요소가 제거되고 레이아웃은 다른 레이아웃인 `ProductionsLayout`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-175">The header and footer elements are removed, and the layout references another layout, `ProductionsLayout`.</span></span> <span data-ttu-id="f9158-176">`DoctorWhoLayout`에서 `@Body`가 나타나는 곳에서 `Episodes` 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-176">The `Episodes` component is rendered where `@Body` appears in the `DoctorWhoLayout`.</span></span>

<span data-ttu-id="f9158-177">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-177">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

<span data-ttu-id="f9158-178">`ProductionsLayout` 구성 요소는 이제 머리글(`<header>...</header>`) 및 바닥글(`<footer>...</footer>`) 요소가 있는 최상위 레이아웃 요소를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-178">The `ProductionsLayout` component contains the top-level layout elements, where the header (`<header>...</header>`) and footer (`<footer>...</footer>`) elements now reside.</span></span> <span data-ttu-id="f9158-179">`Episodes` 구성 요소가 있는 `DoctorWhoLayout`은 `@Body`가 나타나는 곳에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-179">The `DoctorWhoLayout` with the `Episodes` component is rendered where `@Body` appears.</span></span>

<span data-ttu-id="f9158-180">`Shared/ProductionsLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9158-180">`Shared/ProductionsLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

<span data-ttu-id="f9158-181">렌더링된 다음 HTML 태그는 이전의 중첩된 레이아웃에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-181">The following rendered HTML markup is produced by the preceding nested layout.</span></span> <span data-ttu-id="f9158-182">관련된 세 가지 구성 요소에서 제공하는 중첩된 콘텐츠에 초점을 맞추기 위해 관련되지 않은 태그는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-182">Extraneous markup doesn't appear in order to focus on the nested content provided by the three components involved:</span></span>

* <span data-ttu-id="f9158-183">머리글(`<header>...</header>`), 프로덕션 탐색 모음(`<nav>...</nav>`), 바닥글(`<footer>...</footer>`) 요소와 해당 콘텐츠는 `ProductionsLayout` 구성 요소에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-183">The header (`<header>...</header>`), production navigation bar (`<nav>...</nav>`), and footer (`<footer>...</footer>`) elements and their content come from the `ProductionsLayout` component.</span></span>
* <span data-ttu-id="f9158-184">**Doctor Who&trade; Episode Database** 제목(`<h1>...</h1>`), 에피소드 탐색 모음(`<nav>...</nav>`), 상표 정보 요소(`<div>...</div>`)는 `DoctorWhoLayout` 구성 요소에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-184">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`), episode navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="f9158-185">**Episodes** 제목(`<h2>...</h2>`)과 에피소드 목록(`<ul>...</ul>`)은 `Episodes` 구성 요소에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-185">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

```html
<body>
    <div id="app">
        <header>
            <h1>Productions</h1>
        </header>

        <nav>
            <a href="master-production-list">Master Production List</a>
            <a href="production-search">Search</a>
            <a href="new-production">Add Production</a>
        </nav>

        <h1>Doctor Who&trade; Episode Database</h1>

        <nav>
            <a href="episode-masterlist">Master Episode List</a>
            <a href="episode-search">Search</a>
            <a href="new-episode">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <div>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </div>

        <footer>
            Footer of Productions Layout
        </footer>
    </div>
</body>
```

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="f9158-186">통합 구성 요소와 Razor Pages 레이아웃 공유</span><span class="sxs-lookup"><span data-stu-id="f9158-186">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="f9158-187">라우팅 가능한 구성 요소가 Razor Pages 앱에 통합된 경우 구성 요소와 함께 앱의 공유 레이아웃을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9158-187">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="f9158-188">자세한 내용은 <xref:blazor/components/prerendering-and-integration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9158-188">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9158-189">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9158-189">Additional resources</span></span>

* <xref:mvc/views/layout>
