---
title: ASP.NET Core의 앵커 태그 도우미
author: pkellner
description: ASP.NET Core 앵커 태그 도우미 특성 및 HTML 앵커 태그의 작업을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 2e49c545b0d343475ce44a636a6ae66324f9d3bf
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587205"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>ASP.NET Core의 앵커 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net) 및 [Scott Addie](https://github.com/scottaddie)

[앵커 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper)는 표준 HTML 앵커(`<a ... ></a>`) 태그에 새로운 특성을 추가하여 향상시킵니다. 규칙에 따라 해당 특성들의 이름은 `asp-` 접두사로 시작합니다. 렌더링 되는 앵커 요소의 `href` 특성 값은 `asp-` 특성들의 값에 따라 결정됩니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서의 예제 전반에서는 다음의 *SpeakerController* 가 사용됩니다.

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a>앵커 태그 도우미 특성

### <a name="asp-controller"></a>asp-controller

[asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) 특성은 URL 생성에 사용되는 컨트롤러를 할당합니다. 다음 태그는 모든 스피커를 나열합니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker">All Speakers</a>
```

`asp-controller` 특성이 지정되고 `asp-action`이 지정되지 않으면, 기본 `asp-action` 값은 현재 실행 중인 보기와 연결된 컨트롤러 작업입니다. *HomeController* 의 *Index* 뷰(*/Home*)에서 위의 태그에서 `asp-action`을 생략한 앵커 태그 도우미를 사용할 경우, 생성되는 HTML은 다음과 같습니다.

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>asp-action

[asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) 특성 값은 생성되는 `href` 특성에 포함되는 컨트롤러 작업의 이름을 나타냅니다. 다음 태그는 생성되는 `href` 특성 값을 스피커 평가 페이지로 설정합니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

`asp-controller` 특성이 지정되지 않으면 현재 보기를 실행하는 보기를 호출하는 기본 컨트롤러가 사용됩니다.

`asp-action` 특성 값이 `Index`이면 URL에 아무 작업도 추가되지 않으므로 기본 `Index` 작업이 호출됩니다. 지정된(또는 기본값으로 설정된) 작업은 `asp-controller`에서 참조되는 컨트롤러에 있어야 합니다.

### <a name="asp-route-value"></a>asp-route-{value}

[asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 특성은 와일드카드 경로 접두사를 사용하도록 설정합니다. `{value}` 자리 표시자에 위치하는 모든 값은 잠재적인 경로 매개 변수로 해석됩니다. 기본 경로가 발견되지 않으면 이 경로 접두사는 생성되는 `href` 특성에 요청 매개 변수 및 값으로 추가됩니다. 그렇지 않으면 경로 템플릿에서 이 경로 접두사가 대체됩니다.

다음 컨트롤러 작업을 고려해보세요.

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

*Startup.Configure* 에 정의된 기본 경로 템플릿을 사용할 경우:

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

다음과 같이 MVC 보기가 작업을 통해서 제공되는 모델을 사용하면:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

기본 경로의 `{id?}` 자리 표시자가 일치했습니다. 생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

다음 MVC 보기와 같이 경로 접두사가 라우팅 템플릿의 일부와 일치하지 않는다고 가정해보세요.

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

일치하는 경로에서 `speakerid`를 찾을 수 없기 때문에 생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

`asp-controller` 또는 `asp-action`이 지정되지 않으면 `asp-route` 특성과 동일한 기본 처리가 수행됩니다.

### <a name="asp-route"></a>asp-route

[asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) 특성은 명명된 경로에 직접 연결되는 URL을 만드는 데 사용됩니다. [라우팅 특성](xref:mvc/controllers/routing#attribute-routing)을 사용하면 경로에 `SpeakerController`에서 볼 수 있는 것과 같이 이름을 지정할 수 있고 `Evaluations` 작업에서처럼 사용할 수 있습니다.

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

다음 태그에서 `asp-route` 특성은 명명된 경로를 참조합니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

앵커 태그 도우미는 */Speaker/Evaluations* URL을 사용하여 해당 컨트롤러 작업을 직접 가리키는 경로를 생성합니다. 생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

`asp-route`에 더해 `asp-controller` 또는 `asp-action`을 함께 지정하면 기대하는 것과 다른 경로가 생성될 수 있습니다. 경로 충돌을 방지하려면 `asp-route`를 `asp-controller` 및 `asp-action` 특성과 함께 사용하면 안됩니다.

### <a name="asp-all-route-data"></a>asp-all-route-data

[asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 특성은 키-값 쌍 사전을 만들 수 있도록 지원합니다. 키는 매개 변수 이름이고, 값은 매개 변수 값입니다.

다음 예제에서는 사전이 초기화 되어 뷰에 전달 됩니다 Razor . 또는 모델을 사용하여 데이터를 전달할 수도 있습니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

이전 코드로 생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

`asp-all-route-data` 사전은 병합되어 오버로드된 `Evaluations` 작업의 요구 사항을 충족하는 쿼리 문자열을 생성합니다.

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

사전에 존재하는 키가 경로 매개 변수와 일치하면 경로에서 해당 값이 적절하게 대체됩니다. 나머지 일치하지 않는 다른 값들은 요청 매개 변수로 생성됩니다.

### <a name="asp-fragment"></a>asp-fragment

[asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) 특성은 URL에 추가할 URL 조각을 정의합니다. 앵커 태그 도우미는 해시 문자(#)를 추가합니다. 다음 태그를 살펴보세요.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

해시 태그는 클라이언트 쪽 앱을 만들 때 유용합니다. 다음과 같이 JavaScript에서 손쉽게 만들고 검색하는 데 사용할 수 있습니다.

### <a name="asp-area"></a>asp-area

[asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) 특성은 적절한 경로를 설정하는 데 사용되는 영역 이름을 설정합니다. 이어지는 예제는 `asp-area` 특성이 경로를 다시 매핑하는 방법을 보여줍니다.

#### <a name="usage-in-razor-pages"></a>페이지에서 사용 Razor

Razor 페이지 영역은 ASP.NET Core 2.1 이상에서 지원 됩니다.

다음 디렉터리 계층 구조를 고려해보세요.

* **{프로젝트 이름}**
  * **wwwroot**
  * **Areas**
    * **세션**
      * **페이지**
        * *\_ViewStart. cshtml*
        * *Index.cshtml*
        * *Index.cshtml.cs*
  * **페이지**

*세션* 영역 *인덱스* 페이지를 참조할 태그는 Razor 다음과 같습니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> 페이지 앱에서 영역을 지원 하려면 Razor 에서 다음 중 하나를 수행 합니다 `Startup.ConfigureServices` .
>
> * [호환성 버전](xref:mvc/compatibility-version)을 2.1 이상으로 설정합니다.
> * 작업 [ Razor 옵션. allowareas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) 속성을 다음으로 설정 합니다. `true`
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a>MVC에서 사용하는 방법

다음 디렉터리 계층 구조를 고려해보세요.

* **{프로젝트 이름}**
  * **wwwroot**
  * **Areas**
    * **Blogs**
      * **컨트롤러**
        * *HomeController.cs*
      * **뷰**
        * **Home**
          * *AboutBlog.cshtml*
          * *Index.cshtml*
        * *\_ViewStart. cshtml*
  * **컨트롤러**

`asp-area`를 "Blogs"로 설정하면 해당 앵커 태그에 연결된 컨트롤러 및 보기의 경로에 *Areas/Blogs* 디렉터리가 접두사로 추가됩니다. *AboutBlog* 보기를 참조하는 태그는 다음과 같습니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> MVC 앱에서 영역을 지원하려면 경로 템플릿에 해당 영역에 대한 참조가 포함되어야 합니다(존재할 경우). 해당 템플릿은 *Startup.Configure* 에서 `routes.MapRoute` 메서드 호출의 두 번째 매개 변수에 표시됩니다.
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>asp-protocol

[asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) 특성은 URL에서 프로토콜(예: `https`)을 지정하는 데 사용됩니다. 예를 들면 다음과 같습니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="https://localhost/Home/About">About</a>
```

