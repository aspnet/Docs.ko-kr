---
title: ASP.NET Core Razor 구성 요소 미리 렌더링 및 통합
author: guardrex
description: 서버에서의 Razor 구성 요소 미리 렌더링을 비롯하여 Blazor 앱의 Razor 구성 요소 통합 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431692"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="12bc7-103">ASP.NET Core Razor 구성 요소 미리 렌더링 및 통합</span><span class="sxs-lookup"><span data-stu-id="12bc7-103">Prerender and integrate ASP.NET Core Razor components</span></span>

<span data-ttu-id="12bc7-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="12bc7-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12bc7-105">Razor 구성 요소는 Razor Pages 및 호스트된 Blazor WebAssembly 솔루션의 MVC 앱에 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-105">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="12bc7-106">페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="12bc7-107">Configuration</span><span class="sxs-lookup"><span data-stu-id="12bc7-107">Configuration</span></span>

<span data-ttu-id="12bc7-108">Blazor WebAssembly 앱의 미리 렌더링을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="12bc7-108">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="12bc7-109">ASP.NET Core 앱에서 Blazor WebAssembly 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="12bc7-110">독립 실행형 Blazor WebAssembly 앱을 ASP.NET Core 솔루션에 추가하거나 Blazor Hosted 프로젝트 템플릿에서 만든 호스트된 Blazor WebAssembly 앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="12bc7-111">Blazor WebAssembly 클라이언트 프로젝트에서 기본 정적 `wwwroot/index.html` 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-111">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="12bc7-112">클라이언트 프로젝트의 `Program.Main`에서 다음 줄을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="12bc7-113">서버 프로젝트에 `Pages/_Host.cshtml` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="12bc7-114">명령 셸에서 `dotnet new blazorserver -o BlazorServer` 명령을 사용하여 Blazor Server 템플릿에서 만든 앱에서 `_Host.cshtml` 파일을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-114">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="12bc7-115">`Pages/_Host.cshtml` 파일을 호스트된 Blazor WebAssembly 솔루션의 서버 앱에 배치한 후 파일을 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="12bc7-116">네임스페이스를 서버 앱의 `Pages` 폴더(예: `@namespace BlazorHosted.Server.Pages`)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="12bc7-117">클라이언트 프로젝트의 [`@using`](xref:mvc/views/razor#using) 지시문을 설정합니다(예: `@using BlazorHosted.Client`).</span><span class="sxs-lookup"><span data-stu-id="12bc7-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="12bc7-118">WebAssembly의 스타일시트를 가리키도록 스타일시트 링크를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="12bc7-119">다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-119">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="12bc7-120">루트 `App` 구성 요소를 미리 렌더링하도록 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)의 `render-mode`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="12bc7-121">클라이언트 쪽 Blazor WebAssembly 스크립트를 사용하도록 Blazor 스크립트 원본을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-121">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="12bc7-122">서버 프로젝트의 `Startup.Configure`(`Startup.cs`)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="12bc7-123">개발 환경의 앱 작성기에서 `UseDeveloperExceptionPage`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="12bc7-124">앱 작성기에서 `UseBlazorFrameworkFiles`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-124">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="12bc7-125">`index.html` 페이지(`endpoints.MapFallbackToFile("index.html");`)에서 `_Host.cshtml` 페이지로 대체를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="12bc7-126">구성 요소 태그 도우미를 사용하여 페이지 또는 보기에서 구성 요소를 렌더링</span><span class="sxs-lookup"><span data-stu-id="12bc7-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="12bc7-127">구성 요소 태그 도우미는 페이지 또는 보기에서 Blazor WebAssembly 앱의 구성 요소를 렌더링하기 위한 두 가지 렌더링 모드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-127">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="12bc7-128">`WebAssembly`: 브라우저에 로드될 때 대화형 구성 요소를 포함하는 데 사용할 Blazor WebAssembly 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-128">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="12bc7-129">구성 요소는 미리 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-129">The component isn't prerendered.</span></span> <span data-ttu-id="12bc7-130">이 옵션을 사용하면 다양한 Blazor WebAssembly 구성 요소를 서로 다른 페이지에서 쉽게 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-130">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="12bc7-131">`WebAssemblyPrerendered`: 구성 요소를 정적 HTML로 미리 렌더링하고 나중에 브라우저에서 로드될 때 구성 요소를 대화형으로 만드는 데 사용할 수 있도록 Blazor WebAssembly 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="12bc7-132">다음 Razor Pages 예제에서는 `Counter` 구성 요소가 페이지에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-132">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="12bc7-133">구성 요소를 대화형으로 만들기 위해 Blazor WebAssembly 스크립트가 페이지의 [렌더링 섹션](xref:mvc/views/layout#sections)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-133">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="12bc7-134">구성 요소 태그 도우미에 `Counter` 구성 요소의 전체 네임스페이스를 사용하지 않으려면(`{APP ASSEMBLY}.Pages.Counter`) 클라이언트 앱의 `Pages` 네임스페이스에 대한 [`@using`](xref:mvc/views/razor#using) 지시문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="12bc7-135">다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-135">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="12bc7-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="12bc7-137">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="12bc7-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="12bc7-138">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="12bc7-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="12bc7-139">매개 변수 전달과 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 구성을 포함하여 구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="12bc7-140">앞의 예제에서는 서버 앱의 레이아웃(`_Layout.cshtml`)에 닫는 `</body>` 태그 안에 있는 스크립트에 대한 [렌더링 섹션](xref:mvc/views/layout#sections)(<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)이 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="12bc7-141">`_Layout.cshtml` 파일은 Razor Pages 앱의 `Pages/Shared` 폴더 또는 MVC 앱의 `Views/Shared` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="12bc7-142">앱이 Blazor WebAssembly 앱의 스타일로 구성 요소의 스타일을 지정해야 하는 경우 `_Layout.cshtml` 파일에 앱의 스타일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-142">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="12bc7-143">다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-143">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="12bc7-144">CSS 선택기를 사용하여 페이지 또는 보기에서 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="12bc7-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="12bc7-145">`Program.Main`(`Program.cs`)의 *Client* 프로젝트에 루트 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="12bc7-146">다음 예제에서 `Counter` 구성 요소는 `my-counter`와 일치하는 `id`가 있는 요소를 선택하는 CSS 선택기를 사용하여 루트 구성 요소로 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="12bc7-147">다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-147">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="12bc7-148">다음 Razor Pages 예제에서는 `Counter` 구성 요소가 페이지에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-148">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="12bc7-149">구성 요소를 대화형으로 만들기 위해 Blazor WebAssembly 스크립트가 페이지의 [렌더링 섹션](xref:mvc/views/layout#sections)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-149">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="12bc7-150">앞의 예제에서는 서버 앱의 레이아웃(`_Layout.cshtml`)에 닫는 `</body>` 태그 안에 있는 스크립트에 대한 [렌더링 섹션](xref:mvc/views/layout#sections)(<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)이 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="12bc7-151">`_Layout.cshtml` 파일은 Razor Pages 앱의 `Pages/Shared` 폴더 또는 MVC 앱의 `Views/Shared` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="12bc7-152">앱이 Blazor WebAssembly 앱의 스타일로 구성 요소의 스타일을 지정해야 하는 경우 `_Layout.cshtml` 파일에 앱의 스타일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-152">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="12bc7-153">다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-153">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="12bc7-154">호스트된 Blazor WebAssembly 솔루션에서 Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 기능은 .NET 5 이상의 ASP.NET Core에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-154">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="12bc7-155">이 문서의 .NET 5 이상 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="12bc7-156">Blazor Server 앱에서 Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-156">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="12bc7-157">페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="12bc7-158">[앱을 구성](#configuration)한 후 앱 요구 사항에 따라 다음 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="12bc7-159">라우팅 가능한 구성 요소: 사용자 요청에서 직접 라우팅할 수 있는 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="12bc7-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="12bc7-160">방문자가 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 구성 요소에 대한 HTTP 요청을 브라우저에서 만들 수 있는 경우 이 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="12bc7-161">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="12bc7-161">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="12bc7-162">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="12bc7-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="12bc7-163">[페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view): 사용자 요청에서 직접 라우팅할 수 없는 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="12bc7-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="12bc7-164">[구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 앱이 구성 요소를 기존 페이지와 뷰에 포함하는 경우 이 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="12bc7-165">Configuration</span><span class="sxs-lookup"><span data-stu-id="12bc7-165">Configuration</span></span>

<span data-ttu-id="12bc7-166">기존 Razor Pages 또는 MVC 앱은 페이지와 뷰에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-166">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="12bc7-167">앱의 레이아웃 파일(`_Layout.cshtml`)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="12bc7-168">`<head>` 요소에 다음 `<base>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="12bc7-169">위의 예제에서 `href` 값(‘앱 기본 경로’)은 앱이 루트 URL 경로(`/`)에 있는 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-169">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="12bc7-170">앱이 하위 애플리케이션인 경우, <xref:blazor/host-and-deploy/index#app-base-path> 문서의 ‘앱 기본 경로’ 섹션에 설명된 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="12bc7-171">`_Layout.cshtml` 파일은 Razor Pages 앱의 `Pages/Shared` 폴더 또는 MVC 앱의 `Views/Shared` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="12bc7-172">`Scripts` 렌더링 섹션 바로 앞에 `blazor.server.js` 스크립트의 `<script>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="12bc7-173">프레임워크가 `blazor.server.js` 스크립트를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="12bc7-174">앱에 스크립트를 수동으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="12bc7-175">다음 콘텐츠를 사용하여 프로젝트의 루트 폴더에 `_Imports.razor` 파일을 추가합니다(마지막 네임스페이스인 `MyAppNamespace`를 앱의 네임스페이스로 변경).</span><span class="sxs-lookup"><span data-stu-id="12bc7-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="12bc7-176">`Startup.ConfigureServices`에서 Blazor Server 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-176">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="12bc7-177">`Startup.Configure`에서 `app.UseEndpoints`에 Blazor 허브 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-177">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="12bc7-178">페이지 또는 뷰에 구성 요소를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-178">Integrate components into any page or view.</span></span> <span data-ttu-id="12bc7-179">자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="12bc7-180">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="12bc7-180">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="12bc7-181">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="12bc7-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="12bc7-182">Razor Pages 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-182">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="12bc7-183">[구성](#configuration) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="12bc7-184">다음 콘텐츠를 사용하여 프로젝트 루트에 `App.razor` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-184">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="12bc7-185">다음 콘텐츠를 사용하여 `Pages` 폴더에 `_Host.cshtml` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="12bc7-186">구성 요소는 해당 레이아웃에 공유 `_Layout.cshtml` 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="12bc7-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="12bc7-188">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="12bc7-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="12bc7-189">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="12bc7-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="12bc7-190">매개 변수 전달과 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 구성을 포함하여 구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="12bc7-191">`Startup.Configure`의 엔드포인트 구성에 `_Host.cshtml` 페이지의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="12bc7-192">라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-192">Add routable components to the app.</span></span> <span data-ttu-id="12bc7-193">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="12bc7-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="12bc7-194">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="12bc7-195">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="12bc7-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="12bc7-196">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="12bc7-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="12bc7-197">MVC 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-197">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="12bc7-198">[구성](#configuration) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="12bc7-199">다음 콘텐츠를 사용하여 프로젝트 루트에 `App.razor` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="12bc7-200">다음 콘텐츠를 사용하여 `Views/Home` 폴더에 `_Host.cshtml` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="12bc7-201">구성 요소는 해당 레이아웃에 공유 `_Layout.cshtml` 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="12bc7-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="12bc7-203">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="12bc7-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="12bc7-204">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="12bc7-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="12bc7-205">매개 변수 전달과 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 구성을 포함하여 구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="12bc7-206">홈 컨트롤러에 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="12bc7-207">`Startup.Configure`의 엔드포인트 구성에 `_Host.cshtml` 뷰를 반환하는 컨트롤러 작업의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="12bc7-208">`Pages` 폴더를 만들고 라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="12bc7-209">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="12bc7-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="12bc7-210">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="12bc7-211">페이지 또는 뷰에서 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="12bc7-211">Render components from a page or view</span></span>

<span data-ttu-id="12bc7-212">‘이 섹션에서는 사용자 요청에서 직접 구성 요소를 라우팅할 수 없는 페이지 또는 뷰에 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="12bc7-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="12bc7-213">페이지 또는 뷰에서 구성 요소를 렌더링하려면 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="12bc7-214">상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="12bc7-214">Render stateful interactive components</span></span>

<span data-ttu-id="12bc7-215">Razor 페이지 또는 뷰에 상태 저장 대화형 구성 요소를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-215">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="12bc7-216">페이지 또는 뷰를 렌더링하는 경우와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-216">When the page or view renders:</span></span>

* <span data-ttu-id="12bc7-217">구성 요소가 페이지 또는 뷰와 함께 미리 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="12bc7-218">미리 렌더링하는 데 사용된 초기 구성 요소 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="12bc7-219">SignalR 연결이 완료되면 새 구성 요소 상태가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-219">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="12bc7-220">다음 Razor 페이지는 `Counter` 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-220">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="12bc7-221">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="12bc7-222">비대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="12bc7-222">Render noninteractive components</span></span>

<span data-ttu-id="12bc7-223">다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 통해 지정된 초기 값을 사용하여 정적으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-223">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="12bc7-224">구성 요소가 정적으로 렌더링되므로 구성 요소는 대화형이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="12bc7-225">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="12bc7-226">구성 요소 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="12bc7-226">Component namespaces</span></span>

<span data-ttu-id="12bc7-227">사용자 지정 폴더를 사용하여 앱의 구성 요소를 저장하는 경우, 폴더를 나타내는 네임스페이스를 페이지/뷰 또는 `_ViewImports.cshtml` 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="12bc7-228">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="12bc7-228">In the following example:</span></span>

* <span data-ttu-id="12bc7-229">`MyAppNamespace`를 앱의 네임스페이스로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="12bc7-230">구성 요소를 저장하는 데 `Components` 폴더를 사용하지 않은 경우, `Components`를 구성 요소가 있는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="12bc7-231">`_ViewImports.cshtml` 파일은 Razor Pages 앱의 `Pages` 폴더 또는 MVC 앱의 `Views` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12bc7-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="12bc7-232">자세한 내용은 <xref:blazor/components/index#namespaces>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="12bc7-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
