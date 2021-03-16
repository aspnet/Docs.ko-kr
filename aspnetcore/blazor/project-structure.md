---
title: ASP.NET Core Blazor 프로젝트 구조
author: guardrex
description: ASP.NET Core Blazor 앱 프로젝트 구조를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: fe42c2d43b79ea959bb0ba8e5b96e6c865b2a416
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394878"
---
# <a name="aspnet-core-blazor-project-structure"></a><span data-ttu-id="92b81-103">ASP.NET Core Blazor 프로젝트 구조</span><span class="sxs-lookup"><span data-stu-id="92b81-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="92b81-104">이 문서에서는 Blazor 프레임워크의 프로젝트 템플릿 중 하나에서 생성된 Blazor 앱을 구성하는 파일과 폴더를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-104">This article describes the files and folders that make up a Blazor app generated from one of the Blazor framework's project templates.</span></span> <span data-ttu-id="92b81-105">도구를 사용하여 Blazor 프로젝트 템플릿에서 Blazor 앱을 만드는 방법에 대한 자세한 내용은 <xref:blazor/tooling>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="92b81-105">For information on how to use tooling to create a Blazor app from a Blazor project template, see <xref:blazor/tooling>.</span></span> <span data-ttu-id="92b81-106">Blazor의 호스팅 모델인 Blazor WebAssembly 및 Blazor Server에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="92b81-106">For information on Blazor's hosting models, Blazor WebAssembly and Blazor Server, see <xref:blazor/hosting-models>.</span></span>

## Blazor WebAssembly

<span data-ttu-id="92b81-107">Blazor WebAssembly 프로젝트 템플릿: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="92b81-107">Blazor WebAssembly project template: `blazorwasm`</span></span>

