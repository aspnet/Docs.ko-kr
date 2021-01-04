---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청 라우팅을 관리하는 방법과 Blazor 앱에서 NavLink 구성 요소를 사용하여 탐색하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: ec183f4aadc6bafd8e77f9d97291ba3d47bd92f5
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506931"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="95580-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="95580-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="95580-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="95580-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="95580-105">이 문서에서는 요청을 라우팅하는 방법과 Blazor 앱에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="95580-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="95580-106">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="95580-106">Route templates</span></span>

<span data-ttu-id="95580-107"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 Blazor 앱에서 Razor 구성 요소로 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="95580-108"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 Blazor 앱의 `App` 구성 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="95580-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="95580-110">[`@page` 지시문](xref:mvc/views/razor#page)을 포함하는 Razor 구성 요소(`.razor`)를 컴파일하면 생성된 구성요소 클래스에 구성 요소의 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="95580-111">앱이 시작하면 라우터의 `AppAssembly`로 지정된 어셈블리를 검사하여 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 있는 앱의 구성 요소에 대한 경로 정보를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="95580-112">런타임에 <xref:Microsoft.AspNetCore.Components.RouteView> 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="95580-113">경로 매개 변수와 함께 <xref:Microsoft.AspNetCore.Components.Routing.Router>에서 <xref:Microsoft.AspNetCore.Components.RouteData>를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="95580-114">추가로 중첩된 레이아웃을 포함하여 해당 [레이아웃](xref:blazor/layouts)과 함께 지정된 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="95580-115">필요한 경우, [`@layout` 지시문](xref:blazor/layouts#specify-a-layout-in-a-component)으로 레이아웃을 지정하지 않는 구성 요소에 레이아웃 클래스가 포함된 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="95580-116">프레임워크의 Blazor 프로젝트 템플릿은 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)를 앱의 기본 레이아웃으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="95580-117">레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="95580-118">구성 요소는 여러 [`@page` 지시문](xref:mvc/views/razor#page)을 사용하여 여러 경로 템플릿을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="95580-119">다음 예제 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="95580-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="95580-121">URL을 올바르게 확인하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 `<base>` 태그가 있어야 하고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="95580-122">자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="95580-123">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="95580-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="95580-124"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="95580-125">`App` 구성 요소에서 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소의 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 템플릿에 사용자 지정 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="95580-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="95580-127">다른 대화형 구성 요소와 같은 임의 항목이 `<NotFound>` 태그의 콘텐츠로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="95580-128"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts#default-layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="95580-129">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="95580-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="95580-130"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대해 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="95580-131">`AppAssembly`에 지정된 어셈블리 외에도 추가 어셈블리가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="95580-132">다음 예제에서 `Component1`은 참조된 [구성 요소 클래스 라이브러리](xref:blazor/components/class-libraries)에서 정의된 라우팅 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="95580-133">다음 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="95580-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="95580-135">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95580-135">Route parameters</span></span>

