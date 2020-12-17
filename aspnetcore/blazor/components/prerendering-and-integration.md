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
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506983"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>ASP.NET Core Razor 구성 요소 미리 렌더링 및 통합

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor 구성 요소는 Razor Pages 및 호스트된 Blazor WebAssembly 솔루션의 MVC 앱에 통합할 수 있습니다. 페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.

## <a name="configuration"></a>Configuration

Blazor WebAssembly 앱의 미리 렌더링을 설정하려면:

1. ASP.NET Core 앱에서 Blazor WebAssembly 앱을 호스트합니다. 독립 실행형 Blazor WebAssembly 앱을 ASP.NET Core 솔루션에 추가하거나 Blazor Hosted 프로젝트 템플릿에서 만든 호스트된 Blazor WebAssembly 앱을 사용할 수 있습니다.

1. Blazor WebAssembly 클라이언트 프로젝트에서 기본 정적 `wwwroot/index.html` 파일을 제거합니다.

1. 클라이언트 프로젝트의 `Program.Main`에서 다음 줄을 삭제합니다.

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. 서버 프로젝트에 `Pages/_Host.cshtml` 파일을 추가합니다. 명령 셸에서 `dotnet new blazorserver -o BlazorServer` 명령을 사용하여 Blazor Server 템플릿에서 만든 앱에서 `_Host.cshtml` 파일을 가져올 수 있습니다. `Pages/_Host.cshtml` 파일을 호스트된 Blazor WebAssembly 솔루션의 서버 앱에 배치한 후 파일을 다음과 같이 변경합니다.

   * 네임스페이스를 서버 앱의 `Pages` 폴더(예: `@namespace BlazorHosted.Server.Pages`)로 설정합니다.
   * 클라이언트 프로젝트의 [`@using`](xref:mvc/views/razor#using) 지시문을 설정합니다(예: `@using BlazorHosted.Client`).
   * WebAssembly의 스타일시트를 가리키도록 스타일시트 링크를 업데이트합니다. 다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * 루트 `App` 구성 요소를 미리 렌더링하도록 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)의 `render-mode`를 업데이트합니다.

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * 클라이언트 쪽 Blazor WebAssembly 스크립트를 사용하도록 Blazor 스크립트 원본을 업데이트합니다.

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. 서버 프로젝트의 `Startup.Configure`(`Startup.cs`)에서 다음을 수행합니다.

   * 개발 환경의 앱 작성기에서 `UseDeveloperExceptionPage`를 호출합니다.
   * 앱 작성기에서 `UseBlazorFrameworkFiles`를 호출합니다.
   * `index.html` 페이지(`endpoints.MapFallbackToFile("index.html");`)에서 `_Host.cshtml` 페이지로 대체를 변경합니다.

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

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>구성 요소 태그 도우미를 사용하여 페이지 또는 보기에서 구성 요소를 렌더링

구성 요소 태그 도우미는 페이지 또는 보기에서 Blazor WebAssembly 앱의 구성 요소를 렌더링하기 위한 두 가지 렌더링 모드를 지원합니다.

* `WebAssembly`: 브라우저에 로드될 때 대화형 구성 요소를 포함하는 데 사용할 Blazor WebAssembly 앱의 마커를 렌더링합니다. 구성 요소는 미리 렌더링되지 않습니다. 이 옵션을 사용하면 다양한 Blazor WebAssembly 구성 요소를 서로 다른 페이지에서 쉽게 렌더링할 수 있습니다.
* `WebAssemblyPrerendered`: 구성 요소를 정적 HTML로 미리 렌더링하고 나중에 브라우저에서 로드될 때 구성 요소를 대화형으로 만드는 데 사용할 수 있도록 Blazor WebAssembly 앱의 마커를 포함합니다.

