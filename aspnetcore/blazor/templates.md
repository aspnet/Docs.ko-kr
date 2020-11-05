---
title: 'ASP.NET Core :::no-loc(Blazor)::: 템플릿'
author: guardrex
description: 'ASP.NET Core :::no-loc(Blazor)::: 앱 템플릿 및 :::no-loc(Blazor)::: 프로젝트 구조에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
uid: blazor/templates
ms.openlocfilehash: eef381367d7aa59dcc430c529746088d4488e700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054933"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="70003-103">ASP.NET Core :::no-loc(Blazor)::: 템플릿</span><span class="sxs-lookup"><span data-stu-id="70003-103">ASP.NET Core :::no-loc(Blazor)::: templates</span></span>

<span data-ttu-id="70003-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70003-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="70003-105">:::no-loc(Blazor)::: 프레임워크는 각 :::no-loc(Blazor)::: 호스팅 모델용 앱을 개발하기 위한 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-105">The :::no-loc(Blazor)::: framework provides templates to develop apps for each of the :::no-loc(Blazor)::: hosting models:</span></span>

* <span data-ttu-id="70003-106">:::no-loc(Blazor WebAssembly)::: (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="70003-106">:::no-loc(Blazor WebAssembly)::: (`blazorwasm`)</span></span>
* <span data-ttu-id="70003-107">:::no-loc(Blazor Server)::: (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="70003-107">:::no-loc(Blazor Server)::: (`blazorserver`)</span></span>

<span data-ttu-id="70003-108">:::no-loc(Blazor):::의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70003-108">For more information on :::no-loc(Blazor):::'s hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="70003-109">템플릿 옵션은 `--help` 옵션을 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 명령에 전달하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70003-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="70003-110">:::no-loc(Blazor)::: 프로젝트 구조</span><span class="sxs-lookup"><span data-stu-id="70003-110">:::no-loc(Blazor)::: project structure</span></span>

<span data-ttu-id="70003-111">다음 파일 및 폴더는 :::no-loc(Blazor)::: 프로젝트 템플릿에서 생성된 :::no-loc(Blazor)::: 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-111">The following files and folders make up a :::no-loc(Blazor)::: app generated from a :::no-loc(Blazor)::: project template:</span></span>

* <span data-ttu-id="70003-112">`Program.cs`: 다음을 설정하는 앱의 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="70003-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="70003-113">ASP.NET Core [호스트](xref:fundamentals/host/generic-host)(:::no-loc(Blazor Server):::)</span><span class="sxs-lookup"><span data-stu-id="70003-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (:::no-loc(Blazor Server):::)</span></span>
  * <span data-ttu-id="70003-114">WebAssembly 호스트(:::no-loc(Blazor WebAssembly):::): 이 파일의 코드는 :::no-loc(Blazor WebAssembly)::: 템플릿(`blazorwasm`)에서 만든 앱에 고유합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-114">WebAssembly host (:::no-loc(Blazor WebAssembly):::): The code in this file is unique to apps created from the :::no-loc(Blazor WebAssembly)::: template (`blazorwasm`).</span></span>
    * <span data-ttu-id="70003-115">`App` 구성 요소는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="70003-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="70003-116">`App` 구성 요소는 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("app")`)에 대한 `app` DOM 요소(`<app>...</app>`)로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-116">The `App` component is specified as the `app` DOM element (`<app>...</app>`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="70003-117">[서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="70003-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

* <span data-ttu-id="70003-118">`Startup.cs`(:::no-loc(Blazor Server):::): 앱의 시작 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-118">`Startup.cs` (:::no-loc(Blazor Server):::): Contains the app's startup logic.</span></span> <span data-ttu-id="70003-119">`Startup` 클래스는 다음 두 가지 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="70003-120">`ConfigureServices`: 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="70003-121">:::no-loc(Blazor Server)::: 앱에서 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSide:::no-loc(Blazor):::%2A>를 호출하여 서비스가 추가되고 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 `WeatherForecastService`가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-121">In :::no-loc(Blazor Server)::: apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSide:::no-loc(Blazor):::%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="70003-122">`Configure`: 앱의 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="70003-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A>는 브라우저와의 실시간 연결을 위해 엔드포인트를 설정하도록 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="70003-124">연결은 애플리케이션에 앱에 실시간 웹 기능을 추가하기 위한 프레임워크인 [:::no-loc(SignalR):::](xref:signalr/introduction)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="70003-124">The connection is created with [:::no-loc(SignalR):::](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="70003-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage*)는 앱의 루트 페이지(`Pages/_Host.cshtml`)를 설정하고 탐색을 사용하도록 설정하기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="70003-126">`wwwroot/index.html`(:::no-loc(Blazor WebAssembly):::): HTML 페이지로 구현된 앱의 루트 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="70003-126">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="70003-127">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="70003-128">이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="70003-129">구성 요소는 `app` DOM 요소(`<app>...</app>`)의 위치에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-129">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="70003-130">`_framework/blazor.webassembly.js` JavaScript 파일이 로드되며, 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="70003-131">.NET 런타임, 앱 및 앱의 종속성을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="70003-132">런타임을 초기화하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="70003-133">`App.razor`: <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="70003-133">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="70003-134"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="70003-135">`Pages` 폴더: :::no-loc(Blazor)::: 앱을 구성하는 라우팅 가능한 구성 요소/페이지(`.razor`)와 :::no-loc(Blazor Server)::: 앱의 루트 :::no-loc(Razor)::: 페이지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-135">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the :::no-loc(Blazor)::: app and the root :::no-loc(Razor)::: page of a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="70003-136">각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-136">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="70003-137">이 템플릿은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-137">The template includes the following:</span></span>
  * <span data-ttu-id="70003-138">`_Host.cshtml`(:::no-loc(Blazor Server):::): :::no-loc(Razor)::: 페이지로 구현된 앱의 루트 페이지:</span><span class="sxs-lookup"><span data-stu-id="70003-138">`_Host.cshtml` (:::no-loc(Blazor Server):::): The root page of the app implemented as a :::no-loc(Razor)::: Page:</span></span>
    * <span data-ttu-id="70003-139">앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-139">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="70003-140">브라우저와 서버 간에 실시간 :::no-loc(SignalR)::: 연결을 설정하는 `_framework/blazor.server.js` JavaScript 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-140">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time :::no-loc(SignalR)::: connection between the browser and the server.</span></span>
    * <span data-ttu-id="70003-141">이 호스트 페이지는 루트 `App` 구성 요소(`App.razor`)가 렌더링되는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-141">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="70003-142">`Counter`(`Pages/Counter.razor`): 카운터 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-142">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="70003-143">`Error`(`Error.razor`, :::no-loc(Blazor Server)::: 앱에만 해당): 앱에서 처리되지 않은 예외가 발생할 때 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-143">`Error` (`Error.razor`, :::no-loc(Blazor Server)::: app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="70003-144">`FetchData`(`Pages/FetchData.razor`): 데이터 가져오기 페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-144">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="70003-145">`Index`(`Pages/Index.razor`): 홈페이지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-145">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="70003-146">`Properties/launchSettings.json`: [개발 환경 구성](xref:fundamentals/environments#development-and-launchsettingsjson)을 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-146">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="70003-147">`Shared` 폴더: 앱에서 사용되는 다른 UI 구성 요소(`.razor`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="70003-148">`MainLayout`(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.</span><span class="sxs-lookup"><span data-stu-id="70003-148">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="70003-149">`NavMenu`(`NavMenu.razor`): 사이드바 탐색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="70003-150">다른 :::no-loc(Razor)::: 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other :::no-loc(Razor)::: components.</span></span> <span data-ttu-id="70003-151"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="70003-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="70003-152">`_Imports.razor`: 네임스페이스의 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소(`.razor`)에 포함할 일반적인 :::no-loc(Razor)::: 지시문을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-152">`_Imports.razor`: Includes common :::no-loc(Razor)::: directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="70003-153">`Data` 폴더(:::no-loc(Blazor Server):::): 앱의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공하는 `WeatherForecast` 클래스 및 `WeatherForecastService` 구현이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70003-153">`Data` folder (:::no-loc(Blazor Server):::): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="70003-154">`wwwroot`: 앱의 퍼블릭 정적 자산을 포함하는 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="70003-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="70003-155">`:::no-loc(appsettings.json):::`: 앱의 [구성 설정](xref:blazor/fundamentals/configuration)을 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="70003-155">`:::no-loc(appsettings.json):::`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="70003-156">:::no-loc(Blazor WebAssembly)::: 앱에서 앱 설정 파일은 `wwwroot` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70003-156">In a :::no-loc(Blazor WebAssembly)::: app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="70003-157">:::no-loc(Blazor Server)::: 앱에서 앱 설정 파일은 프로젝트 루트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70003-157">In a :::no-loc(Blazor Server)::: app, the app settings file is located at the project root.</span></span>