<span data-ttu-id="95580-136">라우터는 경로 매개 변수를 사용하여 해당 [구성 요소 매개 변수](xref:blazor/components/index#component-parameters)를 동일한 이름으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="95580-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="95580-137">경로 매개 변수 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="95580-138">다음 예제에서 `text` 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="95580-139">`/RouteParameter/amazing`에 대한 요청을 수행하면 `<h1>` 태그 콘텐츠가 `Blazor is amazing!`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="95580-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="95580-141">선택적 매개 변수가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-141">Optional parameters are supported.</span></span> <span data-ttu-id="95580-142">다음 예제에서 `text` 선택적 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="95580-143">세그먼트가 없으면 `Text` 값이 `fantastic`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="95580-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="95580-145">선택적 매개 변수는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="95580-146">다음 예제에서는 두 개의 [`@page` 지시문](xref:mvc/views/razor#page)이 적용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="95580-147">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="95580-148">두 번째 지시문은 `{text}` 경로 매개 변수 값을 구성 요소의 `Text` 속성에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="95580-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="95580-150">다른 선택적 매개 변수 값을 사용하여 동일한 구성 요소에 앱 탐색을 허용하려면 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 대신 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set)에 대해 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="95580-151">앞의 예제를 기반으로 하여, `/RouteParameter`에서 `/RouteParameter/amazing`으로, 또는 `/RouteParameter/amazing`에서 `/RouteParameter`로 이동할 수 있어야 할 때 `OnParametersSet`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="95580-152">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="95580-152">Route constraints</span></span>

<span data-ttu-id="95580-153">경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="95580-154">다음 예제에서 `User` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="95580-155">요청 URL에 `Id` 경로 세그먼트가 있는 경우</span><span class="sxs-lookup"><span data-stu-id="95580-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="95580-156">`Id` 세그먼트가 정수(`int`) 형식인 경우</span><span class="sxs-lookup"><span data-stu-id="95580-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="95580-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="95580-158">다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="95580-159">고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="95580-160">제약 조건</span><span class="sxs-lookup"><span data-stu-id="95580-160">Constraint</span></span> | <span data-ttu-id="95580-161">예제</span><span class="sxs-lookup"><span data-stu-id="95580-161">Example</span></span>           | <span data-ttu-id="95580-162">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="95580-162">Example Matches</span></span>                                                                  | <span data-ttu-id="95580-163">고정</span><span class="sxs-lookup"><span data-stu-id="95580-163">Invariant</span></span><br><span data-ttu-id="95580-164">culture</span><span class="sxs-lookup"><span data-stu-id="95580-164">culture</span></span><br><span data-ttu-id="95580-165">일치</span><span class="sxs-lookup"><span data-stu-id="95580-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="95580-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="95580-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="95580-167">아니요</span><span class="sxs-lookup"><span data-stu-id="95580-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="95580-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="95580-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="95580-169">예</span><span class="sxs-lookup"><span data-stu-id="95580-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="95580-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="95580-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="95580-171">예</span><span class="sxs-lookup"><span data-stu-id="95580-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="95580-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="95580-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="95580-173">예</span><span class="sxs-lookup"><span data-stu-id="95580-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="95580-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="95580-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="95580-175">예</span><span class="sxs-lookup"><span data-stu-id="95580-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="95580-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="95580-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="95580-177">아니요</span><span class="sxs-lookup"><span data-stu-id="95580-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="95580-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="95580-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="95580-179">예</span><span class="sxs-lookup"><span data-stu-id="95580-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="95580-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="95580-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="95580-181">예</span><span class="sxs-lookup"><span data-stu-id="95580-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="95580-182">CLR 형식(예: `int` 또는 <xref:System.DateTime>)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="95580-183">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="95580-184">점이 포함된 URL을 사용한 라우팅</span><span class="sxs-lookup"><span data-stu-id="95580-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="95580-185">호스트된 Blazor WebAssembly 및 Blazor Server 앱의 경우 서버 쪽 기본 경로 템플릿에서 요청 URL의 마지막 세그먼트에 파일을 요청하는 점(`.`)이 포함되어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="95580-186">예를 들어 URL `https://localhost.com:5001/example/some.thing`은 라우터에서 `some.thing`라는 파일에 대한 요청으로 해석됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="95580-187">`some.thing`이 [`@page` 지시문](xref:mvc/views/razor#page)을 사용하여 구성 요소에 라우팅되기 위한 것이고 `some.thing`이 경로 매개 변수 값인 경우 추가 구성이 없는 앱은 ‘404 - 찾을 수 없음’ 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="95580-188">점이 포함된 하나 이상의 매개 변수가 있는 경로를 사용하려면 앱에서 사용자 지정 템플릿을 사용하여 경로를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="95580-189">URL의 마지막 세그먼트에서 경로 매개 변수를 받을 수 있는 다음 `Example` 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="95580-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="95580-191">호스트된 Blazor WebAssembly 솔루션의 *`Server`* 앱이 `param` 경로 매개 변수에 점을 사용하여 요청을 라우팅하도록 허용하려면 `Startup.Configure`에서 선택적 매개 변수를 사용하여 대체 파일 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="95580-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="95580-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="95580-193">`param` 경로 매개 변수에서 점을 사용하여 요청을 라우팅하도록 Blazor Server 앱을 구성하려면 `Startup.Configure`에서 선택적 매개 변수를 사용하여 대체 페이지 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="95580-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="95580-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="95580-195">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="95580-196">모든 경로 매개 변수 catch</span><span class="sxs-lookup"><span data-stu-id="95580-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="95580-197">여러 폴더 경계에서 경로를 캡처하는 catch-all 경로 매개 변수는 구성 요소에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="95580-198">Catch-all 경로 매개 변수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="95580-199">이름이 경로 세그먼트 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-199">Named to match the route segment name.</span></span> <span data-ttu-id="95580-200">이름 지정에서 대소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="95580-201">`string` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-201">A `string` type.</span></span> <span data-ttu-id="95580-202">프레임워크가 자동 캐스팅을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="95580-203">URL의 끝부분에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-203">At the end of the URL.</span></span>

<span data-ttu-id="95580-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="95580-205">`/catch-all/{*pageRoute}`의 경로 템플릿이 있는 URL `/catch-all/this/is/a/test`의 경우 `PageRoute`의 값이 `this/is/a/test`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="95580-206">캡처된 경로의 슬래시 및 세그먼트가 디코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="95580-207">`/catch-all/{*pageRoute}`의 경로 템플릿의 경우 `/catch-all/this/is/a%2Ftest%2A` URL은 `this/is/a/test*`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="95580-208">catch-all 경로 매개 변수는 ASP.NET Core 5.0 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="95580-209">자세한 내용을 보려면 이 문서의 5.0 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="95580-210">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="95580-210">URI and navigation state helpers</span></span>

<span data-ttu-id="95580-211"><xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 C# 코드로 URI와 탐색을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="95580-212"><xref:Microsoft.AspNetCore.Components.NavigationManager>는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="95580-213">멤버</span><span class="sxs-lookup"><span data-stu-id="95580-213">Member</span></span> | <span data-ttu-id="95580-214">설명</span><span class="sxs-lookup"><span data-stu-id="95580-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="95580-215">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="95580-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="95580-216">절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="95580-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="95580-217">일반적으로 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>는 `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="95580-218">지정한 URI로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-218">Navigates to the specified URI.</span></span> <span data-ttu-id="95580-219">`forceLoad`가 `true`인 경우</span><span class="sxs-lookup"><span data-stu-id="95580-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="95580-220">클라이언트 쪽 라우팅이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="95580-221">클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="95580-222">탐색 위치가 변경된 경우에 발생하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="95580-223">상대 URI를 절대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="95580-224">기본 URI(예: 이전에 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="95580-225"><xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 이벤트의 경우 <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>는 탐색 이벤트에 대해 다음 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="95580-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: 새 위치의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="95580-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: `true`인 경우 Blazor는 브라우저에서 탐색을 가로챘습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="95580-228">`false`인 경우 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>에서 탐색이 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="95580-229">다음 구성 요소는</span><span class="sxs-lookup"><span data-stu-id="95580-229">The following component:</span></span>

* <span data-ttu-id="95580-230"><xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>를 사용하여 단추를 선택하면 앱의 `Counter` 구성 요소(`Pages/Counter.razor`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="95580-231"><xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>를 구독하여 위치 변경 이벤트를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="95580-232">`HandleLocationChanged` 메서드는 프레임워크에서 `Dispose`를 호출할 때 언후크됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="95580-233">메서드를 언후크하면 구성 요소의 가비지 수집이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="95580-234">로거 구현은 단추를 선택할 때 다음 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="95580-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="95580-236">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="95580-237">쿼리 문자열 및 구문 분석 매개 변수</span><span class="sxs-lookup"><span data-stu-id="95580-237">Query string and parse parameters</span></span>

<span data-ttu-id="95580-238">요청의 쿼리 문자열은 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="95580-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="95580-239">쿼리 문자열의 매개 변수를 구문 분석하려면</span><span class="sxs-lookup"><span data-stu-id="95580-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="95580-240">앱은 <xref:Microsoft.AspNetCore.WebUtilities> API를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="95580-241">앱에서 API를 사용할 수 없는 경우 [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)에 대한 앱의 프로젝트 파일에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="95580-242"><xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>을 사용하여 쿼리 문자열을 구문 분석한 후 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="95580-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="95580-243">다음 `ParseQueryString` 구성 요소 예제에서는 `ship`이라는 쿼리 문자열 매개 변수 키를 구문 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="95580-244">예를 들어 URL 쿼리 문자열 키-값 쌍 `?ship=Tardis`는 `queryValue`에서 `Tardis` 값을 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="95580-245">다음 예제에서는 URL `https://localhost:5001/parse-query-string?ship=Tardis`를 사용하여 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="95580-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="95580-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="95580-247">`NavLink` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95580-247">`NavLink` component</span></span>

<span data-ttu-id="95580-248">탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="95580-249"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="95580-250">`active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="95580-251">현재 경로가 `href`와 일치하는 경우, 필요에 따라 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>에 CSS 클래스 이름을 할당하여 렌더링된 링크에 사용자 지정 CSS 클래스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="95580-252">다음 `NavMenu` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하는 방법을 보여 주는 [`Bootstrap`](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="95580-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="95580-253">`NavMenu` 구성 요소(`NavMenu.razor`)는 Blazor 프로젝트 템플릿에서 생성된 앱의 `Shared` 폴더에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="95580-254">`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="95580-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: 현재 URL 전체와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="95580-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(기본값): 현재 URL의 접두사와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="95580-257">위의 예제에서 홈 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="95580-258">두 번째 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>는 사용자가 `component` 접두사를 포함하는 URL(예: `https://localhost:5001/component` 및 `https://localhost:5001/component/another-segment`)을 방문할 때 `active` 클래스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="95580-259">추가 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="95580-260">다음 예제에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `target` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="95580-261">다음 HTML 태그가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="95580-262">Blazor가 자식 콘텐츠를 렌더링하는 방식 때문에 `for` 루프 내에서 `NavLink` 구성 요소를 렌더링하려면 `NavLink`(자식) 구성 요소 콘텐츠에서 증분 루프 변수를 사용할 경우 로컬 인덱스 변수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="95580-263">이 시나리오에서 인덱스 변수를 사용하는 것은 `NavLink` 구성 요소뿐 아니라 [자식 콘텐츠](xref:blazor/components/index#child-content)에서 루프 변수를 사용하는 **모든** 자식 구성 요소의 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="95580-264">또는 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>을 활용하여 `foreach` 루프를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="95580-265">ASP.NET Core 엔드포인트 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="95580-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="95580-266">‘이 섹션은 Blazor Server 앱에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="95580-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="95580-267">Blazor Server는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="95580-268">`Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="95580-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="95580-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="95580-270">일반적인 구성은 Blazor Server 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="95580-271">규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 앱의 `Pages` 폴더에서 `_Host.cshtml`입니다.</span><span class="sxs-lookup"><span data-stu-id="95580-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="95580-272">호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="95580-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="95580-273">일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="95580-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="95580-274">이렇게 하면 앱이 Blazor Server 앱의 구성 요소 라우팅을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95580-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="95580-275">루트가 아닌 URL 서버 호스팅을 위한 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 구성에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95580-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