<span data-ttu-id="92b81-108">Blazor WebAssembly 템플릿은 Blazor WebAssembly 앱의 초기 파일 및 디렉터리 구조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-108">The Blazor WebAssembly template creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="92b81-109">이 앱은 정적 자산에서 데이터를 로드하는 `FetchData` 구성 요소의 데모 코드, `weather.json`, `Counter` 구성 요소와의 사용자 상호 작용으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-109">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="92b81-110">`Pages` 폴더: Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지(`.razor`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-110">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="92b81-111">각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-111">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="92b81-112">이 템플릿은 다음 구성 요소를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-112">The template includes the following components:</span></span>
  * <span data-ttu-id="92b81-113">`Counter` 구성 요소(`Counter.razor`): 카운터 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-113">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="92b81-114">`FetchData` 구성 요소(`FetchData.razor`): 데이터 가져오기 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-114">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="92b81-115">`Index` 구성 요소(`Index.razor`): 홈페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-115">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="92b81-116">`Properties/launchSettings.json`: [개발 환경 구성](xref:fundamentals/environments#development-and-launchsettingsjson)을 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-116">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="92b81-117">`Shared` 폴더: 다음 공유 구성 요소와 스타일시트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-117">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="92b81-118">`MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-118">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="92b81-119">`MainLayout.razor.css`: 앱의 기본 레이아웃용 스타일 시트입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-119">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="92b81-120">`NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-120">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="92b81-121">다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-121">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="92b81-122"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-122">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="92b81-123">`NavMenu.razor.css`: 앱의 탐색 메뉴용 스타일 시트입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-123">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="92b81-124">`SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-124">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="92b81-125">`Shared` 폴더: 다음 공유 구성 요소를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-125">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="92b81-126">`MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-126">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="92b81-127">`NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-127">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="92b81-128">다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-128">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="92b81-129"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-129">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="92b81-130">`SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-130">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="92b81-131">`wwwroot`: [구성 설정](xref:blazor/fundamentals/configuration)의 `appsettings.json` 및 환경 앱 설정 파일을 포함하여 앱의 공용 정적 자산이 포함된 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-131">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="92b81-132">`index.html` 웹 페이지는 HTML 페이지로 구현된 앱의 루트 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-132">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="92b81-133">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="92b81-134">이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="92b81-135">구성 요소는 `app`의 `id`를 사용하는 `div` DOM 요소(`<div id="app">Loading...</div>`)의 위치에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-135">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="92b81-136">`wwwroot`: [구성 설정](xref:blazor/fundamentals/configuration)의 `appsettings.json` 및 환경 앱 설정 파일을 포함하여 앱의 공용 정적 자산이 포함된 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-136">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="92b81-137">`index.html` 웹 페이지는 HTML 페이지로 구현된 앱의 루트 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-137">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="92b81-138">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-138">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="92b81-139">이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-139">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="92b81-140">구성 요소는 `app` DOM 요소(`<app>Loading...</app>`)의 위치에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-140">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="92b81-141">`wwwroot/index.html` 파일에 추가된 JavaScript(JS) 파일은 닫는 `</body>` 태그 앞에 와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-141">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="92b81-142">일부 시나리오에서는 사용자 지정 JS 코드가 JS 파일에서 로드되는 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-142">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="92b81-143">예를 들어 interop 메서드가 있는 JS 파일은 Blazor 프레임워크 JS 파일 전에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-143">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="92b81-144">`_Imports.razor`: 네임스페이스의 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소(`.razor`)에 포함할 일반적인 Razor 지시문을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-144">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="92b81-145">`App.razor`: <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-145">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="92b81-146"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-146">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="92b81-147">`Program.cs`: WebAssembly 호스트를 설정하는 앱의 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-147">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="92b81-148">`App` 구성 요소는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-148">The `App` component is the root component of the app.</span></span> <span data-ttu-id="92b81-149">`App` 구성 요소는 `app`의 `id`를 사용하여 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("#app")`)에 대한 `div` DOM 요소(`wwwroot/index.html`의 `<div id="app">Loading...</div>`)로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-149">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="92b81-150">[서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="92b81-150">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="92b81-151">`Program.cs`: WebAssembly 호스트를 설정하는 앱의 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-151">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="92b81-152">`App` 구성 요소는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-152">The `App` component is the root component of the app.</span></span> <span data-ttu-id="92b81-153">`App` 구성 요소는 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("app")`)에 대한 `app` DOM 요소(`wwwroot/index.html`의 `<app>Loading...</app>`)로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-153">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="92b81-154">[서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="92b81-154">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="92b81-155">Blazor Server 프로젝트 템플릿: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="92b81-155">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="92b81-156">Blazor Server 템플릿은 Blazor Server 앱의 초기 파일 및 디렉터리 구조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-156">The Blazor Server template creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="92b81-157">이 앱은 등록된 서비스에서 데이터를 로드하는 `FetchData` 구성 요소의 데모 코드, `WeatherForecastService`, `Counter` 구성 요소와의 사용자 상호 작용으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-157">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="92b81-158">`Data` 폴더: 앱의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공하는 `WeatherForecastService` 구현과 `WeatherForecast` 클래스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-158">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="92b81-159">`Pages` 폴더: Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지(`.razor`)와 Blazor Server 앱의 루트 Razor 페이지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-159">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="92b81-160">각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-160">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="92b81-161">이 템플릿은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-161">The template includes the following:</span></span>
  * <span data-ttu-id="92b81-162">`_Host.cshtml`: Razor 페이지로 구현된 앱의 루트 페이지:</span><span class="sxs-lookup"><span data-stu-id="92b81-162">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="92b81-163">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-163">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="92b81-164">이 호스트 페이지는 루트 `App` 구성 요소(`App.razor`)가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-164">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="92b81-165">`Counter` 구성 요소(`Counter.razor`): 카운터 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-165">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="92b81-166">`Error` 구성 요소(`Error.razor`): 앱에서 처리되지 않은 예외가 발생할 때 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-166">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="92b81-167">`FetchData` 구성 요소(`FetchData.razor`): 데이터 가져오기 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-167">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="92b81-168">`Index` 구성 요소(`Index.razor`): 홈페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-168">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="92b81-169">`Pages/_Host.cshtml` 파일에 추가된 JavaScript(JS) 파일은 닫는 `</body>` 태그 앞에 와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-169">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="92b81-170">일부 시나리오에서는 사용자 지정 JS 코드가 JS 파일에서 로드되는 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-170">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="92b81-171">예를 들어 interop 메서드가 있는 JS 파일은 Blazor 프레임워크 JS 파일 전에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-171">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="92b81-172">`Properties/launchSettings.json`: [개발 환경 구성](xref:fundamentals/environments#development-and-launchsettingsjson)을 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-172">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="92b81-173">`Shared` 폴더: 다음 공유 구성 요소와 스타일시트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-173">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="92b81-174">`MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-174">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="92b81-175">`MainLayout.razor.css`: 앱의 기본 레이아웃용 스타일 시트입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-175">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="92b81-176">`NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-176">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="92b81-177">다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-177">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="92b81-178"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-178">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="92b81-179">`NavMenu.razor.css`: 앱의 탐색 메뉴용 스타일 시트입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-179">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="92b81-180">`SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-180">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="92b81-181">`Shared` 폴더: 다음 공유 구성 요소를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-181">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="92b81-182">`MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-182">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="92b81-183">`NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-183">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="92b81-184">다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-184">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="92b81-185"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-185">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="92b81-186">`SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-186">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="92b81-187">`wwwroot`: 앱의 퍼블릭 정적 자산을 포함하는 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-187">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="92b81-188">`_Imports.razor`: 네임스페이스의 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소(`.razor`)에 포함할 일반적인 Razor 지시문을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-188">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="92b81-189">`App.razor`: <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-189">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="92b81-190"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-190">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="92b81-191">`appsettings.json` 및 환경 앱 설정 파일: 앱의 [구성 설정](xref:blazor/fundamentals/configuration)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-191">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="92b81-192">`Program.cs`: ASP.NET Core [호스트](xref:fundamentals/host/generic-host)를 설정하는 앱의 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-192">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="92b81-193">`Startup.cs`: 앱의 시작 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-193">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="92b81-194">`Startup` 클래스는 다음 두 가지 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-194">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="92b81-195">`ConfigureServices`: 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-195">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="92b81-196"><xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>를 호출하여 서비스가 추가되고, 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 `WeatherForecastService`가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-196">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="92b81-197">`Configure`: 앱의 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-197">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="92b81-198"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>는 브라우저와의 실시간 연결을 위해 엔드포인트를 설정하도록 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-198"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="92b81-199">연결은 애플리케이션에 앱에 실시간 웹 기능을 추가하기 위한 프레임워크인 [SignalR](xref:signalr/introduction)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-199">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="92b81-200">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)는 앱의 루트 페이지(`Pages/_Host.cshtml`)를 설정하고 탐색을 사용하도록 설정하기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="92b81-200">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92b81-201">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="92b81-201">Additional resources</span></span>

* <xref:blazor/tooling>
* <xref:blazor/hosting-models>