이 예제의 호스트 이름은 localhost입니다. 앵커 태그 도우미는 URL을 생성할 때 웹 사이트의 공용 도메인을 사용합니다.

### <a name="asp-host"></a>asp-host

[asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) 특성은 URL에 호스트 이름을 지정하는 데 사용됩니다. 예를 들면 다음과 같습니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>asp-page

[Asp 페이지](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) 특성은 페이지와 함께 사용 됩니다 Razor . 이 특성은 앵커 태그의 `href` 특성 값을 특정 페이지로 설정하는 데 사용됩니다. 페이지 이름 앞에 슬래시("/")를 접두사로 사용해서 URL을 생성합니다.

다음 샘플에서는 참석자 페이지를 가리킵니다 Razor .

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Attendee">All Attendees</a>
```

`asp-page` 특성은 `asp-route`, `asp-controller` 및 `asp-action` 특성과 상호 배타적입니다. 그러나 다음 태그에서 보여 주듯이 `asp-page`는 `asp-route-{value}`와 함께 사용하여 라우팅을 제어할 수 있습니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>asp-page-handler

[Asp-페이지 처리기](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) 특성은 페이지와 함께 사용 됩니다 Razor . 이 특성은 특정 페이지 처리기에 연결하기 위한 것입니다.

다음 페이지 처리기를 고려해보세요.

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

페이지 모델의 관련 태그는 `OnGetProfile` 페이지 처리기에 연결됩니다. 페이지 처리기 메서드 이름의 `On<Verb>` 접두사는 `asp-page-handler` 특성 값에서 생략됩니다. 비동기 메서드인 경우 `Async` 접미사도 생략됩니다.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

생성되는 HTML은 다음과 같습니다.

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
