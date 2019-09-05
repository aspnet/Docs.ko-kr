---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법 및 NavLink 구성 요소에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: 067dad657c1e89a31fac45fdfa095cce4b10798d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238054"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 라우팅

[Luke Latham](https://github.com/guardrex)으로

Blazor apps에서 요청을 라우팅하는 방법 및 `NavLink` 구성 요소를 사용 하 여 탐색 링크를 만드는 방법을 알아봅니다.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 끝점 라우팅 통합

Blazor 서버 쪽은 [ASP.NET Core 끝점 라우팅](xref:fundamentals/routing)에 통합 됩니다. ASP.NET Core 앱은 `MapBlazorHub` 에서 `Startup.Configure`대화형 구성 요소에 대 한 들어오는 연결을 허용 하도록 구성 됩니다.

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>경로 템플릿

구성 `Router` 요소는 라우팅을 활성화 하 고 액세스 가능한 각 구성 요소에 경로 템플릿이 제공 됩니다. 구성 요소가 다음과 같이 *응용 프로그램 razor* 파일에 나타납니다. `Router`

Blazor 서버 쪽 앱에서:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

Blazor 클라이언트 쪽 앱에서:

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

`@page` 지시문을 사용 하는 *razor* 파일이 컴파일되면 생성 된 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 클래스에 경로 템플릿을 지정 하는이 제공 됩니다. 런타임에 라우터는를 `RouteAttribute` 사용 하 여 구성 요소 클래스를 검색 하 고 요청 된 URL과 일치 하는 경로 템플릿을 사용 하 여 구성 요소를 렌더링 합니다.

여러 경로 템플릿을 구성 요소에 적용할 수 있습니다. 다음 구성 요소는 및 `/BlazorRoute` `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> 경로를 올바르게 생성 `<base>` 하려면 앱에서 `href` 특성에 지정 된 앱 기본 경로를 사용 하 여 *wwwroot/index.html* 파일 (Blazor client side) 또는 *Pages/_Host* 파일 (Blazor server side)에 태그를 포함 해야 합니다. `<base href="/">`). 자세한 내용은 <xref:host-and-deploy/blazor/client-side#app-base-path>을 참조하세요.

## <a name="provide-custom-content-when-content-isnt-found"></a>콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공

요청 된 경로에 대 한 콘텐츠를 찾을 수 없는 경우 구성요소를사용하여앱에서사용자지정콘텐츠를지정할수있습니다.`Router`

*응용 프로그램 razor* 파일에서 `<NotFoundContent>` `Router` 구성 요소의 요소에 사용자 지정 콘텐츠를 설정 합니다.

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

의 `<NotFoundContent>` 콘텐츠에는 다른 대화형 구성 요소와 같은 임의의 항목이 포함 될 수 있습니다.

## <a name="route-parameters"></a>경로 매개 변수

라우터는 경로 매개 변수를 사용 하 여 해당 구성 요소 매개 변수를 동일한 이름 (대/소문자 구분 안 함)으로 채웁니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

선택적 매개 변수는 ASP.NET Core 3.0 미리 보기에서 Blazor apps에 대해 지원 되지 않습니다. 이전 `@page` 예제에서는 두 개의 지시문이 적용 됩니다. 첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다. 두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용 하 여 값을 `Text` 속성에 할당 합니다.

## <a name="route-constraints"></a>경로 제약 조건

경로 제약 조건은 경로 세그먼트에 대 한 형식 일치를 구성 요소에 적용 합니다.

다음 예제에서 `Users` 구성 요소에 대 한 경로는 다음과 같은 경우에만 일치 합니다.

* 요청 URL에 경로 세그먼트가 있습니다. `Id`
* 세그먼트가 정수 (`int`)입니다. `Id`

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

다음 표에 표시 된 경로 제약 조건을 사용할 수 있습니다. 고정 문화권과 일치 하는 경로 제약 조건에 대 한 자세한 내용은 테이블 아래 경고를 참조 하세요.

| 제약 조건 | 예제           | 일치하는 예제                                                                  | 고정<br>Culture<br>일치 |
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
> CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다. 이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.

### <a name="routing-with-urls-that-contain-dots"></a>점이 포함 된 Url로 라우팅

Blazor 서버 쪽 앱에서 *_Host* `/` 의 기본 경로는 (`@page "/"`)입니다. Url이 파일을 요청 하는 것`.`으로 나타나기 때문에 점 ()이 포함 된 요청 url이 기본 경로와 일치 하지 않습니다. Blazor 앱은 존재 하지 않는 정적 파일에 대해 *404-찾을 수* 없음 응답을 반환 합니다. 점이 포함 된 경로를 사용 하려면 다음 경로 템플릿을 사용 하 여 _Host를 구성 *합니다* .

```cshtml
@page "/{**path}"
```

템플릿은 `"/{**path}"` 다음을 포함 합니다.

* 큰따옴표 ()를 *사용* 하 여 여러`**`폴더 경계에서 경로를 캡처하는 두 개의 별표를 사용 하 여`/`슬래시 ()를 사용 합니다.
* `path` 경로 매개 변수 이름입니다.

자세한 내용은 <xref:fundamentals/routing>을 참조하세요.

## <a name="navlink-component"></a>NavLink 구성 요소

탐색 링크 `NavLink` 를 만들 때 HTML 하이퍼링크 요소 (`<a>`) 대신 구성 요소를 사용 합니다. 구성 `NavLink` 요소는 `<a>` 요소가 현재 URL `href` 과 일치 하는지 여부에 `active` 따라 CSS 클래스를 전환 하는 것을 제외 하 고 요소 처럼 동작 합니다. 클래스 `active` 를 통해 사용자는 표시 된 탐색 링크 중에서 활성 페이지가 되는 페이지를 이해할 수 있습니다.

다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

요소의`<NavLink>` `NavLinkMatch` 특성`Match` 에 할당할 수 있는 두 가지 옵션이 있습니다.

* `NavLinkMatch.All`&ndash; 는`NavLink` 전체 현재 URL과 일치 하는 경우 활성 상태입니다.
* `NavLinkMatch.Prefix`(*기본값*) &ndash; 는`NavLink` 현재 URL의 접두사와 일치 하는 경우 활성 상태입니다.

`NavLink` 위의 예제에서 홈 `href=""` 은 홈 `active` URL과 일치 하 고 앱의 `https://localhost:5001/`기본 기본 경로 URL (예:)에서 CSS 클래스를 수신 합니다. 두 번째 `NavLink` 는 사용자 `active` 가 `MyComponent` 접두사 (예: 및 `https://localhost:5001/MyComponent/AnotherSegment`)를 사용 하 여 URL을 `https://localhost:5001/MyComponent` 방문할 때 클래스를 수신 합니다.

추가 `NavLink` 구성 요소 특성이 렌더링 된 앵커 태그로 전달 됩니다. 다음 예의 구성 요소는 `NavLink` `target` 특성을 포함 합니다.

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

렌더링 되는 HTML 태그는 다음과 같습니다.

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI 및 탐색 상태 도우미

를 `Microsoft.AspNetCore.Components.IUriHelper` 사용 하 여 코드에서 C# uri 및 탐색 작업을 수행 합니다. `IUriHelper`는 다음 표에 나와 있는 이벤트와 메서드를 제공 합니다.

| 멤버 | 설명 |
| ------ | ----------- |
| `GetAbsoluteUri` | 현재 절대 URI를 가져옵니다. |
| `GetBaseUri` | 절대 URI를 생성 하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI (후행 슬래시 포함)를 가져옵니다. 일반적으로 `GetBaseUri` 는 *wwwroot/index.html* ( `href` Blazor) 또는 *Pages/_Host* (Blazor 서버측)의 문서 `<base>` 요소에 있는 특성에 해당 합니다. |
| `NavigateTo` | 지정 된 URI로 이동 합니다. `forceLoad` 가`true`다음과 같은 경우:<ul><li>클라이언트 쪽 라우팅이 무시 됩니다.</li><li>URI가 일반적으로 클라이언트 쪽 라우터에서 처리 되는지 여부와 상관 없이 브라우저는 서버에서 새 페이지를 강제로 로드 합니다.</li></ul> |
| `OnLocationChanged` | 탐색 위치가 변경 될 때 발생 하는 이벤트입니다. |
| `ToAbsoluteUri` | 상대 URI를 절대 URI로 변환 합니다. |
| `ToBaseRelativePath` | 기본 uri가 지정 된 경우 (예: 이전에에서 `GetBaseUri`반환 된 uri)는 절대 uri를 기본 uri 접두사에 상대적인 uri로 변환 합니다. |

다음 구성 요소는 단추를 선택 하면 `Counter` 앱의 구성 요소로 이동 합니다.

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
