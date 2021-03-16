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
# <a name="aspnet-core-blazor-project-structure"></a>ASP.NET Core Blazor 프로젝트 구조

이 문서에서는 Blazor 프레임워크의 프로젝트 템플릿 중 하나에서 생성된 Blazor 앱을 구성하는 파일과 폴더를 설명합니다. 도구를 사용하여 Blazor 프로젝트 템플릿에서 Blazor 앱을 만드는 방법에 대한 자세한 내용은 <xref:blazor/tooling>을 참조하세요. Blazor의 호스팅 모델인 Blazor WebAssembly 및 Blazor Server에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

## Blazor WebAssembly

Blazor WebAssembly 프로젝트 템플릿: `blazorwasm`

Blazor WebAssembly 템플릿은 Blazor WebAssembly 앱의 초기 파일 및 디렉터리 구조를 만듭니다. 이 앱은 정적 자산에서 데이터를 로드하는 `FetchData` 구성 요소의 데모 코드, `weather.json`, `Counter` 구성 요소와의 사용자 상호 작용으로 채워집니다.

* `Pages` 폴더: Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지(`.razor`)가 포함되어 있습니다. 각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다. 이 템플릿은 다음 구성 요소를 포함합니다.
  * `Counter` 구성 요소(`Counter.razor`): 카운터 페이지를 구현합니다.
  * `FetchData` 구성 요소(`FetchData.razor`): 데이터 가져오기 페이지를 구현합니다.
  * `Index` 구성 요소(`Index.razor`): 홈페이지를 구현합니다.
  
