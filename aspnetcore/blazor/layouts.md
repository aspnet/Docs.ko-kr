---
title: ASP.NET Core Blazor 레이아웃
author: guardrex
description: Blazor 앱의 재사용 가능한 레이아웃 구성 요소를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
ms.openlocfilehash: 417f69e797296cdcd01fc4ce326388512a406368
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058274"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="8a41c-103">ASP.NET Core Blazor 레이아웃</span><span class="sxs-lookup"><span data-stu-id="8a41c-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="8a41c-104">작성자: [Rainer Stropek](https://www.timecockpit.com) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8a41c-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8a41c-105">메뉴, 저작권 메시지, 회사 로고 등의 일부 앱 요소는 일반적으로 앱의 전체 레이아웃에 포함되며 앱의 모든 구성 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="8a41c-106">이러한 요소의 코드를 앱의 모든 구성 요소에 복사하는 것은 효율적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="8a41c-107">요소 중 하나에 업데이트가 필요할 때마다 모든 구성 요소를 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="8a41c-108">해당 중복은 유지 관리하기 어렵고, 시간이 지남에 따라 일관성 없는 콘텐츠가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="8a41c-109">‘레이아웃’을 통해 이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="8a41c-110">기술적으로 레이아웃은 또 다른 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="8a41c-111">레이아웃은 Razor 템플릿 또는 C# 코드에서 정의되며 [데이터 바인딩](xref:blazor/components/data-binding), [종속성 주입](xref:blazor/fundamentals/dependency-injection) 및 기타 구성 요소 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span> <span data-ttu-id="8a41c-112">레이아웃은 [`@page`](xref:mvc/views/razor#page) 지시문이 있는 라우팅할 수 있는 Razor 구성 요소에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-112">Layouts only apply to routable Razor components that have [`@page`](xref:mvc/views/razor#page) directives.</span></span>

<span data-ttu-id="8a41c-113">구성 요소를 레이아웃으로 변환하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-113">To convert a component into a layout:</span></span>

* <span data-ttu-id="8a41c-114"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>에서 구성 요소를 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-114">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="8a41c-115"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase>는 레이아웃 내부에 렌더링된 콘텐츠의 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-115">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="8a41c-116">Razor 구문 `@Body`를 사용하여 콘텐츠가 렌더링되는 위치를 레이아웃 태그에 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-116">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="8a41c-117">다음 코드 샘플은 레이아웃 구성 요소인 `MainLayout.razor`의 Razor 템플릿을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-117">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="8a41c-118">레이아웃은 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>를 상속하고 탐색 모음과 바닥글 사이에 `@Body`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-118">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="8a41c-119">`MainLayout` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="8a41c-119">`MainLayout` component</span></span>

<span data-ttu-id="8a41c-120">Blazor 프로젝트 템플릿 중 하나를 기반으로 하는 앱에서 `MainLayout` 구성 요소(`MainLayout.razor`)는 앱의 `Shared` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-120">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="8a41c-121">기본 레이아웃</span><span class="sxs-lookup"><span data-stu-id="8a41c-121">Default layout</span></span>

<span data-ttu-id="8a41c-122">앱의 `App.razor` 파일에 있는 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에서 기본 앱 레이아웃을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-122">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="8a41c-123">기본 Blazor 템플릿에서 제공하는 다음 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 `MainLayout` 구성 요소에 기본 레이아웃을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-123">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="8a41c-124"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠의 기본 레이아웃을 제공하려면 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠에 대해 <xref:Microsoft.AspNetCore.Components.LayoutView>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-124">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="8a41c-125"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대한 자세한 내용은 <xref:blazor/fundamentals/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8a41c-125">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="8a41c-126">라우터에서 레이아웃을 기본 레이아웃으로 지정하는 경우 구성 요소별 또는 폴더별로 재정의할 수 있기 때문에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-126">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="8a41c-127">가장 일반적인 방법이므로 라우터를 사용하여 앱의 기본 레이아웃을 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-127">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="8a41c-128">구성 요소에서 레이아웃 지정</span><span class="sxs-lookup"><span data-stu-id="8a41c-128">Specify a layout in a component</span></span>

<span data-ttu-id="8a41c-129">[`@layout`](xref:mvc/views/razor#layout) Razor 지시문을 사용하여 [`@page`](xref:mvc/views/razor#page) 지시문을 포함하는 라우팅 가능한 Razor 구성 요소에 레이아웃을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-129">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that also has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="8a41c-130">컴파일러는 `@layout`을 구성 요소 클래스에 적용되는 <xref:Microsoft.AspNetCore.Components.LayoutAttribute>로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-130">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="8a41c-131">다음 `MasterList` 구성 요소의 콘텐츠는 `MasterLayout`의 `@Body` 위치에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-131">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="8a41c-132">구성 요소에서 직접 레이아웃을 지정하면 라우터에서 설정된 ‘기본 레이아웃’이나 `_Imports.razor`에서 가져온 `@layout` 지시문이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-132">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="8a41c-133">중앙에서 레이아웃 선택</span><span class="sxs-lookup"><span data-stu-id="8a41c-133">Centralized layout selection</span></span>

<span data-ttu-id="8a41c-134">앱의 모든 폴더에 이름이 `_Imports.razor`인 템플릿 파일을 선택적으로 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-134">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="8a41c-135">컴파일러는 imports 파일에 지정된 지시문을 동일한 폴더와 모든 하위 폴더의 모든 Razor 템플릿에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-135">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="8a41c-136">따라서 `_Imports.razor` 파일에 `@layout MyCoolLayout`을 포함하면 폴더의 모든 구성 요소가 `MyCoolLayout`을 사용하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-136">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="8a41c-137">폴더와 하위 폴더에 있는 모든 `.razor` 파일에 `@layout MyCoolLayout`을 반복적으로 추가할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-137">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="8a41c-138">`@using` 지시문은 구성 요소에도 동일한 방식으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-138">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="8a41c-139">다음 `_Imports.razor` 파일은 아래 항목을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-139">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="8a41c-140">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="8a41c-140">`MyCoolLayout`.</span></span>
* <span data-ttu-id="8a41c-141">동일한 폴더와 하위 폴더에 있는 모든 Razor 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="8a41c-141">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="8a41c-142">`BlazorApp1.Data` 네임스페이스.</span><span class="sxs-lookup"><span data-stu-id="8a41c-142">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="8a41c-143">`_Imports.razor` 파일은 [Razor 뷰 및 Pages용 _ViewImports.cshtml 파일](xref:mvc/views/layout#importing-shared-directives)과 유사하지만, 구체적으로 Razor 구성 요소 파일에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-143">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="8a41c-144">`_Imports.razor`에서 레이아웃을 지정하면 라우터의 ‘기본 레이아웃’으로 지정된 레이아웃이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-144">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="8a41c-145">루트 `_Imports.razor` 파일에 Razor `@layout` 지시어를 추가하지 **마세요**. 그러면 앱에서 레이아웃의 무한 루프가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-145">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="8a41c-146">기본 앱 레이아웃을 제어하려면 `Router` 구성 요소에서 레이아웃을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-146">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="8a41c-147">자세한 내용은 [기본 레이아웃](#default-layout) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8a41c-147">For more information, see the [Default layout](#default-layout) section.</span></span>

> [!NOTE]
> <span data-ttu-id="8a41c-148">[`@layout`](xref:mvc/views/razor#layout) Razor 지시문은 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 라우팅할 수 있는 Razor 구성 요소에만 레이아웃을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-148">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with [`@page`](xref:mvc/views/razor#page) directives.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="8a41c-149">중첩된 레이아웃</span><span class="sxs-lookup"><span data-stu-id="8a41c-149">Nested layouts</span></span>

<span data-ttu-id="8a41c-150">앱은 중첩된 레이아웃으로 구성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-150">Apps can consist of nested layouts.</span></span> <span data-ttu-id="8a41c-151">구성 요소는 레이아웃을 참조할 수 있고, 레이아웃은 자기 다른 레이아웃을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-151">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="8a41c-152">예를 들어 레이아웃 중첩은 여러 수준의 메뉴 구조를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-152">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="8a41c-153">다음 예제에서는 중첩된 레이아웃을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-153">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="8a41c-154">`EpisodesComponent.razor` 파일은 표시할 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-154">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="8a41c-155">이 구성 요소는 `MasterListLayout`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-155">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="8a41c-156">`MasterListLayout.razor` 파일은 `MasterListLayout`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-156">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="8a41c-157">레이아웃은 해당 레이아웃이 렌더링되는 다른 레이아웃인 `MasterLayout`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-157">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="8a41c-158">`EpisodesComponent`는 `@Body`가 표시되는 위치에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-158">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="8a41c-159">최종적으로, `MasterLayout.razor`의 `MasterLayout`에는 헤더, 주 메뉴, 바닥글 등의 최상위 레이아웃 요소가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-159">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="8a41c-160">`EpisodesComponent`를 포함하는 `MasterListLayout`은 `@Body`가 표시되는 위치에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-160">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="8a41c-161">통합 구성 요소와 Razor Pages 레이아웃 공유</span><span class="sxs-lookup"><span data-stu-id="8a41c-161">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="8a41c-162">라우팅 가능한 구성 요소가 Razor Pages 앱에 통합된 경우 구성 요소와 함께 앱의 공유 레이아웃을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8a41c-162">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="8a41c-163">자세한 내용은 <xref:blazor/components/prerendering-and-integration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8a41c-163">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a41c-164">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8a41c-164">Additional resources</span></span>

* <xref:mvc/views/layout>
