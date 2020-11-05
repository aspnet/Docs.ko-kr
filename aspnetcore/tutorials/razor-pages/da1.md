---
title: 5부. ASP.NET Core 앱에서 생성된 페이지 업데이트
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 5부입니다.
ms.author: riande
ms.date: 12/20/2018
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7d25dae67c928fa659654ce4ab34cfdad08b5300
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060068"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>5부. ASP.NET Core 앱에서 생성된 페이지 업데이트

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

스캐폴드된 동영상 앱을 사용하는 것이 좋지만 프레젠테이션은 이상적이지 않습니다. **ReleaseDate** 는 **출시일** (두 단어)이어야 합니다.

![크롬에서 열린 동영상 애플리케이션](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>생성된 코드 업데이트

*Models/Movie.cs* 파일을 열고 다음 코드에 표시된 강조 표시된 줄을 추가합니다.

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]` 데이터 주석을 사용하면 Entity Framework Core에서 `Price`를 데이터베이스의 통화에 올바르게 매핑할 수 있습니다. 자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.

다음 자습서에서 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)를 다룹니다. [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 특성은 필드의 이름으로 표시할 내용을 지정합니다(이 경우 "ReleaseDate" 대신 "Release Date") [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 특성은 필드에 저장된 시간 정보가 표시되지 않도록 데이터의 형식(날짜)을 지정합니다.

페이지/동영상으로 이동하고 **편집** 링크로 마우스를 가져가 대상 URL을 봅니다.

![브라우저 창에서 편집 링크에 마우스를 가져가면 http://localhost:1234/Movies/Edit/5 의 링크 Url이 표시됩니다.](~/tutorials/razor-pages/da1/edit7.png)

**편집** , **세부 정보** 및 **삭제** 링크는 *Pages/Movies/Index.cshtml* 파일에서 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)에 의해 생성됩니다.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다. 위의 코드에서 `AnchorTagHelper`는 Razor 페이지에서 HTML `href` 특성 값(경로는 상대적), `asp-page` 및 경로 ID(`asp-route-id`)를 동적으로 생성합니다. 자세한 내용은 [페이지에 대한 URL 생성](xref:razor-pages/index#url-generation-for-pages)을 참조하세요.

선호하는 브라우저에서 **소스 보기** 를 사용하여 생성된 표시를 검사합니다. 생성된 HTML의 일부는 다음과 같습니다.

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

동적으로 생성된 링크는 쿼리 문자열이 포함된 동영상 ID를 전달합니다(예: `https://localhost:5001/Movies/Details?id=1`의 `?id=1`).

### <a name="add-route-template"></a>경로 템플릿 추가

편집, 세부 정보 및 삭제 Razor Pages를 “{id:int}” 경로 템플릿을 사용하도록 업데이트합니다. 이러한 각 페이지에 대한 page 지시문을 `@page`에서 `@page "{id:int}"`로 변경합니다. 앱을 실행한 다음 소스를 봅니다. 생성된 HTML에서 URL의 경로 부분에 ID를 추가합니다.

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

정수를 포함하지 **않는** "{id:int}" 경로 템플릿이 있는 페이지에 대한 요청은 HTTP 404(찾을 수 없음) 오류를 반환합니다. 예를 들어 `http://localhost:5000/Movies/Details`는 404 오류를 반환합니다. ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.

 ```cshtml
@page "{id:int?}"
```

`@page "{id:int?}"`의 동작을 테스트하려면:

* *Pages/Movies/Details.cshtml* 에서 페이지 지시문을 `@page "{id:int?}"`로 설정합니다.
* *Pages/Movies/Details.cshtml.cs* 의 `public async Task<IActionResult> OnGetAsync(int? id)`에서 중단점을 설정합니다.
* `https://localhost:5001/Movies/Details/`로 이동합니다.

`@page "{id:int}"` 지시문을 사용하면 중단점에 도달하지 않습니다. 라우팅 엔진은 HTTP 404를 반환합니다. `OnGetAsync` 메서드는 `@page "{id:int?}"`를 사용하여 `NotFound`(HTTP 404)를 반환합니다.

### <a name="review-concurrency-exception-handling"></a>동시성 예외 처리 검토

*Pages/Movies/Edit.cshtml.cs* 파일에서 `OnPostAsync` 메서드를 검토합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

하나의 클라이언트가 동영상을 삭제하고 다른 클라이언트가 동영상에 변경 내용을 게시하는 경우 이전 코드에서는 동시성 예외를 검색합니다.