다음 Razor Pages 예제에서는 `Counter` 구성 요소가 페이지에서 렌더링됩니다. 구성 요소를 대화형으로 만들기 위해 Blazor WebAssembly 스크립트가 페이지의 [렌더링 섹션](xref:mvc/views/layout#sections)에 포함됩니다. 구성 요소 태그 도우미에 `Counter` 구성 요소의 전체 네임스페이스를 사용하지 않으려면(`{APP ASSEMBLY}.Pages.Counter`) 클라이언트 앱의 `Pages` 네임스페이스에 대한 [`@using`](xref:mvc/views/razor#using) 지시문을 추가합니다. 다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.

* 페이지에 미리 렌더링할지 여부
* 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

매개 변수 전달과 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 구성을 포함하여 구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

앞의 예제에서는 서버 앱의 레이아웃(`_Layout.cshtml`)에 닫는 `</body>` 태그 안에 있는 스크립트에 대한 [렌더링 섹션](xref:mvc/views/layout#sections)(<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)이 포함되어야 합니다.

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

`_Layout.cshtml` 파일은 Razor Pages 앱의 `Pages/Shared` 폴더 또는 MVC 앱의 `Views/Shared` 폴더에 있습니다.

앱이 Blazor WebAssembly 앱의 스타일로 구성 요소의 스타일을 지정해야 하는 경우 `_Layout.cshtml` 파일에 앱의 스타일을 포함합니다. 다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>CSS 선택기를 사용하여 페이지 또는 보기에서 구성 요소 렌더링

`Program.Main`(`Program.cs`)의 *Client* 프로젝트에 루트 구성 요소를 추가합니다. 다음 예제에서 `Counter` 구성 요소는 `my-counter`와 일치하는 `id`가 있는 요소를 선택하는 CSS 선택기를 사용하여 루트 구성 요소로 선언됩니다. 다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

다음 Razor Pages 예제에서는 `Counter` 구성 요소가 페이지에서 렌더링됩니다. 구성 요소를 대화형으로 만들기 위해 Blazor WebAssembly 스크립트가 페이지의 [렌더링 섹션](xref:mvc/views/layout#sections)에 포함됩니다.

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

앞의 예제에서는 서버 앱의 레이아웃(`_Layout.cshtml`)에 닫는 `</body>` 태그 안에 있는 스크립트에 대한 [렌더링 섹션](xref:mvc/views/layout#sections)(<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)이 포함되어야 합니다.

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

`_Layout.cshtml` 파일은 Razor Pages 앱의 `Pages/Shared` 폴더 또는 MVC 앱의 `Views/Shared` 폴더에 있습니다.

앱이 Blazor WebAssembly 앱의 스타일로 구성 요소의 스타일을 지정해야 하는 경우 `_Layout.cshtml` 파일에 앱의 스타일을 포함합니다. 다음 예제에서 클라이언트 앱의 네임스페이스는 `BlazorHosted.Client`입니다.

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

호스트된 Blazor WebAssembly 솔루션에서 Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 기능은 .NET 5 이상의 ASP.NET Core에서 지원됩니다. 이 문서의 .NET 5 이상 버전을 선택하세요.

::: moniker-end

::: zone-end

::: zone pivot="server"

Blazor Server 앱에서 Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합할 수 있습니다. 페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.

[앱을 구성](#configuration)한 후 앱 요구 사항에 따라 다음 섹션의 지침을 따릅니다.

* 라우팅 가능한 구성 요소: 사용자 요청에서 직접 라우팅할 수 있는 구성 요소. 방문자가 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 구성 요소에 대한 HTTP 요청을 브라우저에서 만들 수 있는 경우 이 지침을 따릅니다.
  * [Razor Pages 앱에서 라우팅 가능한 구성 요소 사용](#use-routable-components-in-a-razor-pages-app)
  * [MVC 앱에서 라우팅 가능한 구성 요소 사용](#use-routable-components-in-an-mvc-app)
* [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view): 사용자 요청에서 직접 라우팅할 수 없는 구성 요소. [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 앱이 구성 요소를 기존 페이지와 뷰에 포함하는 경우 이 지침을 따릅니다.

## <a name="configuration"></a>Configuration

기존 Razor Pages 또는 MVC 앱은 페이지와 뷰에 Razor 구성 요소를 통합할 수 있습니다.

1. 앱의 레이아웃 파일(`_Layout.cshtml`)에서 다음을 수행합니다.

   * `<head>` 요소에 다음 `<base>` 태그를 추가합니다.

     ```html
     <base href="~/" />
     ```

     위의 예제에서 `href` 값(‘앱 기본 경로’)은 앱이 루트 URL 경로(`/`)에 있는 것으로 가정합니다. 앱이 하위 애플리케이션인 경우, <xref:blazor/host-and-deploy/index#app-base-path> 문서의 ‘앱 기본 경로’ 섹션에 설명된 지침을 따르세요.

     `_Layout.cshtml` 파일은 Razor Pages 앱의 `Pages/Shared` 폴더 또는 MVC 앱의 `Views/Shared` 폴더에 있습니다.

   * `Scripts` 렌더링 섹션 바로 앞에 `blazor.server.js` 스크립트의 `<script>` 태그를 추가합니다.

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     프레임워크가 `blazor.server.js` 스크립트를 앱에 추가합니다. 앱에 스크립트를 수동으로 추가할 필요는 없습니다.

1. 다음 콘텐츠를 사용하여 프로젝트의 루트 폴더에 `_Imports.razor` 파일을 추가합니다(마지막 네임스페이스인 `MyAppNamespace`를 앱의 네임스페이스로 변경).

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

1. `Startup.ConfigureServices`에서 Blazor Server 서비스를 등록합니다.

   ```csharp
   services.AddServerSideBlazor();
   ```

1. `Startup.Configure`에서 `app.UseEndpoints`에 Blazor 허브 엔드포인트를 추가합니다.

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 페이지 또는 뷰에 구성 요소를 통합합니다. 자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조하세요.

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Razor Pages 앱에서 라우팅 가능한 구성 요소 사용

‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’

Razor Pages 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.

1. [구성](#configuration) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용하여 프로젝트 루트에 `App.razor` 파일을 추가합니다.

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. 다음 콘텐츠를 사용하여 `Pages` 폴더에 `_Host.cshtml` 파일을 추가합니다.

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   구성 요소는 해당 레이아웃에 공유 `_Layout.cshtml` 파일을 사용합니다.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.

   * 페이지에 미리 렌더링할지 여부
   * 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

   매개 변수 전달과 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 구성을 포함하여 구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

1. `Startup.Configure`의 엔드포인트 구성에 `_Host.cshtml` 페이지의 우선순위가 낮은 경로를 추가합니다.

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 라우팅 가능한 구성 요소를 앱에 추가합니다. 예를 들어:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.

## <a name="use-routable-components-in-an-mvc-app"></a>MVC 앱에서 라우팅 가능한 구성 요소 사용

‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’

MVC 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.

1. [구성](#configuration) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용하여 프로젝트 루트에 `App.razor` 파일을 추가합니다.

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. 다음 콘텐츠를 사용하여 `Views/Home` 폴더에 `_Host.cshtml` 파일을 추가합니다.

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   구성 요소는 해당 레이아웃에 공유 `_Layout.cshtml` 파일을 사용합니다.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.

   * 페이지에 미리 렌더링할지 여부
   * 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

   매개 변수 전달과 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 구성을 포함하여 구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

1. 홈 컨트롤러에 작업을 추가합니다.

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. `Startup.Configure`의 엔드포인트 구성에 `_Host.cshtml` 뷰를 반환하는 컨트롤러 작업의 우선순위가 낮은 경로를 추가합니다.

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. `Pages` 폴더를 만들고 라우팅 가능한 구성 요소를 앱에 추가합니다. 예를 들어:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.

## <a name="render-components-from-a-page-or-view"></a>페이지 또는 뷰에서 구성 요소 렌더링

‘이 섹션에서는 사용자 요청에서 직접 구성 요소를 라우팅할 수 없는 페이지 또는 뷰에 구성 요소를 추가하는 방법을 설명합니다.’

페이지 또는 뷰에서 구성 요소를 렌더링하려면 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용합니다.

### <a name="render-stateful-interactive-components"></a>상태 저장 대화형 구성 요소 렌더링

Razor 페이지 또는 뷰에 상태 저장 대화형 구성 요소를 추가할 수 있습니다.

페이지 또는 뷰를 렌더링하는 경우와 관련해서 다음 사항을 확인합니다.

* 구성 요소가 페이지 또는 뷰와 함께 미리 렌더링됩니다.
* 미리 렌더링하는 데 사용된 초기 구성 요소 상태가 손실됩니다.
* SignalR 연결이 완료되면 새 구성 요소 상태가 생성됩니다.

다음 Razor 페이지는 `Counter` 구성 요소를 렌더링합니다.

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

### <a name="render-noninteractive-components"></a>비대화형 구성 요소 렌더링

다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 통해 지정된 초기 값을 사용하여 정적으로 렌더링됩니다. 구성 요소가 정적으로 렌더링되므로 구성 요소는 대화형이 아닙니다.

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

자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

## <a name="component-namespaces"></a>구성 요소 네임스페이스

사용자 지정 폴더를 사용하여 앱의 구성 요소를 저장하는 경우, 폴더를 나타내는 네임스페이스를 페이지/뷰 또는 `_ViewImports.cshtml` 파일에 추가합니다. 다음 예제에서는

* `MyAppNamespace`를 앱의 네임스페이스로 변경합니다.
* 구성 요소를 저장하는 데 `Components` 폴더를 사용하지 않은 경우, `Components`를 구성 요소가 있는 폴더로 변경합니다.

```cshtml
@using MyAppNamespace.Components
```

`_ViewImports.cshtml` 파일은 Razor Pages 앱의 `Pages` 폴더 또는 MVC 앱의 `Views` 폴더에 있습니다.

자세한 내용은 <xref:blazor/components/index#namespaces>를 참조하세요.

::: zone-end
