---
title: '3부: 스캐폴드된 Razor Pages'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 3부입니다.
ms.author: riande
ms.date: 09/25/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a6efbb22f8b6280bd636cd1575d8a4a2bca0bb06
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486176"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>3부. ASP.NET Core의 스캐폴드된 Razor Pages

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 [이전 자습서](xref:tutorials/razor-pages/model)에서 스캐폴딩을 통해 만든 Razor 페이지를 살펴봅니다.

::: moniker range=">= aspnetcore-5.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a>만들기, 삭제, 세부 정보 및 편집 페이지

*Pages/Movies/Index.cshtml.cs* 페이지 모델을 검사합니다.

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Pages는 `PageModel`에서 파생됩니다. 일반적으로 `PageModel` 파생 클래스의 이름은 `<PageName>Model`로 지정됩니다. 생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 `RazorPagesMovieContext`를 페이지에 추가합니다.

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

Entity Framework로 비동기 프로그래밍에 대한 자세한 내용은 [비동기 코드](xref:data/ef-rp/intro#asynchronous-code)를 참조하세요.

페이지에 대한 요청을 만들면 `OnGetAsync` 메서드가 Razor Page에 동영상 목록을 반환합니다. Razor 페이지에서 `OnGetAsync` 또는 `OnGet`을 호출하여 페이지 상태를 초기화합니다. 이 경우 `OnGetAsync`는 동영상 목록을 가져와 표시합니다.

`OnGet`이 `void`를 반환하거나 `OnGetAsync`가 `Task`를 반환하면 return 문이 사용되지 않은 것입니다. 개인 정보 페이지를 예로 들 수 있습니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

반환 형식이 `IActionResult` 또는 `Task<IActionResult>`이면 반환 문을 제공해야 합니다. *Pages/Movies/Create.cshtml.cs* `OnPostAsync` 메서드를 예로 들 수 있습니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> *Pages/Movies/Index.cshtml* Razor 페이지를 검사합니다.

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor는 HTML에서 C# 또는 Razor 관련 태그로 전환될 수 있습니다. `@` 기호 뒤에 [Razor 예약 키워드](xref:mvc/views/razor#razor-reserved-keywords)가 사용되면 이 기호는 Razor 관련 태그로 전환됩니다. 이외의 경우에는 C#으로 전환됩니다.

### <a name="the-page-directive"></a>@page 지시문

`@page` Razor 지시문은 파일을 MVC 작업으로 만들고, 이것은 요청을 처리할 수 있음을 의미합니다. `@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다. `@page` 및 `@model`은 Razor 관련 태그로 전환되는 예입니다. 자세한 내용은 [Razor 구문](xref:mvc/views/razor#razor-syntax)을 참조하세요.

<a name="md"></a>

### <a name="the-model-directive"></a>@model 지시문

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model` 지시문은 Razor Page에 전달되는 모델 형식을 지정합니다. 위의 예제에서 `@model` 줄은 Razor Page에서 `PageModel` 파생 클래스를 사용할 수 있게 만듭니다. 모델은 페이지에서 `@Html.DisplayNameFor` 및 `@Html.DisplayFor` [HTML 도우미](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)에서 사용됩니다.


다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다. 람다 식은 계산되는 것이 아니라 검사됩니다. 즉, `model`, `model.Movie` 또는 `model.Movie[0]`가 `null`이거나 비어 있을 경우 액세스 위반이 없습니다. 람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)` 사용) 모델의 속성 값이 계산됩니다.

### <a name="the-layout-page"></a>레이아웃 페이지

메뉴 링크( **RazorPagesMovie**, **홈** 및 **개인 정보**)를 선택합니다. 각 페이지는 동일한 메뉴 레이아웃을 보여줍니다. 메뉴 레이아웃은 *Pages/Shared/_Layout.cshtml* 파일에서 구현됩니다.

*Pages/Shared/_Layout.cshtml* 파일을 열고 검사합니다.

[레이아웃](xref:mvc/views/layout) 템플릿을 사용하여 HTML 컨테이너 레이아웃을 다음과 같이 지정할 수 있습니다.

* 한 위치에 지정됩니다.
* 사이트의 여러 페이지에 적용됩니다.

`@RenderBody()` 줄을 찾습니다. `RenderBody`는 사용자가 만드는 모든 페이지 특정 보기가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑* 됩니다. 예를 들어, **개인 정보** 링크를 선택하는 경우 *Pages/Privacy.cshtml* 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData 및 레이아웃

*Pages/Movies/Index.cshtml* 파일의 다음 태그를 고려해 보세요.

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

강조 표시된 이전 태그는 C#으로 전환되는 Razor의 예제입니다. `{` 및 `}` 문자로 C# 코드 블록을 묶습니다.

`PageModel` 기본 클래스에는 데이터를 뷰에 전달하는 데 사용할 수 있는 `ViewData` 사전 속성이 있습니다. **키 값** 패턴을 사용하여 개체가 `ViewData` 사전에 추가됩니다. 이전 샘플에서는 `Title` 속성이 `ViewData` 사전에 추가됩니다.

`Title` 속성은 Pages/Shared/_Layout.cshtml 파일에서 사용됩니다. 다음 태그는 *_Layout.cshtml* 파일의 처음 몇 줄을 표시합니다.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

`@*Markup removed for brevity.*@` 줄은 Razor 주석입니다. HTML 주석 `<!-- -->`과 달리 Razor 주석은 클라이언트에 전송되지 않습니다. 자세한 내용은 [MDN 웹 문서: HTML 시작](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)을 참조하세요.

### <a name="update-the-layout"></a>레이아웃 업데이트

1. *Pages/Shared/_Layout.cshtml* 파일의 `<title>` 요소를 변경하여 **RazorPagesMovie** 대신 **Movie** 를 표시합니다.

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. *Pages/Shared/_Layout.cshtml* 파일에서 다음 앵커 요소를 찾습니다.

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. 이전 요소를 다음 태그로 바꿉니다.

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   이전 앵커 요소는 [태그 도우미](xref:mvc/views/tag-helpers/intro)입니다. 이 경우에는 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)입니다. `asp-page="/Movies/Index"` 태그 도우미 특성 및 값으로 `/Movies/Index` Razor Page의 링크를 만듭니다. `asp-area` 특성 값이 비어 있으므로 영역은 링크에서 사용되지 않습니다. 자세한 내용은 [영역](xref:mvc/controllers/areas)을 참조하세요.

1. 변경 내용을 저장하고 **RpMovie** 링크를 선택하여 앱을 테스트합니다. 문제가 있는 경우 GitHub에서 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 파일을 참조하세요.

1. **홈**, **RpMovie**, **만들기**, **편집** 및 **삭제** 링크를 테스트합니다. 각 페이지에서 설정되는 제목은 브라우저 탭에서 확인할 수 있습니다. 페이지의 책갈피를 지정하면 제목이 책갈피에 사용됩니다.

> [!NOTE]
> `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비영어 로캘 및 비미국 영어 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화하는 단계를 수행해야 합니다. 소수점 추가에 대한 지침은 이 [GitHub 문제 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)을 참조하세요.

