---
title: 6부. ASP.NET Core Razor Pages에 검색 추가
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 6부입니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 960f60198f5e65ed05d0374fd0704537376d27d6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058092"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>6부. ASP.NET Core Razor Pages에 검색 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

다음 섹션에서는 *장르* 또는 *이름* 으로 영화 검색이 추가됩니다.

*Pages/Movies/Index.cshtml.cs* 에 강조 표시된 다음 속성을 추가합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: 사용자가 검색 텍스트 상자에 입력하는 텍스트를 포함합니다. `SearchString`에는 [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 특성이 있습니다. `[BindProperty]`는 양식 값 및 쿼리 문자열을 속성과 동일한 이름으로 바인딩합니다. `(SupportsGet = true)`는 GET 요청을 바인딩하는 데 필요합니다.
* `Genres`: 장르 목록을 포함합니다. `Genres`를 통해 사용자는 목록에서 장르를 선택할 수 있습니다. `SelectList`는 `using Microsoft.AspNetCore.Mvc.Rendering;`이 필요합니다.
* `MovieGenre`: 사용자가 선택하는 특정 장르를 포함합니다(예: "서부 영화").
* `Genres` 및 `MovieGenre`는 이 자습서의 뒷부분에서 사용됩니다.

[!INCLUDE[](~/includes/bind-get.md)]

인덱스 페이지의 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 메서드의 첫 번째 줄은 동영상을 선택하는 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리를 만듭니다.

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

이 시점에 쿼리는 정의 *만* 되었으며 데이터베이스에 대해서 실행되지는 **않았습니다**.

`SearchString` 속성이 Null 또는 비어 있는 경우 영화 쿼리는 검색 문자열에 대해 필터링하도록 수정됩니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 코드는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다. 람다 식은 메서드 기반 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리에서 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다. LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다. 대신 쿼리 실행이 지연됩니다. 즉, 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다. 자세한 내용은 [쿼리 실행](/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 C# 코드에서가 아닌 데이터베이스에서 실행됩니다. 쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다. SQL Server에서 `Contains`는 대/소문자를 구분하는 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다. SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.

Movies 페이지로 이동하고 `?searchString=Ghost`와 같은 쿼리 문자열을 URL에 추가합니다(예: `https://localhost:5001/Movies?searchString=Ghost`). 필터링된 동영상이 표시됩니다.

![인덱스 보기](search/_static/ghost.png)

다음 경로 템플릿이 인덱스 페이지에 추가되는 경우 검색 문자열이 URL 세그먼트로 전달될 수 있습니다(예: `https://localhost:5001/Movies/Ghost`).

```cshtml
@page "{searchString?}"
```

이전 경로 제약 조건을 통해 쿼리 문자열 값 대신 경로 데이터(URL 세그먼트)로 제목을 검색할 수 있습니다.  `"{searchString?}"`에서 `?`는 선택적 경로 매개 변수임을 의미합니다.

![URL에 ghost라는 단어가 추가되고 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 Index 보기](search/_static/g2.png)

ASP.NET Core 런타임은 [모델 바인딩](xref:mvc/models/model-binding)을 사용하여 쿼리 문자열(`?searchString=Ghost`) 또는 경로 데이터(`https://localhost:5001/Movies/Ghost`)에서 `SearchString` 속성의 값을 설정합니다. 모델 바인딩은 대/소문자를 구분하지 않습니다.

그러나 사용자가 동영상 검색을 위해 URL을 수정하는 것을 기대할 수 없습니다. 이 단계에서 동영상을 필터링하도록 UI가 추가됩니다. 경로 제약 조건 `"{searchString?}"`을 추가한 경우 이를 제거합니다.

*Pages/Movies/Index.cshtml* 파일을 열고 다음 코드에서 강조 표시된 `<form>` 표시를 추가합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 태그는 다음과 같은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용합니다.

* [형식 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper). 양식이 제출되면 쿼리 문자열을 통해 필터 문자열이 *Pages/Movies/Index* 페이지로 전송됩니다.
* [입력 태그 도우미](xref:mvc/views/working-with-forms#the-input-tag-helper)

변경 내용을 저장하고 필터를 테스트합니다.

![제목 필터 텍스트 상자에 단어 ghost를 입력하여 인덱스 보기](search/_static/filter.png)

## <a name="search-by-genre"></a>장르별 검색

`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

다음 코드는 데이터베이스에서 모든 장르를 검색하는 LINQ 쿼리입니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

장르의 `SelectList`는 고유 장르를 프로젝션함으로써 생성됩니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Razor 페이지에 장르별 검색 추가

다음과 같이 *Index.cshtml* 을 업데이트합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

장르별, 동영상 제목별 및 둘 다로 검색하여 앱을 테스트합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [이전: 페이지 업데이트](xref:tutorials/razor-pages/da1)
> [다음: 새 필드 추가](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

다음 섹션에서는 *장르* 또는 *이름* 으로 영화 검색이 추가됩니다.

*Pages/Movies/Index.cshtml.cs* 에 강조 표시된 다음 속성을 추가합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: 사용자가 검색 텍스트 상자에 입력하는 텍스트를 포함합니다. `SearchString`에는 [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 특성이 있습니다. `[BindProperty]`는 양식 값 및 쿼리 문자열을 속성과 동일한 이름으로 바인딩합니다. `(SupportsGet = true)`는 GET 요청을 바인딩하는 데 필요합니다.
* `Genres`: 장르 목록을 포함합니다. `Genres`를 통해 사용자는 목록에서 장르를 선택할 수 있습니다. `SelectList`는 `using Microsoft.AspNetCore.Mvc.Rendering;`이 필요합니다.
* `MovieGenre`: 사용자가 선택하는 특정 장르를 포함합니다(예: "서부 영화").
* `Genres` 및 `MovieGenre`는 이 자습서의 뒷부분에서 사용됩니다.

[!INCLUDE[](~/includes/bind-get.md)]

인덱스 페이지의 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 메서드의 첫 번째 줄은 동영상을 선택하는 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리를 만듭니다.

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

이 시점에 쿼리는 정의 *만* 되었으며 데이터베이스에 대해서 실행되지는 **않았습니다**.

`SearchString` 속성이 Null 또는 비어 있는 경우 영화 쿼리는 검색 문자열에 대해 필터링하도록 수정됩니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 코드는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다. 람다 식은 메서드 기반 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리에서 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다. LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다. 대신 쿼리 실행이 지연됩니다. 즉, 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다. 자세한 내용은 [쿼리 실행](/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.

**참고:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 C# 코드에서가 아닌 데이터베이스에서 실행됩니다. 쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다. SQL Server에서 `Contains`는 대/소문자를 구분하는 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다. SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.

Movies 페이지로 이동하고 `?searchString=Ghost`와 같은 쿼리 문자열을 URL에 추가합니다(예: `https://localhost:5001/Movies?searchString=Ghost`). 필터링된 동영상이 표시됩니다.

![인덱스 보기](search/_static/ghost.png)

다음 경로 템플릿이 인덱스 페이지에 추가되는 경우 검색 문자열이 URL 세그먼트로 전달될 수 있습니다(예: `https://localhost:5001/Movies/Ghost`).

```cshtml
@page "{searchString?}"
```

이전 경로 제약 조건을 통해 쿼리 문자열 값 대신 경로 데이터(URL 세그먼트)로 제목을 검색할 수 있습니다.  `"{searchString?}"`에서 `?`는 선택적 경로 매개 변수임을 의미합니다.

![URL에 ghost라는 단어가 추가되고 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 Index 보기](search/_static/g2.png)

ASP.NET Core 런타임은 [모델 바인딩](xref:mvc/models/model-binding)을 사용하여 쿼리 문자열(`?searchString=Ghost`) 또는 경로 데이터(`https://localhost:5001/Movies/Ghost`)에서 `SearchString` 속성의 값을 설정합니다. 모델 바인딩은 대/소문자를 구분하지 않습니다.

그러나 사용자가 동영상 검색을 위해 URL을 수정하는 것을 기대할 수 없습니다. 이 단계에서 동영상을 필터링하도록 UI가 추가됩니다. 경로 제약 조건 `"{searchString?}"`을 추가한 경우 이를 제거합니다.

*Pages/Movies/Index.cshtml* 파일을 열고 다음 코드에서 강조 표시된 `<form>` 표시를 추가합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 태그는 다음과 같은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용합니다.

* [형식 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper). 양식이 제출되면 쿼리 문자열을 통해 필터 문자열이 *Pages/Movies/Index* 페이지로 전송됩니다.
* [입력 태그 도우미](xref:mvc/views/working-with-forms#the-input-tag-helper)

변경 내용을 저장하고 필터를 테스트합니다.

![제목 필터 텍스트 상자에 단어 ghost를 입력하여 인덱스 보기](search/_static/filter.png)

## <a name="search-by-genre"></a>장르별 검색

`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

다음 코드는 데이터베이스에서 모든 장르를 검색하는 LINQ 쿼리입니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

장르의 `SelectList`는 고유 장르를 프로젝션함으로써 생성됩니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Razor 페이지에 장르별 검색 추가

다음과 같이 *Index.cshtml* 을 업데이트합니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

장르별, 동영상 제목별 및 둘 다로 검색하여 앱을 테스트합니다.
위의 코드는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper) 및 Option 태그 도우미를 사용합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [이전: 페이지 업데이트](xref:tutorials/razor-pages/da1)
> [다음: 새 필드 추가](xref:tutorials/razor-pages/new-field)

::: moniker-end
