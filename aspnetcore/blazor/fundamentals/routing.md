---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법과 NavLink 구성 요소를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
ms.openlocfilehash: 3bfd623a206f260d24e2c9009acdb3b205b7ab2d
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855406"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="f36a7-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="f36a7-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="f36a7-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="f36a7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f36a7-105">요청을 라우팅하는 방법과 Blazor 앱에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="f36a7-106">ASP.NET Core 엔드포인트 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="f36a7-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="f36a7-107">Blazor Server는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="f36a7-108">`Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="f36a7-109">가장 일반적인 구성은 Blazor Server 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="f36a7-110">규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 `_Host.cshtml`입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="f36a7-111">호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="f36a7-112">일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="f36a7-113">이렇게 하면 앱이 Blazor Server 앱을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-113">This allows the app to use other controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="f36a7-114">루트가 아닌 URL 서버 호스팅을 위한 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 구성에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="f36a7-115">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="f36a7-115">Route templates</span></span>

<span data-ttu-id="f36a7-116"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 지정된 경로를 사용하여 각 구성 요소로 라우팅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="f36a7-117"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 `App.razor` 파일에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="f36a7-118">`@page` 지시문을 포함하는 `.razor` 파일을 컴파일하면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="f36a7-119">앱이 부팅되면 `AppAssembly`로 지정된 어셈블리를 검사하여 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 있는 모든 구성 요소에 대한 정보를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-119">When the app boots, the assembly specified as the `AppAssembly` is scanned to gather information about all of the components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="f36a7-120">런타임에 <xref:Microsoft.AspNetCore.Components.RouteView> 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-120">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="f36a7-121">원하는 매개 변수와 함께 <xref:Microsoft.AspNetCore.Components.Routing.Router>에서 <xref:Microsoft.AspNetCore.Components.RouteData>를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-121">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="f36a7-122">지정된 매개 변수를 사용하여 지정된 구성 요소를 해당 레이아웃(또는 선택적 기본 레이아웃)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-122">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="f36a7-123">필요한 경우, 레이아웃을 지정하지 않는 구성 요소에 사용할 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 매개 변수를 레이아웃 클래스로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-123">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="f36a7-124">기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-124">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="f36a7-125">`MainLayout.razor`는 템플릿 프로젝트의 `Shared` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-125">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="f36a7-126">레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-126">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="f36a7-127">한 구성 요소에 여러 개의 경로 템플릿을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-127">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="f36a7-128">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-128">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="f36a7-129">URL을 올바르게 확인하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 `<base>` 태그가 있어야 하고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다(`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="f36a7-129">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="f36a7-130">자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-130">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="f36a7-131">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="f36a7-131">Provide custom content when content isn't found</span></span>

<span data-ttu-id="f36a7-132"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-132">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="f36a7-133">`App.razor` 파일에서 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소의 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 템플릿 매개 변수에 사용자 지정 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-133">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="f36a7-134">`<NotFound>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-134">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="f36a7-135"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-135">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="f36a7-136">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="f36a7-136">Route to components from multiple assemblies</span></span>

<span data-ttu-id="f36a7-137"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대해 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-137">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="f36a7-138">지정한 어셈블리는 `AppAssembly`에서 지정한 어셈블리에 추가로 고려됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-138">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="f36a7-139">다음 예제에서 `Component1`은 참조된 클래스 라이브러리에서 정의된 라우팅 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-139">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="f36a7-140">다음 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-140">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="f36a7-141">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="f36a7-141">Route parameters</span></span>

<span data-ttu-id="f36a7-142">라우터는 경로 매개 변수를 사용하여 해당 구성 요소 매개 변수를 동일한 이름(대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-142">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f36a7-143">선택적 매개 변수가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-143">Optional parameters are supported.</span></span> <span data-ttu-id="f36a7-144">다음 예제에서 `text` 선택적 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-144">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="f36a7-145">세그먼트가 없으면 `Text` 값이 `fantastic`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-145">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="f36a7-146">선택적 매개 변수는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-146">Optional parameters aren't supported.</span></span> <span data-ttu-id="f36a7-147">위의 예제에서는 두 개의 `@page` 지시문이 적용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-147">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="f36a7-148">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-148">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f36a7-149">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용하고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-149">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="f36a7-150">다른 선택적 매개 변수 값을 사용하여 동일한 구성 요소에 앱 탐색을 허용하려면 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 대신 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set)에 대해 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="f36a7-151">앞의 예제를 기반으로 하여, `/RouteParameter`에서 `/RouteParameter/awesome`으로, 또는 `/RouteParameter/awesome`에서 `/RouteParameter`로 이동할 수 있어야 할 때 `OnParametersSet`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/awesome` or from `/RouteParameter/awesome` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="f36a7-152">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="f36a7-152">Route constraints</span></span>

<span data-ttu-id="f36a7-153">경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="f36a7-154">다음 예제에서 `Users` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-154">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="f36a7-155">요청 URL에 `Id` 경로 세그먼트가 있는 경우</span><span class="sxs-lookup"><span data-stu-id="f36a7-155">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="f36a7-156">`Id` 세그먼트가 정수(`int`)인 경우</span><span class="sxs-lookup"><span data-stu-id="f36a7-156">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="f36a7-157">다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="f36a7-158">고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-158">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="f36a7-159">제약 조건</span><span class="sxs-lookup"><span data-stu-id="f36a7-159">Constraint</span></span> | <span data-ttu-id="f36a7-160">예제</span><span class="sxs-lookup"><span data-stu-id="f36a7-160">Example</span></span>           | <span data-ttu-id="f36a7-161">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="f36a7-161">Example Matches</span></span>                                                                  | <span data-ttu-id="f36a7-162">고정</span><span class="sxs-lookup"><span data-stu-id="f36a7-162">Invariant</span></span><br><span data-ttu-id="f36a7-163">culture</span><span class="sxs-lookup"><span data-stu-id="f36a7-163">culture</span></span><br><span data-ttu-id="f36a7-164">일치</span><span class="sxs-lookup"><span data-stu-id="f36a7-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="f36a7-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="f36a7-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="f36a7-166">아니요</span><span class="sxs-lookup"><span data-stu-id="f36a7-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="f36a7-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="f36a7-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="f36a7-168">예</span><span class="sxs-lookup"><span data-stu-id="f36a7-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="f36a7-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="f36a7-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="f36a7-170">예</span><span class="sxs-lookup"><span data-stu-id="f36a7-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="f36a7-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f36a7-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f36a7-172">예</span><span class="sxs-lookup"><span data-stu-id="f36a7-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="f36a7-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f36a7-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f36a7-174">예</span><span class="sxs-lookup"><span data-stu-id="f36a7-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="f36a7-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="f36a7-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="f36a7-176">아니요</span><span class="sxs-lookup"><span data-stu-id="f36a7-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="f36a7-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f36a7-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f36a7-178">예</span><span class="sxs-lookup"><span data-stu-id="f36a7-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="f36a7-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f36a7-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f36a7-180">예</span><span class="sxs-lookup"><span data-stu-id="f36a7-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="f36a7-181">CLR 형식(예: `int` 또는 <xref:System.DateTime>)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="f36a7-182">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-182">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="f36a7-183">점이 포함된 URL을 사용한 라우팅</span><span class="sxs-lookup"><span data-stu-id="f36a7-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="f36a7-184">호스트된 Blazor WebAssembly 및 Blazor Server 앱의 경우 서버 쪽 기본 경로 템플릿에서 요청 URL의 마지막 세그먼트에 파일을 요청하는 점(`.`)이 포함되어 있다고 가정합니다(예: `https://localhost.com:5001/example/some.thing`).</span><span class="sxs-lookup"><span data-stu-id="f36a7-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="f36a7-185">추가 구성이 없는 앱은 ‘404 - 찾을 수 없음’ 응답을 반환하여 구성 요소로 라우팅되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-185">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="f36a7-186">점이 포함된 하나 이상의 매개 변수가 있는 경로를 사용하려면 앱에서 사용자 지정 템플릿을 사용하여 경로를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-186">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="f36a7-187">URL의 마지막 세그먼트에서 경로 매개 변수를 받을 수 있는 다음 `Example` 구성 요소를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-187">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="f36a7-188">호스트된 Blazor WebAssembly 솔루션의 *Server* 앱이 `param` 매개 변수의 점으로 요청을 라우팅하도록 허용하려면 `Startup.Configure`(`Startup.cs`)에서 선택적 매개 변수를 사용하여 대체 파일 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-188">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="f36a7-189">`param` 매개 변수에서 점을 사용하여 요청을 라우팅하도록 Blazor Server 앱을 구성하려면 `Startup.Configure`(`Startup.cs`)에서 선택적 매개 변수를 사용하여 대체 페이지 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-189">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="f36a7-190">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-190">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="f36a7-191">모든 경로 매개 변수 catch</span><span class="sxs-lookup"><span data-stu-id="f36a7-191">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f36a7-192">‘이 섹션은 .NET 5 RC1(릴리스 후보 1) 이상의 ASP.NET Core에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="f36a7-192">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="f36a7-193">여러 폴더 경계에서 경로를 캡처하는 catch-all 경로 매개 변수는 구성 요소에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-193">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="f36a7-194">catch-all 경로 매개 변수는 다음을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-194">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="f36a7-195">이름이 경로 세그먼트 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-195">Named to match the route segment name.</span></span> <span data-ttu-id="f36a7-196">이름 지정에서 대소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-196">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="f36a7-197">`string` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-197">A `string` type.</span></span> <span data-ttu-id="f36a7-198">프레임워크가 자동 캐스팅을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-198">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="f36a7-199">URL의 끝부분에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-199">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="f36a7-200">`/page/{*pageRoute}`의 경로 템플릿이 있는 URL `/page/this/is/a/test`의 경우 `PageRoute`의 값이 `this/is/a/test`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-200">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="f36a7-201">캡처된 경로의 슬래시 및 세그먼트가 디코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-201">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="f36a7-202">`/page/{*pageRoute}`의 경로 템플릿의 경우 `/page/this/is/a%2Ftest%2A` URL은 `this/is/a/test*`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-202">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f36a7-203">catch-all 경로 매개 변수는 ASP.NET Core 5.0 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-203">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="f36a7-204">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="f36a7-204">NavLink component</span></span>

<span data-ttu-id="f36a7-205">탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-205">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="f36a7-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-206">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="f36a7-207">`active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-207">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="f36a7-208">현재 경로가 `href`와 일치하는 경우, 필요에 따라 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>에 CSS 클래스 이름을 할당하여 렌더링된 링크에 사용자 지정 CSS 클래스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-208">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="f36a7-209">다음 `NavMenu` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하는 방법을 보여 주는 [`Bootstrap`](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-209">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="f36a7-210">`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-210">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="f36a7-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: 현재 URL 전체와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="f36a7-212"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(기본값): 현재 URL의 접두사와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-212"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="f36a7-213">위의 예제에서 홈 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-213">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="f36a7-214">두 번째 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>는 사용자가 `MyComponent` 접두사를 포함하는 URL(예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)을 방문할 때 `active` 클래스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-214">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="f36a7-215">추가 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-215">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="f36a7-216">다음 예제에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `target` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-216">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="f36a7-217">다음 HTML 태그가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-217">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="f36a7-218">Blazor가 자식 콘텐츠를 렌더링하는 방식 때문에 `for` 루프 내에서 `NavLink` 구성 요소를 렌더링하려면 `NavLink`(자식) 구성 요소 콘텐츠에서 증분 루프 변수를 사용할 경우 로컬 인덱스 변수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-218">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="f36a7-219">이 시나리오에서 인덱스 변수를 사용하는 것은 `NavLink` 구성 요소뿐 아니라 [자식 콘텐츠](xref:blazor/components/index#child-content)에서 루프 변수를 사용하는 **모든** 자식 구성 요소의 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-219">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="f36a7-220">또는 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>을 활용하여 `foreach` 루프를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-220">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="f36a7-221">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="f36a7-221">URI and navigation state helpers</span></span>

<span data-ttu-id="f36a7-222"><xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 C# 코드로 URI 및 탐색 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-222">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="f36a7-223"><xref:Microsoft.AspNetCore.Components.NavigationManager>는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-223"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="f36a7-224">멤버</span><span class="sxs-lookup"><span data-stu-id="f36a7-224">Member</span></span> | <span data-ttu-id="f36a7-225">설명</span><span class="sxs-lookup"><span data-stu-id="f36a7-225">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="f36a7-226">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-226">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="f36a7-227">절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-227">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="f36a7-228">일반적으로 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>는 `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-228">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="f36a7-229">지정한 URI로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-229">Navigates to the specified URI.</span></span> <span data-ttu-id="f36a7-230">`forceLoad`가 `true`인 경우</span><span class="sxs-lookup"><span data-stu-id="f36a7-230">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="f36a7-231">클라이언트 쪽 라우팅이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-231">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="f36a7-232">클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-232">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="f36a7-233">탐색 위치가 변경된 경우에 발생하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-233">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="f36a7-234">상대 URI를 절대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-234">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="f36a7-235">기본 URI(예: 이전에 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-235">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="f36a7-236">단추를 선택하면 다음 구성 요소가 앱의 `Counter` 구성 요소로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-236">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="f36a7-237">다음 구성 요소는 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>을 구독하여 위치 변경 이벤트를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-237">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f36a7-238">`HandleLocationChanged` 메서드는 프레임워크에서 `Dispose`를 호출할 때 언후크됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-238">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="f36a7-239">메서드를 언후크하면 구성 요소의 가비지 수집이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-239">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="f36a7-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>는 이벤트에 대해 다음과 같은 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="f36a7-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: 새 위치의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="f36a7-242"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: `true`인 경우 Blazor는 브라우저에서 탐색을 가로챘습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-242"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="f36a7-243">`false`인 경우 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>에서 탐색이 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-243">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="f36a7-244">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f36a7-244">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="f36a7-245">쿼리 문자열 및 구문 분석 매개 변수</span><span class="sxs-lookup"><span data-stu-id="f36a7-245">Query string and parse parameters</span></span>

<span data-ttu-id="f36a7-246">요청의 쿼리 문자열은 <xref:Microsoft.AspNetCore.Components.NavigationManager>의 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> 속성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-246">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="f36a7-247">쿼리 문자열의 매개 변수를 구문 분석하려면</span><span class="sxs-lookup"><span data-stu-id="f36a7-247">To parse a query string's parameters:</span></span>

* <span data-ttu-id="f36a7-248">[Microsoft.AspNetCore.Hosting.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-248">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="f36a7-249"><xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>을 사용하여 쿼리 문자열을 구문 분석한 후 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-249">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="f36a7-250">앞의 예에서 자리 표시자 `{KEY}`는 쿼리 문자열 매개 변수 키입니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-250">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="f36a7-251">예를 들어 URL 키-값 쌍 `?ship=Tardis`는 `ship` 키를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f36a7-251">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
