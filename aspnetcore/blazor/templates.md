---
title: ASP.NET Core Blazor 템플릿
author: guardrex
description: ASP.NET Core Blazor 앱 템플릿 및 Blazor 프로젝트 구조에 대해 알아봅니다.
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703724"
---
# <a name="aspnet-core-no-locblazor-templates"></a>ASP.NET Core Blazor 템플릿

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

Blazor 프레임워크는 각 Blazor 호스팅 모델용 앱을 개발하기 위한 템플릿을 제공합니다.

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Blazor의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

템플릿 옵션은 `--help` 옵션을 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 명령에 전달하여 사용할 수 있습니다.

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Blazor 프로젝트 구조

다음 파일 및 폴더는 Blazor 프로젝트 템플릿에서 생성된 Blazor 앱을 구성합니다.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: 다음을 설정하는 앱의 진입점입니다.

  * ASP.NET Core [호스트](xref:fundamentals/host/generic-host)(Blazor Server)
  * WebAssembly 호스트(Blazor WebAssembly): 이 파일의 코드는 Blazor WebAssembly 템플릿(`blazorwasm`)에서 만든 앱에 고유합니다.
    * `App` 구성 요소는 앱의 루트 구성 요소입니다. `App` 구성 요소는 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("#app")`)에 대한 `app` DOM 요소(`wwwroot/index.html`의 `<div id="app">Loading...</div>`)로 지정됩니다.
    * [서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: 다음을 설정하는 앱의 진입점입니다.

  * ASP.NET Core [호스트](xref:fundamentals/host/generic-host)(Blazor Server)
  * WebAssembly 호스트(Blazor WebAssembly): 이 파일의 코드는 Blazor WebAssembly 템플릿(`blazorwasm`)에서 만든 앱에 고유합니다.
    * `App` 구성 요소는 앱의 루트 구성 요소입니다. `App` 구성 요소는 루트 구성 요소 컬렉션(`builder.RootComponents.Add<App>("app")`)에 대한 `app` DOM 요소(`wwwroot/index.html`의 `<app>Loading...</app>`)로 지정됩니다.
    * [서비스](xref:blazor/fundamentals/dependency-injection)가 추가되고 구성됩니다(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

* `Startup.cs`(Blazor Server): 앱의 시작 논리를 포함합니다. `Startup` 클래스는 다음 두 가지 메서드를 정의합니다.

  * `ConfigureServices`: 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성합니다. Blazor Server 앱에서 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>를 호출하여 서비스가 추가되고 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 `WeatherForecastService`가 추가됩니다.
  * `Configure`: 앱의 요청 처리 파이프라인을 구성합니다.
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>는 브라우저와의 실시간 연결을 위해 엔드포인트를 설정하도록 호출됩니다. 연결은 애플리케이션에 앱에 실시간 웹 기능을 추가하기 위한 프레임워크인 [SignalR](xref:signalr/introduction)를 사용하여 만들어집니다.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)는 앱의 루트 페이지(`Pages/_Host.cshtml`)를 설정하고 탐색을 사용하도록 설정하기 위해 호출됩니다.

* `wwwroot/index.html`(Blazor WebAssembly): HTML 페이지로 구현된 앱의 루트 페이지입니다.
  * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
  * 이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다. 구성 요소는 `app` DOM 요소(`<app>...</app>`)의 위치에서 렌더링됩니다.
  * `_framework/blazor.webassembly.js` JavaScript 파일이 로드되며, 다음을 수행합니다.
    * .NET 런타임, 앱 및 앱의 종속성을 다운로드합니다.
    * 런타임을 초기화하여 앱을 실행합니다.

* `App.razor`: <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다. <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.

* `Pages` 폴더: Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지(`.razor`)와 Blazor Server 앱의 루트 Razor 페이지를 포함합니다. 각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다. 이 템플릿은 다음을 포함합니다.
  * `_Host.cshtml`(Blazor Server): Razor 페이지로 구현된 앱의 루트 페이지:
    * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
    * 브라우저와 서버 간에 실시간 SignalR 연결을 설정하는 `_framework/blazor.server.js` JavaScript 파일이 로드됩니다.
    * 이 호스트 페이지는 루트 `App` 구성 요소(`App.razor`)가 렌더링되는 위치를 지정합니다.
  * `Counter` 구성 요소(`Pages/Counter.razor`): 카운터 페이지를 구현합니다.
  * `Error` 구성 요소(`Error.razor`, Blazor Server 앱에만 해당): 앱에서 처리되지 않은 예외가 발생할 때 렌더링됩니다.
  * `FetchData` 구성 요소(`Pages/FetchData.razor`): 데이터 가져오기 페이지를 구현합니다.
  * `Index` 구성 요소(`Pages/Index.razor`): 홈페이지를 구현합니다.
  
* `Properties/launchSettings.json`: [개발 환경 구성](xref:fundamentals/environments#development-and-launchsettingsjson)을 보관합니다.

::: moniker range=">= aspnetcore-5.0"

* `Shared` 폴더: 앱에서 사용되는 다른 UI 구성 요소(`.razor`)를 포함합니다.
  * `MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.
  * `MainLayout.razor.css`: 앱의 기본 레이아웃용 스타일 시트입니다.
  * `NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.
  * `NavMenu.razor.css`: 앱의 탐색 메뉴용 스타일 시트입니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` 폴더: 앱에서 사용되는 다른 UI 구성 요소(`.razor`)를 포함합니다.
  * `MainLayout` 구성 요소(`MainLayout.razor`): 앱의 [레이아웃 구성 요소](xref:blazor/layouts)입니다.
  * `NavMenu` 구성 요소(`NavMenu.razor`): 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소의 탐색 링크를 렌더링하는 [`NavLink` 구성 요소](xref:blazor/fundamentals/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.
  
::: moniker-end

* `_Imports.razor`: 네임스페이스의 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소(`.razor`)에 포함할 일반적인 Razor 지시문을 포함합니다.

* `Data` 폴더(Blazor Server): 앱의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공하는 `WeatherForecast` 클래스 및 `WeatherForecastService` 구현이 포함되어 있습니다.

* `wwwroot`: 앱의 퍼블릭 정적 자산을 포함하는 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.

* `appsettings.json`: 앱의 [구성 설정](xref:blazor/fundamentals/configuration)을 보관합니다. Blazor WebAssembly 앱에서 앱 설정 파일은 `wwwroot` 폴더에 있습니다. Blazor Server 앱에서 앱 설정 파일은 프로젝트 루트에 있습니다.