`catch` 블록을 테스트하려면:

* `catch (DbUpdateConcurrencyException)`에서 중단점 설정
* 동영상에 대한 **편집** 을 선택하고, 변경하지만 **저장** 을 입력하지 않습니다.
* 다른 브라우저 창에서 동일한 동영상에 대한 **삭제** 링크를 선택한 다음 동영상을 삭제합니다.
* 이전 브라우저 창에서 동영상에 변경 내용을 게시합니다.

프로덕션 코드는 동시성 충돌을 검색할 수 있습니다. 자세한 내용은 [동시성 충돌 처리](xref:data/ef-rp/concurrency)를 참조하세요.

### <a name="posting-and-binding-review"></a>검토 게시 및 바인딩

*Pages/Movies/Edit.cshtml.cs* 파일을 검사합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

동영상/편집 페이지에 대해 HTTP GET 요청이 만들어지는 경우(예: `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` 메서드는 데이터베이스에서 동영상을 가져오고 `Page` 메서드를 반환합니다.
* `Page` 메서드는 *Pages/Movies/Edit.cshtml* Razor 페이지를 렌더링합니다. *Pages/Movies/Edit.cshtml* 파일은 동영상 모델을 페이지에서 사용할 수 있도록 하는 모델 지시문(`@model RazorPagesMovie.Pages.Movies.EditModel`)을 포함합니다.
* 편집 양식은 동영상에서 값으로 표시됩니다.

동영상/편집 페이지가 게시될 때:

* 페이지에서 양식 값은 `Movie` 속성으로 바인딩됩니다. `[BindProperty]` 특성은 [모델 바인딩](xref:mvc/models/model-binding)을 활성화합니다.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* 모델 상태에 오류가 있는 경우(예: `ReleaseDate`를 날짜로 변환할 수 없는 경우) 양식은 제출된 값으로 다시 표시됩니다.
* 모델 오류가 없는 경우 동영상이 저장됩니다.

인덱스, 만들기 및 삭제 Razor Pages의 HTTP GET 메서드는 유사한 패턴을 따릅니다. 만들기 Razor 페이지에서 HTTP POST `OnPostAsync` 메서드는 편집 Razor 페이지의 `OnPostAsync` 메서드와 유사한 패턴을 따릅니다.

## <a name="additional-resources"></a>추가 자료

> [!div class="step-by-step"]
> [이전: 데이터베이스 작업](xref:tutorials/razor-pages/sql)
> [다음: 검색 추가](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

스캐폴드된 동영상 앱을 사용하는 것이 좋지만 프레젠테이션은 이상적이지 않습니다. **ReleaseDate** 는 **출시일** (두 단어)이어야 합니다.

![크롬에서 열린 동영상 애플리케이션](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>생성된 코드 업데이트

*Models/Movie.cs* 파일을 열고 다음 코드에 표시된 강조 표시된 줄을 추가합니다.

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]` 데이터 주석을 사용하면 Entity Framework Core에서 `Price`를 데이터베이스의 통화에 올바르게 매핑할 수 있습니다. 자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.

다음 자습서에서 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)를 다룹니다. [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 특성은 필드의 이름으로 표시할 내용을 지정합니다(이 경우 "ReleaseDate" 대신 "Release Date") [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 특성은 필드에 저장된 시간 정보가 표시되지 않도록 데이터의 형식(날짜)을 지정합니다.

페이지/동영상으로 이동하고 **편집** 링크로 마우스를 가져가 대상 URL을 봅니다.

![브라우저 창에서 편집 링크에 마우스를 가져가면 http://localhost:1234/Movies/Edit/5 의 링크 Url이 표시됩니다.](~/tutorials/razor-pages/da1/edit7.png)

**편집** , **세부 정보** 및 **삭제** 링크는 *Pages/Movies/Index.cshtml* 파일에서 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)에 의해 생성됩니다.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다. 위의 코드에서 `AnchorTagHelper`는 Razor 페이지에서 HTML `href` 특성 값(경로는 상대적), `asp-page` 및 경로 ID(`asp-route-id`)를 동적으로 생성합니다. 자세한 내용은 [페이지에 대한 URL 생성](xref:razor-pages/index#url-generation-for-pages)을 참조하세요.

선호하는 브라우저에서 **소스 보기** 를 사용하여 생성된 표시를 검사합니다. 생성된 HTML의 일부는 다음과 같습니다.

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

동적으로 생성된 링크는 쿼리 문자열이 포함된 동영상 ID를 전달합니다(예: `https://localhost:5001/Movies/Details?id=1`의 `?id=1`).

편집, 세부 정보 및 삭제 Razor Pages를 “{id:int}” 경로 템플릿을 사용하도록 업데이트합니다. 이러한 각 페이지에 대한 page 지시문을 `@page`에서 `@page "{id:int}"`로 변경합니다. 앱을 실행한 다음 소스를 봅니다. 생성된 HTML에서 URL의 경로 부분에 ID를 추가합니다.

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

정수를 포함하지 **않는** "{id:int}" 경로 템플릿이 있는 페이지에 대한 요청은 HTTP 404(찾을 수 없음) 오류를 반환합니다. 예를 들어 `http://localhost:5000/Movies/Details`는 404 오류를 반환합니다. ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.

 ```cshtml
@page "{id:int?}"
```

`@page "{id:int?}"`의 동작을 테스트하려면:

* *Pages/Movies/Details.cshtml* 에서 페이지 지시문을 `@page "{id:int?}"`로 설정합니다.
* *Pages/Movies/Details.cshtml.cs* 의 `public async Task<IActionResult> OnGetAsync(int? id)`에서 중단점을 설정합니다.
* `https://localhost:5001/Movies/Details/`로 이동합니다.

`@page "{id:int}"` 지시문을 사용하면 중단점에 도달하지 않습니다. 라우팅 엔진은 HTTP 404를 반환합니다. `OnGetAsync` 메서드는 `@page "{id:int?}"`를 사용하여 `NotFound`(HTTP 404)를 반환합니다.

### <a name="review-concurrency-exception-handling"></a>동시성 예외 처리 검토

*Pages/Movies/Edit.cshtml.cs* 파일에서 `OnPostAsync` 메서드를 검토합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

하나의 클라이언트가 동영상을 삭제하고 다른 클라이언트가 동영상에 변경 내용을 게시하는 경우 이전 코드에서는 동시성 예외를 검색합니다.

`catch` 블록을 테스트하려면:

* `catch (DbUpdateConcurrencyException)`에서 중단점 설정
* 동영상에 대한 **편집** 을 선택하고, 변경하지만 **저장** 을 입력하지 않습니다.
* 다른 브라우저 창에서 동일한 동영상에 대한 **삭제** 링크를 선택한 다음 동영상을 삭제합니다.
* 이전 브라우저 창에서 동영상에 변경 내용을 게시합니다.

프로덕션 코드는 동시성 충돌을 검색할 수 있습니다. 자세한 내용은 [동시성 충돌 처리](xref:data/ef-rp/concurrency)를 참조하세요.

### <a name="posting-and-binding-review"></a>검토 게시 및 바인딩

*Pages/Movies/Edit.cshtml.cs* 파일을 검사합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

동영상/편집 페이지에 대해 HTTP GET 요청이 만들어지는 경우(예: `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` 메서드는 데이터베이스에서 동영상을 가져오고 `Page` 메서드를 반환합니다. 
* `Page` 메서드는 *Pages/Movies/Edit.cshtml* Razor 페이지를 렌더링합니다. *Pages/Movies/Edit.cshtml* 파일은 동영상 모델을 페이지에서 사용할 수 있도록 하는 모델 지시문(`@model RazorPagesMovie.Pages.Movies.EditModel`)을 포함합니다.
* 편집 양식은 동영상에서 값으로 표시됩니다.

동영상/편집 페이지가 게시될 때:

* 페이지에서 양식 값은 `Movie` 속성으로 바인딩됩니다. `[BindProperty]` 특성은 [모델 바인딩](xref:mvc/models/model-binding)을 활성화합니다.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* 모델 상태에 오류가 있는 경우(예: `ReleaseDate`를 날짜로 변환할 수 없는 경우) 양식은 제출된 값으로 표시됩니다.
* 모델 오류가 없는 경우 동영상이 저장됩니다.

인덱스, 만들기 및 삭제 Razor Pages의 HTTP GET 메서드는 유사한 패턴을 따릅니다. 만들기 Razor 페이지에서 HTTP POST `OnPostAsync` 메서드는 편집 Razor 페이지의 `OnPostAsync` 메서드와 유사한 패턴을 따릅니다.

검색은 다음 자습서에 추가됩니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [이전: 데이터베이스 작업](xref:tutorials/razor-pages/sql)
> [다음: 검색 추가](xref:tutorials/razor-pages/search)

::: moniker-end