`Layout` 속성은 *Pages/_ViewStart.cshtml* 파일에서 설정됩니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

이전 태그는 *Pages* 폴더 아래에 있는 모든 Razor 파일에 대한 레이아웃 파일을 *Pages/Shared/_Layout.cshtml* 로 설정합니다. 자세한 내용은 [레이아웃](xref:razor-pages/index#layout)을 참조하세요.

### <a name="the-create-page-model"></a>Create 페이지 모델

*Pages/Movies/Create.cshtml.cs* 페이지 모델을 살펴봅니다.

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet` 메서드는 페이지에 필요한 상태를 초기화합니다. 만들기 페이지에는 초기화할 상태가 없습니다. 따라서 `Page`가 반환됩니다. 자습서의 뒷부분에서 상태를 초기화하는 `OnGet`의 예가 나와 있습니다. `Page` 메서드는 *Create.cshtml* 페이지를 렌더링하는 `PageResult` 개체를 만듭니다.

`Movie` 속성은 [BindProperty](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 특성을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 옵트인합니다. 만들기 폼이 폼 값을 게시하면 ASP.NET Core 런타임이 게시된 값을 `Movie` 모델에 바인딩합니다.

페이지에 폼 데이터가 게시되면 `OnPostAsync` 메서드가 실행됩니다.

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

모델 오류가 있는 경우 폼과 게시된 모든 폼 데이터가 다시 표시됩니다. 대부분의 모델 오류는 폼이 게시되기 전에 클라이언트 쪽에서 catch할 수 있습니다. 예를 들어 데이터로 변환될 수 없는 날짜 필드에 대한 값을 게시하는 모델 오류가 발생할 수 있습니다. 클라이언트 쪽 유효성 검사 및 모델 유효성 검사는 자습서의 뒷부분에서 설명합니다.

모델 오류가 없는 경우:

* 데이터가 저장됩니다.
* 브라우저가 Index 페이지로 리디렉션됩니다.

### <a name="the-create-no-locrazor-page"></a>Create Razor Page

*Pages/Movies/Create.cshtml* Razor Page 파일을 살펴봅니다.

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서는 다음 태그를 태그 도우미에 사용되는 독특한 굵은 글꼴로 표시합니다.

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml 페이지의 VS17 뷰](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

다음 태그 도우미는 위의 태그에 표시됩니다.

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

`<form method="post">` 요소는 [폼 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)입니다. 폼 태그 도우미에는 [위조 방지 토큰](xref:security/anti-request-forgery)이 자동으로 포함됩니다.

스캐폴딩 엔진은 ID를 제외하고 다음과 비슷한 모델에서 필드마다 Razor 태그를 만듭니다.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` 및 `<span asp-validation-for`) 는 유효성 검사 오류를 표시합니다. 유효성 검사는 이 시리즈의 뒷부분에서 자세히 설명합니다.

