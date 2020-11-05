---
title: Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
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
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056259"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a>Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

Razor Pages 및 MVC 앱에 Razor 구성 요소를 통합할 수 있습니다. 페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.

[앱을 준비](#prepare-the-app)한 후 앱 요구 사항에 따라 다음 섹션의 지침을 따릅니다.

* 라우팅 가능한 구성 요소: 사용자 요청에서 직접 라우팅할 수 있는 구성 요소. 방문자가 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 구성 요소에 대한 HTTP 요청을 브라우저에서 만들 수 있는 경우 이 지침을 따릅니다.
  * [Razor Pages 앱에서 라우팅 가능한 구성 요소 사용](#use-routable-components-in-a-razor-pages-app)
  * [MVC 앱에서 라우팅 가능한 구성 요소 사용](#use-routable-components-in-an-mvc-app)
* [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view): 사용자 요청에서 직접 라우팅할 수 없는 구성 요소. [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 앱이 구성 요소를 기존 페이지와 뷰에 포함하는 경우 이 지침을 따릅니다.

## <a name="prepare-the-app"></a>앱 준비

기존 Razor Pages 또는 MVC 앱은 페이지와 뷰에 Razor 구성 요소를 통합할 수 있습니다.

1. 앱의 레이아웃 파일(`_Layout.cshtml`)에서 다음을 수행합니다.

   * `<head>` 요소에 다음 `<base>` 태그를 추가합니다.

     ```html
     <base href="~/" />
     ```

     위의 예제에서 `href` 값(‘앱 기본 경로’)은 앱이 루트 URL 경로(`/`)에 있는 것으로 가정합니다. 앱이 하위 애플리케이션인 경우, <xref:blazor/host-and-deploy/index#app-base-path> 문서의 ‘앱 기본 경로’ 섹션에 설명된 지침을 따르세요.

     `_Layout.cshtml` 파일은 Razor Pages 앱의 *Pages/Shared* 폴더 또는 MVC 앱의 *Views/Shared* 폴더에 있습니다.

   * 닫는 `</body>` 태그 바로 앞에 *blazor.server.js* 스크립트의 `<script>` 태그를 추가합니다.

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     프레임워크가 *blazor.server.js* 스크립트를 앱에 추가합니다. 앱에 스크립트를 수동으로 추가할 필요는 없습니다.

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

1. [앱 준비](#prepare-the-app) 섹션의 지침을 따릅니다.

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

   | 렌더링 모드 | 설명 |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` 구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server 앱의 마커를 렌더링합니다. `App` 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` 구성 요소를 정적 HTML에 렌더링합니다. |

   구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

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

1. [앱 준비](#prepare-the-app) 섹션의 지침을 따릅니다.

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

   | 렌더링 모드 | 설명 |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` 구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server 앱의 마커를 렌더링합니다. `App` 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` 구성 요소를 정적 HTML에 렌더링합니다. |

   구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

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
* 구성 요소를 저장하는 데 *Components* 폴더를 사용하지 않은 경우, `Components`를 구성 요소가 있는 폴더로 변경합니다.

```cshtml
@using MyAppNamespace.Components
```

`_ViewImports.cshtml` 파일은 Razor Pages 앱의 `Pages` 폴더 또는 MVC 앱의 `Views` 폴더에 있습니다.

자세한 내용은 <xref:blazor/components/index#namespaces>를 참조하세요.