* `Properties/launchSettings.json`: [개발 환경 구성](xref:fundamentals/environments#development-and-launchsettingsjson)을 보관합니다.

::: moniker range=">= aspnetcore-5.0"

* `Shared` 폴더: 다음 공유 구성 요소와 스타일시트가 포함되어 있습니다.
  * `MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.
  * `MainLayout.razor.css`: 앱의 기본 레이아웃용 스타일 시트입니다.
  * `NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.
  * `NavMenu.razor.css`: 앱의 탐색 메뉴용 스타일 시트입니다.
  * `SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 폴더: 다음 공유 구성 요소를 포함합니다.
  * `MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.
  * `NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.
  * `SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: [구성 설정](xref:blazor/fundamentals/configuration)의 `appsettings.json` 및 환경 앱 설정 파일을 포함하여 앱의 공용 정적 자산이 포함된 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다. `index.html` 웹 페이지는 HTML 페이지로 구현된 앱의 루트 페이지입니다.
  * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
  * 이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다. 구성 요소는 `app`의 `id`를 사용하는 `div` DOM 요소(`<div id="app">Loading...</div>`)의 위치에서 렌더링됩니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: [구성 설정](xref:blazor/fundamentals/configuration)의 `appsettings.json` 및 환경 앱 설정 파일을 포함하여 앱의 공용 정적 자산이 포함된 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다. `index.html` 웹 페이지는 HTML 페이지로 구현된 앱의 루트 페이지입니다.
  * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
  * 이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다. 구성 요소는 `app` DOM 요소(`<app>Loading...</app>`)의 위치에서 렌더링됩니다.

::: moniker-end

> [!NOTE]
> `wwwroot/index.html` 파일에 추가된 JavaScript(JS) 파일은 닫는 `</body>` 태그 앞에 와야 합니다. 일부 시나리오에서는 사용자 지정 JS 코드가 JS 파일에서 로드되는 순서가 중요합니다. 예를 들어 interop 메서드가 있는 JS 파일은 Blazor 프레임워크 JS 파일 전에 포함되어야 합니다.

* `_Imports.razor`: 네임스페이스의 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소(`.razor`)에 포함할 일반적인 Razor 지시문을 포함합니다.

* `App.razor`: <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다. <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: WebAssembly 호스트를 설정하는 앱의 진입점입니다.
  
  * `App` 구성 요소는 앱의 루트 구성 요소입니다. `App` 구성 요소는 `app`의 `id`를 사용하여 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("#app")`)에 대한 `div` DOM 요소(`wwwroot/index.html`의 `<div id="app">Loading...</div>`)로 지정됩니다.
  * [서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: WebAssembly 호스트를 설정하는 앱의 진입점입니다.

  * `App` 구성 요소는 앱의 루트 구성 요소입니다. `App` 구성 요소는 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("app")`)에 대한 `app` DOM 요소(`wwwroot/index.html`의 `<app>Loading...</app>`)로 지정됩니다.
  * [서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

## Blazor Server

Blazor Server 프로젝트 템플릿: `blazorserver`

Blazor Server 템플릿은 Blazor Server 앱의 초기 파일 및 디렉터리 구조를 만듭니다. 이 앱은 등록된 서비스에서 데이터를 로드하는 `FetchData` 구성 요소의 데모 코드, `WeatherForecastService`, `Counter` 구성 요소와의 사용자 상호 작용으로 채워집니다.

* `Data` 폴더: 앱의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공하는 `WeatherForecastService` 구현과 `WeatherForecast` 클래스가 포함되어 있습니다.

* `Pages` 폴더: Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지(`.razor`)와 Blazor Server 앱의 루트 Razor 페이지를 포함합니다. 각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다. 이 템플릿은 다음을 포함합니다.
  * `_Host.cshtml`: Razor 페이지로 구현된 앱의 루트 페이지:
    * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
    * 이 호스트 페이지는 루트 `App` 구성 요소(`App.razor`)가 렌더링되는 위치를 지정합니다.
  * `Counter` 구성 요소(`Counter.razor`): 카운터 페이지를 구현합니다.
  * `Error` 구성 요소(`Error.razor`): 앱에서 처리되지 않은 예외가 발생할 때 렌더링됩니다.
  * `FetchData` 구성 요소(`FetchData.razor`): 데이터 가져오기 페이지를 구현합니다.
  * `Index` 구성 요소(`Index.razor`): 홈페이지를 구현합니다.

> [!NOTE]
> `Pages/_Host.cshtml` 파일에 추가된 JavaScript(JS) 파일은 닫는 `</body>` 태그 앞에 와야 합니다. 일부 시나리오에서는 사용자 지정 JS 코드가 JS 파일에서 로드되는 순서가 중요합니다. 예를 들어 interop 메서드가 있는 JS 파일은 Blazor 프레임워크 JS 파일 전에 포함되어야 합니다.

* `Properties/launchSettings.json`: [개발 환경 구성](xref:fundamentals/environments#development-and-launchsettingsjson)을 보관합니다.

::: moniker range=">= aspnetcore-5.0"

* `Shared` 폴더: 다음 공유 구성 요소와 스타일시트가 포함되어 있습니다.
  * `MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.
  * `MainLayout.razor.css`: 앱의 기본 레이아웃용 스타일 시트입니다.
  * `NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.
  * `NavMenu.razor.css`: 앱의 탐색 메뉴용 스타일 시트입니다.
  * `SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 폴더: 다음 공유 구성 요소를 포함합니다.
  * `MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.
  * `NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-and-navmenu-components)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.
  * `SurveyPrompt` 구성 요소(`SurveyPrompt.razor`): Blazor 설문 조사 구성 요소입니다.
  
::: moniker-end

* `wwwroot`: 앱의 퍼블릭 정적 자산을 포함하는 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.

* `_Imports.razor`: 네임스페이스의 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소(`.razor`)에 포함할 일반적인 Razor 지시문을 포함합니다.

* `App.razor`: <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다. <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.

* `appsettings.json` 및 환경 앱 설정 파일: 앱의 [구성 설정](xref:blazor/fundamentals/configuration)을 제공합니다.

* `Program.cs`: ASP.NET Core [호스트](xref:fundamentals/host/generic-host)를 설정하는 앱의 진입점입니다.

* `Startup.cs`: 앱의 시작 논리를 포함합니다. `Startup` 클래스는 다음 두 가지 메서드를 정의합니다.

  * `ConfigureServices`: 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성합니다. <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>를 호출하여 서비스가 추가되고, 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 `WeatherForecastService`가 추가됩니다.
  * `Configure`: 앱의 요청 처리 파이프라인을 구성합니다.
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>는 브라우저와의 실시간 연결을 위해 엔드포인트를 설정하도록 호출됩니다. 연결은 애플리케이션에 앱에 실시간 웹 기능을 추가하기 위한 프레임워크인 [SignalR](xref:signalr/introduction)를 사용하여 만들어집니다.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)는 앱의 루트 페이지(`Pages/_Host.cshtml`)를 설정하고 탐색을 사용하도록 설정하기 위해 호출됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/tooling>
* <xref:blazor/hosting-models>
