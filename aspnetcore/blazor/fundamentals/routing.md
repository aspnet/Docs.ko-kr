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
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279890"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 라우팅

이 문서에서는 요청을 라우팅하는 방법과 Blazor 앱에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.

## <a name="route-templates"></a>경로 템플릿

<xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 Blazor 앱에서 Razor 구성 요소로 라우팅할 수 있습니다. <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 Blazor 앱의 `App` 구성 요소에서 사용됩니다.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[`@page` 지시문](xref:mvc/views/razor#page)을 포함하는 Razor 구성 요소(`.razor`)를 컴파일하면 생성된 구성요소 클래스에 구성 요소의 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.

앱이 시작하면 라우터의 `AppAssembly`로 지정된 어셈블리를 검사하여 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 있는 앱의 구성 요소에 대한 경로 정보를 수집합니다.

런타임에 <xref:Microsoft.AspNetCore.Components.RouteView> 구성 요소는 다음을 수행합니다.

* 경로 매개 변수와 함께 <xref:Microsoft.AspNetCore.Components.Routing.Router>에서 <xref:Microsoft.AspNetCore.Components.RouteData>를 받습니다.
* 추가로 중첩된 레이아웃을 포함하여 해당 [레이아웃](xref:blazor/layouts)과 함께 지정된 구성 요소를 렌더링합니다.

필요한 경우, [`@layout` 지시문](xref:blazor/layouts#specify-a-layout-in-a-component)으로 레이아웃을 지정하지 않는 구성 요소에 레이아웃 클래스가 포함된 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 매개 변수를 지정합니다. 프레임워크의 Blazor 프로젝트 템플릿은 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)를 앱의 기본 레이아웃으로 지정합니다. 레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.

구성 요소는 여러 [`@page` 지시문](xref:mvc/views/razor#page)을 사용하여 여러 경로 템플릿을 지원합니다. 다음 예제 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청을 로드합니다.

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> URL을 올바르게 확인하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 `<base>` 태그가 있어야 하고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다. 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.

## <a name="provide-custom-content-when-content-isnt-found"></a>콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공

<xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.

`App` 구성 요소에서 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소의 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 템플릿에 사용자 지정 콘텐츠를 설정합니다.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

다른 대화형 구성 요소와 같은 임의 항목이 `<NotFound>` 태그의 콘텐츠로 지원됩니다. <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts#default-layout>을 참조하세요.

## <a name="route-to-components-from-multiple-assemblies"></a>여러 어셈블리에서 구성 요소로 라우팅

<xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대해 지정할 수 있습니다. `AppAssembly`에 지정된 어셈블리 외에도 추가 어셈블리가 검색됩니다. 다음 예제에서 `Component1`은 참조된 [구성 요소 클래스 라이브러리](xref:blazor/components/class-libraries)에서 정의된 라우팅 가능한 구성 요소입니다. 다음 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a>경로 매개 변수

라우터는 경로 매개 변수를 사용하여 해당 [구성 요소 매개 변수](xref:blazor/components/index#component-parameters)를 동일한 이름으로 채웁니다. 경로 매개 변수 이름은 대/소문자를 구분하지 않습니다. 다음 예제에서 `text` 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다. `/RouteParameter/amazing`에 대한 요청을 수행하면 `<h1>` 태그 콘텐츠가 `Blazor is amazing!`으로 렌더링됩니다.

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

선택적 매개 변수가 지원됩니다. 다음 예제에서 `text` 선택적 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다. 세그먼트가 없으면 `Text` 값이 `fantastic`으로 설정됩니다.

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

선택적 매개 변수는 지원되지 않습니다. 다음 예제에서는 두 개의 [`@page` 지시문](xref:mvc/views/razor#page)이 적용되었습니다. 첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다. 두 번째 지시문은 `{text}` 경로 매개 변수 값을 구성 요소의 `Text` 속성에 할당합니다.

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

다른 선택적 매개 변수 값을 사용하여 동일한 구성 요소에 대한 앱 탐색을 허용하려면 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 대신 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set)를 사용합니다. 앞의 예제를 기반으로 하여, `/RouteParameter`에서 `/RouteParameter/amazing`으로, 또는 `/RouteParameter/amazing`에서 `/RouteParameter`로 이동할 수 있어야 할 때 `OnParametersSet`를 사용합니다.

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>경로 제약 조건

경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.

다음 예제에서 `User` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.

* 요청 URL에 `Id` 경로 세그먼트가 있는 경우
* `Id` 세그먼트가 정수(`int`) 형식인 경우

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다. 고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.

| 제약 조건 | 예제           | 일치하는 예제                                                                  | 고정<br>culture<br>일치 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | 아니요                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | 예                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | 예                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | 예                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | 예                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 아니요                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | 예                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | 예                              |

> [!WARNING]
> CLR 형식(예: `int` 또는 <xref:System.DateTime>)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다. 이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.

## <a name="routing-with-urls-that-contain-dots"></a>점이 포함된 URL을 사용한 라우팅

호스트된 Blazor WebAssembly 및 Blazor Server 앱의 경우 서버 쪽 기본 경로 템플릿에서 요청 URL의 마지막 세그먼트에 파일을 요청하는 점(`.`)이 포함되어 있다고 가정합니다. 예를 들어 URL `https://localhost.com:5001/example/some.thing`은 라우터에서 `some.thing`라는 파일에 대한 요청으로 해석됩니다. `some.thing`이 [`@page` 지시문](xref:mvc/views/razor#page)을 사용하여 구성 요소에 라우팅되기 위한 것이고 `some.thing`이 경로 매개 변수 값인 경우 추가 구성이 없는 앱은 ‘404 - 찾을 수 없음’ 응답을 반환합니다. 점이 포함된 하나 이상의 매개 변수가 있는 경로를 사용하려면 앱에서 사용자 지정 템플릿을 사용하여 경로를 구성해야 합니다.

URL의 마지막 세그먼트에서 경로 매개 변수를 받을 수 있는 다음 `Example` 구성 요소를 고려합니다.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

호스트된 Blazor WebAssembly 솔루션의 *`Server`* 앱이 `param` 경로 매개 변수에 점을 사용하여 요청을 라우팅하도록 허용하려면 `Startup.Configure`에서 선택적 매개 변수를 사용하여 대체 파일 경로 템플릿을 추가합니다.

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

`param` 경로 매개 변수에서 점을 사용하여 요청을 라우팅하도록 Blazor Server 앱을 구성하려면 `Startup.Configure`에서 선택적 매개 변수를 사용하여 대체 페이지 경로 템플릿을 추가합니다.

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

자세한 내용은 <xref:fundamentals/routing>를 참조하세요.

## <a name="catch-all-route-parameters"></a>모든 경로 매개 변수 catch

::: moniker range=">= aspnetcore-5.0"

여러 폴더 경계에서 경로를 캡처하는 catch-all 경로 매개 변수는 구성 요소에서 지원됩니다.

Catch-all 경로 매개 변수는 다음과 같습니다.

* 이름이 경로 세그먼트 이름과 일치합니다. 이름 지정에서 대소문자를 구분하지 않습니다.
* `string` 형식입니다. 프레임워크가 자동 캐스팅을 제공하지 않습니다.
* URL의 끝부분에 있습니다.

`Pages/CatchAll.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

`/catch-all/{*pageRoute}`의 경로 템플릿이 있는 URL `/catch-all/this/is/a/test`의 경우 `PageRoute`의 값이 `this/is/a/test`로 설정됩니다.

캡처된 경로의 슬래시 및 세그먼트가 디코딩됩니다. `/catch-all/{*pageRoute}`의 경로 템플릿의 경우 `/catch-all/this/is/a%2Ftest%2A` URL은 `this/is/a/test*`를 생성합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

catch-all 경로 매개 변수는 ASP.NET Core 5.0 이상에서 지원됩니다. 자세한 내용을 보려면 이 문서의 5.0 버전을 선택합니다.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>URI 및 탐색 상태 도우미

<xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 C# 코드로 URI와 탐색을 관리할 수 있습니다. <xref:Microsoft.AspNetCore.Components.NavigationManager>는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.

| 멤버 | 설명 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | 현재 절대 URI를 가져옵니다. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | 절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다. 일반적으로 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>는 `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | 지정한 URI로 이동합니다. `forceLoad`가 `true`인 경우<ul><li>클라이언트 쪽 라우팅이 무시됩니다.</li><li>클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | 탐색 위치가 변경된 경우에 발생하는 이벤트입니다. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | 상대 URI를 절대 URI로 변환합니다. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | 기본 URI(예: 이전에 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다. |

<xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 이벤트의 경우 <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>는 탐색 이벤트에 대해 다음 정보를 제공합니다.

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: 새 위치의 URL입니다.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: `true`인 경우 Blazor는 브라우저에서 탐색을 가로챘습니다. `false`인 경우 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>에서 탐색이 발생했습니다.

다음 구성 요소는

* <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>를 사용하여 단추를 선택하면 앱의 `Counter` 구성 요소(`Pages/Counter.razor`)로 이동합니다.
* <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>를 구독하여 위치 변경 이벤트를 처리합니다.
  * `HandleLocationChanged` 메서드는 프레임워크에서 `Dispose`를 호출할 때 언후크됩니다. 메서드를 언후크하면 구성 요소의 가비지 수집이 허용됩니다.
  * 로거 구현은 단추를 선택할 때 다음 정보를 기록합니다.

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.

## <a name="query-string-and-parse-parameters"></a>쿼리 문자열 및 구문 분석 매개 변수

요청의 쿼리 문자열은 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> 속성에서 가져옵니다.

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

쿼리 문자열의 매개 변수를 구문 분석하려면

* 앱은 <xref:Microsoft.AspNetCore.WebUtilities> API를 사용할 수 있습니다. 앱에서 API를 사용할 수 없는 경우 [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)에 대한 앱의 프로젝트 파일에 패키지 참조를 추가합니다.
* <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>을 사용하여 쿼리 문자열을 구문 분석한 후 값을 가져옵니다.

다음 `ParseQueryString` 구성 요소 예제에서는 `ship`이라는 쿼리 문자열 매개 변수 키를 구문 분석합니다. 예를 들어 URL 쿼리 문자열 키-값 쌍 `?ship=Tardis`는 `queryValue`에서 `Tardis` 값을 캡처합니다. 다음 예제에서는 URL `https://localhost:5001/parse-query-string?ship=Tardis`를 사용하여 앱으로 이동합니다.

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a>`NavLink` 및 `NavMenu` 구성 요소

탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용합니다. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다. `active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다. 현재 경로가 `href`와 일치하는 경우, 필요에 따라 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>에 CSS 클래스 이름을 할당하여 렌더링된 링크에 사용자 지정 CSS 클래스를 적용합니다.

다음 `NavMenu` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하는 방법을 보여 주는 [`Bootstrap`](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> `NavMenu` 구성 요소(`NavMenu.razor`)는 Blazor 프로젝트 템플릿에서 생성된 앱의 `Shared` 폴더에 제공됩니다.

`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 옵션이 있습니다.

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: 현재 URL 전체와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(기본값): 현재 URL의 접두사와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.

위의 예제에서 홈 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다. 두 번째 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>는 사용자가 `component` 접두사를 포함하는 URL(예: `https://localhost:5001/component` 및 `https://localhost:5001/component/another-segment`)을 방문할 때 `active` 클래스를 받습니다.

추가 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다. 다음 예제에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `target` 특성을 포함합니다.

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

다음 HTML 태그가 렌더링됩니다.

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> Blazor가 자식 콘텐츠를 렌더링하는 방식 때문에 `for` 루프 내에서 `NavLink` 구성 요소를 렌더링하려면 `NavLink`(자식) 구성 요소 콘텐츠에서 증분 루프 변수를 사용할 경우 로컬 인덱스 변수가 필요합니다.
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
> 이 시나리오에서 인덱스 변수를 사용하는 것은 `NavLink` 구성 요소뿐 아니라 [자식 콘텐츠](xref:blazor/components/index#child-content)에서 루프 변수를 사용하는 **모든** 자식 구성 요소의 요구 사항입니다.
>
> 또는 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>을 활용하여 `foreach` 루프를 사용할 수 있습니다.
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

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 엔드포인트 라우팅 통합

‘이 섹션은 Blazor Server 앱에만 적용됩니다.’

Blazor Server는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다. `Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

일반적인 구성은 Blazor Server 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다. 규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 앱의 `Pages` 폴더에서 `_Host.cshtml`입니다.

호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다. 일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다. 이렇게 하면 앱이 Blazor Server 앱의 구성 요소 라우팅을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.

루트가 아닌 URL 서버 호스팅을 위한 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 구성에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.