[레이블 태그 도우미](xref:mvc/views/working-with-forms#the-label-tag-helper)(`<label asp-for="Movie.Title" class="control-label"></label>`)는 `Title` 속성에 대한 레이블 캡션 및 `[for]` 특성을 생성합니다.

[입력 태그 도우미](xref:mvc/views/working-with-forms)(`<input asp-for="Movie.Title" class="form-control">`)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.

태그 도우미(예: `<form method="post">`)에 대한 자세한 내용은 [ASP.NET Core의 태그 도우미](xref:mvc/views/tag-helpers/intro)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 모델 추가](xref:tutorials/razor-pages/model)
> [다음: 데이터베이스 작업](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a>만들기, 삭제, 세부 정보 및 편집 페이지

*Pages/Movies/Index.cshtml.cs* 페이지 모델을 검사합니다.

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Pages는 `PageModel`에서 파생됩니다. 일반적으로 `PageModel` 파생 클래스의 이름은 `<PageName>Model`로 지정됩니다. 생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 `RazorPagesMovieContext`를 페이지에 추가합니다. 스캐폴드된 페이지는 이 패턴을 따릅니다. Entity Framework로 비동기 프로그래밍에 대한 자세한 내용은 [비동기 코드](xref:data/ef-rp/intro#asynchronous-code)를 참조하세요.

페이지에 대한 요청을 만들면 `OnGetAsync` 메서드가 Razor Page에 동영상 목록을 반환합니다. `OnGetAsync` 또는 `OnGet`은 페이지 상태를 초기화하기 위해 Razor Page에서 호출됩니다. 이 경우 `OnGetAsync`는 동영상 목록을 가져와 표시합니다.

`OnGet`에서 `void`를 반환하거나 `OnGetAsync`에서 `Task`를 반환하면 반환 메서드가 사용되지 않은 것입니다. 반환 형식이 `IActionResult` 또는 `Task<IActionResult>`이면 반환 문을 제공해야 합니다. *Pages/Movies/Create.cshtml.cs* `OnPostAsync` 메서드를 예로 들 수 있습니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> *Pages/Movies/Index.cshtml* Razor 페이지를 검사합니다.

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor는 HTML에서 C# 또는 Razor 관련 태그로 전환될 수 있습니다. `@` 기호 뒤에 [Razor 예약 키워드](xref:mvc/views/razor#razor-reserved-keywords)가 사용되면 이 기호는 Razor 관련 태그로 전환됩니다. 이외의 경우에는 C#으로 전환됩니다.

`@page` Razor 지시문은 파일을 MVC 작업으로 만들고, 이것은 요청을 처리할 수 있음을 의미합니다. `@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다. `@page`는 Razor 관련 태그로 전환되는 하나의 예입니다. 자세한 내용은 [Razor 구문](xref:mvc/views/razor#razor-syntax)을 참조하세요.

<a name="md"></a>

### <a name="the-model-directive"></a>@model 지시문

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model` 지시문은 Razor Page에 전달되는 모델 형식을 지정합니다. 위의 예제에서 `@model` 줄은 Razor Page에서 `PageModel` 파생 클래스를 사용할 수 있게 만듭니다. 모델은 페이지에서 `@Html.DisplayNameFor` 및 `@Html.DisplayFor` [HTML 도우미](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)에서 사용됩니다.

### <a name="html-helpers"></a>HTML 도우미

다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다. 람다 식은 계산되는 것이 아니라 검사됩니다. 즉, `model`, `model.Movie` 또는 `model.Movie[0]`가 `null`이거나 비어 있을 경우 액세스 위반이 없습니다. 람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)` 사용) 모델의 속성 값이 계산됩니다.
### <a name="the-layout-page"></a>레이아웃 페이지

메뉴 링크( **RazorPagesMovie**, **홈** 및 **개인 정보**)를 선택합니다. 각 페이지는 동일한 메뉴 레이아웃을 보여줍니다. 메뉴 레이아웃은 *Pages/Shared/_Layout.cshtml* 파일에서 구현됩니다.

*Pages/Shared/_Layout.cshtml* 파일을 열고 검사합니다.

[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트의 여러 페이지에 적용할 수 있습니다. `@RenderBody()` 줄을 찾습니다. `RenderBody`는 사용자가 만드는 모든 페이지 특정 보기가 표시되는 자리 표시자이며 레이아웃 페이지에서 ‘래핑됩니다’. 예를 들어 **개인 정보** 링크를 선택하는 경우 **Pages/Privacy.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData 및 레이아웃

*Pages/Movies/Index.cshtml* 파일의 다음 코드를 고려해 보세요.

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

이전 강조 표시된 코드는 C#으로 전환되는 Razor의 예제입니다. `{` 및 `}` 문자로 C# 코드 블록을 묶습니다.

`PageModel` 기본 클래스에는 뷰에 전달할 데이터를 추가하는 데 사용될 수 있는 `ViewData` 사전 속성이 있습니다. 키/쌍 패턴을 사용하여 개체를 `ViewData` 사전에 추가합니다. 이전 샘플에서는 `Title` 속성이 `ViewData` 사전에 추가됩니다.

`Title` 속성은 *Pages/Shared/_Layout.cshtml* 파일에서 사용됩니다. 다음 태그는 *_Layout.cshtml* 파일의 처음 몇 줄을 표시합니다.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

`@*Markup removed for brevity.*@` 줄은 Razor 주석입니다. HTML 주석 `<!-- -->`과 달리 Razor 주석은 클라이언트에 전송되지 않습니다. 자세한 내용은 [MDN 웹 문서: HTML 시작](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)을 참조하세요.

### <a name="update-the-layout"></a>레이아웃 업데이트

*Pages/Shared/_Layout.cshtml* 파일의 `<title>` 요소를 변경하여 **RazorPagesMovie** 대신 **Movie** 를 표시합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

*Pages/Shared/_Layout.cshtml* 파일에서 다음 앵커 요소를 찾습니다.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

이전 요소를 다음 태그로 바꿉니다.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

이전 앵커 요소는 [태그 도우미](xref:mvc/views/tag-helpers/intro)입니다. 이 경우에는 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)입니다. `asp-page="/Movies/Index"` 태그 도우미 특성 및 값으로 `/Movies/Index` Razor Page의 링크를 만듭니다. `asp-area` 특성 값이 비어 있으므로 영역은 링크에서 사용되지 않습니다. 자세한 내용은 [영역](xref:mvc/controllers/areas)을 참조하세요.

변경 내용을 저장하고 **RpMovie** 링크를 클릭하여 앱을 테스트합니다. 문제가 있는 경우 GitHub에서 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 파일을 참조하세요.

다른 링크(**홈**, **RpMovie**, **만들기**, **편집** 및 **삭제**)를 테스트합니다. 각 페이지에서 설정되는 제목은 브라우저 탭에서 확인할 수 있습니다. 페이지의 책갈피를 지정하면 제목이 책갈피에 사용됩니다.

> [!NOTE]
> `Price` 필드에 소수점을 입력하지 못할 수도 있습니다. 소수점으로 쉼표(",")를 사용하는 비영어 로캘 및 비미국 영어 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화하는 단계를 수행해야 합니다. 소수점 추가에 대한 지침은 이 [GitHub 문제 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)에 나와 있습니다.

`Layout` 속성은 *Pages/_ViewStart.cshtml* 파일에서 설정됩니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

이전 태그는 *Pages* 폴더 아래에 있는 모든 Razor 파일에 대한 레이아웃 파일을 *Pages/Shared/_Layout.cshtml* 로 설정합니다. 자세한 내용은 [레이아웃](xref:razor-pages/index#layout)을 참조하세요.

### <a name="the-create-page-model"></a>Create 페이지 모델

*Pages/Movies/Create.cshtml.cs* 페이지 모델을 살펴봅니다.

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet` 메서드는 페이지에 필요한 상태를 초기화합니다. 만들기 페이지에는 초기화할 상태가 없습니다. 따라서 `Page`가 반환됩니다. 이 자습서의 뒷부분에서 `OnGet` 메서드 초기화 상태를 확인합니다. `Page` 메서드는 *Create.cshtml* 페이지를 렌더링하는 `PageResult` 개체를 만듭니다.

`Movie` 속성은 [BindProperty]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> 특성을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 옵트인합니다. 만들기 폼이 폼 값을 게시하면 ASP.NET Core 런타임이 게시된 값을 `Movie` 모델에 바인딩합니다.

페이지에 폼 데이터가 게시되면 `OnPostAsync` 메서드가 실행됩니다.

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

모델 오류가 있는 경우 폼과 게시된 모든 폼 데이터가 다시 표시됩니다. 대부분의 모델 오류는 폼이 게시되기 전에 클라이언트 쪽에서 catch할 수 있습니다. 예를 들어 데이터로 변환될 수 없는 날짜 필드에 대한 값을 게시하는 모델 오류가 발생할 수 있습니다. 클라이언트 쪽 유효성 검사 및 모델 유효성 검사는 자습서의 뒷부분에서 설명합니다.

모델 오류가 없는 경우 데이터가 저장되고 브라우저가 Index 페이지로 리디렉션됩니다.

### <a name="the-create-no-locrazor-page"></a>Create Razor Page

*Pages/Movies/Create.cshtml* Razor Page 파일을 살펴봅니다.

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서는 `<form method="post">` 태그를 태그 도우미에 사용되는 독특한 굵은 글꼴로 표시합니다.

![Create.cshtml 페이지의 VS17 뷰](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

태그 도우미(예: `<form method="post">`)에 대한 자세한 내용은 [ASP.NET Core의 태그 도우미](xref:mvc/views/tag-helpers/intro)를 참조하세요.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

Mac용 Visual Studio에서는 `<form method="post">` 태그를 태그 도우미에 사용되는 독특한 굵은 글꼴로 표시합니다.

---

`<form method="post">` 요소는 [폼 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper)입니다. 폼 태그 도우미에는 [위조 방지 토큰](xref:security/anti-request-forgery)이 자동으로 포함됩니다.

스캐폴딩 엔진은 ID를 제외하고 다음과 비슷한 모델에서 필드마다 Razor 태그를 만듭니다.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` 및 `<span asp-validation-for`) 는 유효성 검사 오류를 표시합니다. 유효성 검사는 이 시리즈의 뒷부분에서 자세히 설명합니다.

[레이블 태그 도우미](xref:mvc/views/working-with-forms#the-label-tag-helper)(`<label asp-for="Movie.Title" class="control-label"></label>`)는 `Title` 속성에 대한 레이블 캡션 및 `[for]` 특성을 생성합니다.

[입력 태그 도우미](xref:mvc/views/working-with-forms)(`<input asp-for="Movie.Title" class="form-control">`)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [이전: 모델 추가](xref:tutorials/razor-pages/model)
> [다음: 데이터베이스 작업](xref:tutorials/razor-pages/sql)

::: moniker-end
