---
title: ASP.NET Core Blazor WebAssembly의 어셈블리 지연 로드
author: guardrex
description: ASP.NET Core Blazor WebAssembly 앱에서 어셈블리를 지연 로드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0fb744b4e9d44e6b8136123fddfb75ace8901d52
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819931"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="bde6a-103">ASP.NET Core Blazor WebAssembly의 어셈블리 지연 로드</span><span class="sxs-lookup"><span data-stu-id="bde6a-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="bde6a-104">[Safia Abdalla](https://safia.rocks) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bde6a-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bde6a-105">일부 애플리케이션 어셈블리의 로드를 연기(지연 로드라고 함)하여 Blazor WebAssembly 앱 시작 성능을 향상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="bde6a-106">예를 들어 단일 구성 요소를 렌더링하는 데만 사용되는 어셈블리는 사용자가 해당 구성 요소로 이동하는 경우에만 로드되도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="bde6a-107">로드 후 어셈블리는 클라이언트 쪽에서 캐시되며 이후의 모든 탐색에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="bde6a-108">Blazor의 지연 로드 기능을 사용하면 사용자가 특정 경로로 이동할 때 런타임에 어셈블리를 로드하는 지연 로드로 앱 어셈블리를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="bde6a-109">이 기능은 프로젝트 파일 변경과 애플리케이션의 라우터 변경으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="bde6a-110">어셈블리가 Blazor Server 앱에서 클라이언트로 다운로드되지 않기 때문에 어셈블리 지연 로드는 Blazor Server 앱에 유용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="bde6a-111">프로젝트 파일</span><span class="sxs-lookup"><span data-stu-id="bde6a-111">Project file</span></span>

<span data-ttu-id="bde6a-112">`BlazorWebAssemblyLazyLoad` 항목을 사용하여 앱의 프로젝트 파일(`.csproj`)에서 어셈블리를 지연 로드로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="bde6a-113">`.dll` 확장명 없이 어셈블리 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="bde6a-114">Blazor 프레임워크는 이 항목 그룹이 지정한 어셈블리가 앱 시작 시 로드되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="bde6a-115">다음 예제에서는 큰 사용자 지정 어셈블리(`GrantImaharaRobotControls.dll`)를 지연 로드로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="bde6a-116">지연 로드로 표시된 어셈블리에 종속성이 있는 경우 프로젝트 파일에서 종속성도 지연 로드로 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="bde6a-117">앱이 사용하는 어셈블리만 지연 로드될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="bde6a-118">링커는 게시된 출력에서 사용되지 않는 어셈블리를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="bde6a-119">`Router` 구성 요소</span><span class="sxs-lookup"><span data-stu-id="bde6a-119">`Router` component</span></span>

<span data-ttu-id="bde6a-120">Blazor의 `Router` 구성 요소는 Blazor가 라우팅 가능한 구성 요소를 검색할 어셈블리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="bde6a-121">`Router` 구성 요소는 사용자가 탐색하는 경로에 대한 구성 요소 렌더링도 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="bde6a-122">`Router` 구성 요소는 지연 로드와 함께 사용할 수 있는 `OnNavigateAsync` 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="bde6a-123">앱의 `Router` 구성 요소(`App.razor`)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="bde6a-124">`OnNavigateAsync` 콜백을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="bde6a-125">사용자가 다음을 수행하면 `OnNavigateAsync` 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="bde6a-126">브라우저에서 직접 경로로 이동하여 처음으로 경로를 방문합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="bde6a-127">링크 또는 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 호출을 사용하여 새 경로로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="bde6a-128">지연 로드된 어셈블리에 라우팅 가능한 구성 요소가 포함된 경우 [목록](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>>(예: `lazyLoadedAssemblies`)을 구성 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="bde6a-129">어셈블리에 라우팅 가능한 구성 요소가 포함된 경우 어셈블리는 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 컬렉션으로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="bde6a-130">프레임워크는 어셈블리에서 경로를 검색하고 새 경로를 찾으면 경로 컬렉션을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="bde6a-131">`OnNavigateAsync` 콜백이 처리되지 않은 예외를 throw하는 경우 [Blazor 오류 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="bde6a-132">`OnNavigateAsync`의 어셈블리 로드 논리</span><span class="sxs-lookup"><span data-stu-id="bde6a-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="bde6a-133">`OnNavigateAsync`에는 대상 경로(`Path`) 및 취소 토큰(`CancellationToken`)을 포함하여 현재 비동기 탐색 이벤트에 대한 정보를 제공하는 `NavigationContext` 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="bde6a-134">`Path` 속성은 `/robot`과 같이 애플리케이션의 기본 경로를 기준으로 하는 사용자의 대상 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="bde6a-135">`CancellationToken`을 사용하여 비동기 작업의 취소를 관찰할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="bde6a-136">`OnNavigateAsync`는 사용자가 다른 페이지로 이동하면 현재 실행 중인 탐색 작업을 자동으로 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="bde6a-137">`OnNavigateAsync` 내에서 로드할 어셈블리를 결정하는 논리를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="bde6a-138">표시되는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-138">Options include:</span></span>

* <span data-ttu-id="bde6a-139">`OnNavigateAsync` 메서드 내의 조건부 검사입니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="bde6a-140">구성 요소에 삽입되거나 [`@code`](xref:mvc/views/razor#code) 블록 내에서 구현되는 경로를 어셈블리 이름에 매핑하는 조회 테이블입니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="bde6a-141">`LazyAssemblyLoader`는 어셈블리를 로드하기 위한 프레임워크 제공 싱글톤 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="bde6a-142">`Router` 구성 요소에 `LazyAssemblyLoader`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="bde6a-143">`LazyAssemblyLoader`는 다음과 같은 `LoadAssembliesAsync` 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="bde6a-144">JS interop을 사용하여 네트워크 호출을 통해 어셈블리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="bde6a-145">브라우저의 WebAssembly에서 실행되는 런타임에 어셈블리를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="bde6a-146">프레임워크의 지연 로드 구현은 서버에서의 미리 렌더링을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="bde6a-147">미리 렌더링 중에는 지연 로드로 표시된 어셈블리를 포함한 모든 어셈블리가 로드된다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="bde6a-148">`<Navigating>` 콘텐츠와의 사용자 상호 작용</span><span class="sxs-lookup"><span data-stu-id="bde6a-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="bde6a-149">몇 초 정도 걸릴 수 있는 어셈블리 로드 동안 `Router` 구성 요소는 페이지 전환 중임을 사용자에게 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="bde6a-150"><xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 네임스페이스에 대한 [`@using`](xref:mvc/views/razor#using) 지시문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="bde6a-151">페이지 전환 이벤트 중 표시할 태그를 사용하여 구성 요소에 `<Navigating>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="bde6a-152">`OnNavigateAsync`에서의 취소 처리</span><span class="sxs-lookup"><span data-stu-id="bde6a-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="bde6a-153">`OnNavigateAsync` 콜백에 전달되는 `NavigationContext` 개체에는 새 탐색 이벤트가 발생할 때 설정되는 `CancellationToken`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="bde6a-154">오래된 탐색에서 `OnNavigateAsync` 콜백을 계속 실행하지 않도록 이 취소 토큰이 설정된 경우 `OnNavigateAsync` 콜백이 throw해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="bde6a-155">사용자가 경로 A로 이동한 다음 경로 B로 바로 이동하는 경우 앱은 경로 A에 대해 `OnNavigateAsync` 콜백을 계속 실행해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="bde6a-156">`NavigationContext`의 취소 토큰이 취소되는 경우 throw하지 않으면 이전 탐색에서 구성 요소를 렌더링하는 것과 같은 의도하지 않은 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="bde6a-157">전체 예제</span><span class="sxs-lookup"><span data-stu-id="bde6a-157">Complete example</span></span>

<span data-ttu-id="bde6a-158">다음 전체 `Router` 구성 요소는 사용자가 `/robot`으로 이동할 때 `GrantImaharaRobotControls.dll` 어셈블리가 로드되는 것을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="bde6a-159">페이지 전환 중에는 스타일이 지정된 메시지가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="bde6a-160">문제 해결</span><span class="sxs-lookup"><span data-stu-id="bde6a-160">Troubleshoot</span></span>

* <span data-ttu-id="bde6a-161">이전 탐색의 구성 요소가 렌더링되는 것처럼 예기치 않은 렌더링이 발생하는 경우, 취소 토큰이 설정되면 코드에서 throw하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="bde6a-162">애플리케이션 시작 시 어셈블리가 여전히 로드되는 경우 프로젝트 파일에서 해당 어셈블리가 지연 로드로 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bde6a-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bde6a-163">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="bde6a-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>