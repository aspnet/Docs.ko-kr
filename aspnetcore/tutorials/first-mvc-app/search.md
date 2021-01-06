---
title: 7부. ASP.NET Core MVC 앱에 검색 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 7부입니다.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 657072803f59feb99de8b31ddb3a6433d832aa30
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059626"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>7부. ASP.NET Core MVC 앱에 검색 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 `Index` 작업 메서드에 *장르* 또는 *이름* 으로 영화를 검색할 수 있는 검색 기능을 추가합니다.

*Controllers/MoviesController.cs* 에 있는 `Index` 메서드를 다음 코드로 수정합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

`Index` 작업 메서드의 첫 번째 줄은 영화를 선택하는 [LINQ](/dotnet/standard/using-linq) 쿼리를 만듭니다.

```csharp
var movies = from m in _context.Movie
             select m;
```

이 시점에 쿼리는 정의 *만* 되었으며 데이터베이스에 대해서 실행되지는 **않았습니다**.

`searchString` 매개 변수에 문자열이 담겨 있으면 해당 검색 문자열의 값으로 필터링하도록 영화 쿼리가 수정됩니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

위의 `s => s.Title.Contains()` 코드는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다. 람다는 메서드 기반 [LINQ](/dotnet/standard/using-linq) 쿼리에서 [Where](/dotnet/api/system.linq.enumerable.where) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다. LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다. 대신 쿼리 실행이 지연됩니다.  즉, 실제로 결정된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연된다는 뜻입니다. 지연된 쿼리 실행에 대한 자세한 내용은 [쿼리 실행](/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.

참고: [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 위에 표시된 C# 코드에서가 아닌 데이터베이스에서 실행됩니다. 쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다. SQL Server에서 [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)는 대/소문자를 구분하는 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다. SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.

`/Movies/Index`로 이동합니다. 쿼리 문자열(예: `?searchString=Ghost`)을 URL에 추가합니다. 필터링된 영화가 표시됩니다.

![Index 보기](~/tutorials/first-mvc-app/search/_static/ghost.png)

`id`라는 매개 변수를 포함하도록 `Index` 메서드의 서명을 변경하면 해당 `id` 매개 변수는 *Startup.cs* 에서 설정된 기본 경로의 선택적 `{id}` 자리 표시자와 일치합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

매개 변수를 `id`로, `searchString`의 모든 항목을 `id`로 변경합니다.

기존 `Index` 메서드는 다음과 같습니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

`id` 매개 변수를 사용하는 수정된 `Index` 메서드는 다음과 같습니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

이제 쿼리 문자열 값 대신 경로 데이터(URL 세그먼트)로 검색 제목을 전달할 수 있습니다.

![URL에 ghost라는 단어가 추가되고 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 Index 보기](~/tutorials/first-mvc-app/search/_static/g2.png)

그러나 사용자가 영화를 검색하려고 할 때마다 URL을 수정하리라고 기대하지는 않을 것입니다. 따라서 이제 영화를 필터링하는 데 도움이 되는 UI 요소를 추가할 것입니다. 경로 바인딩 `ID` 매개 변수의 전달 방법을 테스트하기 위해 `Index` 메서드의 서명을 변경했다면, `searchString`이라는 매개 변수를 사용하도록 메서드를 다시 변경합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

*Views/Movies/Index.cshtml* 파일을 열고 아래의 강조 표시된 `<form>` 표시를 추가합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

이 HTML `<form>` 태그는 [Form 태그 도우미](xref:mvc/views/working-with-forms)를 사용하므로 양식을 제출할 경우 필터 문자열은 영화 컨트롤러의 `Index` 작업에 게시됩니다. 변경 내용을 저장하고 필터를 테스트합니다.

![Title 필터 텍스트 상자에 ghost라는 단어를 입력한 Index 보기](~/tutorials/first-mvc-app/search/_static/filter.png)

예상할 수 있는 것처럼 `Index` 메서드의 `[HttpPost]` 오버로드는 없습니다. 이 메서드는 앱의 상태를 변경하지 않고 데이터를 필터링하기만 하기 때문에 필요하지 않습니다.

다음 `[HttpPost] Index` 메서드를 추가할 수 있습니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` 매개 변수는 `Index` 메서드의 오버로드를 만들기 위해서 사용됩니다. 자습서의 뒷부분에서 이에 대해 알아봅니다.

이 메서드를 추가하면 작업 호출자는 `[HttpPost] Index` 메서드와 일치하고, 아래 이미지와 같이 `[HttpPost] Index` 메서드가 실행됩니다.

![From HttpPost Index: filter on ghost 응답을 보여주는 브라우저 창](~/tutorials/first-mvc-app/search/_static/fo.png)

그러나 이 `[HttpPost]` 버전의 `Index` 메서드를 추가하는 경우에도 이를 모두 구현하는 방법은 제한됩니다. 특정 검색을 책갈피로 설정하거나 동일하게 필터링된 영화 목록을 보기 위해 클릭할 수 있는 링크를 친구에게 보내려고 한다고 가정합니다. HTTP POST 요청의 URL은 GET 요청의 URL(localhost:{PORT}/Movies/Index)과 동일합니다. URL에는 검색 정보가 없습니다. 검색 문자열 정보는 [양식 필드 값](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)으로 서버에 전송됩니다. 브라우저 개발자 도구 또는 뛰어난 [Fiddler 도구](https://www.telerik.com/fiddler)에서 확인할 수 있습니다. 아래 이미지에서는 Chrome 브라우저 개발자 도구를 보여줍니다.

![Microsoft Edge 개발자 도구의 네트워크 탭이 ghost라는 searchString 값을 가진 요청 본문을 보여줍니다.](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

요청 본문에서 검색 매개 변수 및 [XSRF](xref:security/anti-request-forgery) 토큰을 확인할 수 있습니다. 이전 자습서에서 설명했듯이 [Form 태그 도우미](xref:mvc/views/working-with-forms)는 [XSRF](xref:security/anti-request-forgery) 위조 방지 토큰을 생성합니다. 데이터를 수정하지 않으므로 컨트롤러 메서드에서 토큰 유효성을 검사할 필요도 없습니다.

검색 매개 변수가 URL이 아닌 요청 본문에 존재하기 때문에 책갈피에 해당 검색 정보를 캡처하거나 다른 사용자와 공유할 수 없습니다. *Views/Movies/Index.cshtml* 파일에서 `HTTP GET` 요청을 지정하여 이 문제를 해결합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

이제 검색을 제출하면 URL에 검색 쿼리 문자열이 포함됩니다. `HttpPost Index` 메서드가 존재하더라도 검색은 `HttpGet Index` 작업 메서드로 이동합니다.

![URL에서 searchString=ghost 및 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화를 표시하는 브라우저 창은 단어 ghost를 포함합니다.](~/tutorials/first-mvc-app/search/_static/search_get.png)

다음 태그는 `form` 태그에 대한 변경 내용을 표시합니다.

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>장르별 검색 추가

다음 `MovieGenreViewModel` 클래스를 *Models* 폴더에 추가합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

영화 장르 보기 모델은 다음을 포함합니다.

* 영화 목록.
* 장르 목록을 담고 있는`SelectList` 이를 이용해서 사용자는 목록에서 장르를 선택할 수 있습니다.
* 선택한 장르가 담긴 `MovieGenre`.
* 사용자가 검색 텍스트 상자에 입력한 텍스트가 담긴 `SearchString`.

`MoviesController.cs`에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

다음 코드는 데이터베이스에서 모든 장르를 검색하는 `LINQ` 쿼리입니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

장르의 `SelectList`는 고유한 장르를 프로젝팅하여 생성됩니다(선택 목록에 중복된 장르가 포함되는 것을 원하지 않습니다).

사용자가 항목을 검색하면 검색 상자에 검색 값이 유지됩니다.

## <a name="add-search-by-genre-to-the-index-view"></a>Index 보기에 장르별 검색 추가

*Views/Movies/* 에 있는 `Index.cshtml`을 다음과 같이 업데이트합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

이전 코드에서 `DisplayNameFor` HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다. 람다 식이 평가되지 않고 검사되기 때문에 `model`, `model.Movies` 또는 `model.Movies[0]`가 `null`이거나 비어 있더라도 액세스 위반을 수신하지 않습니다. 람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)`) 모델의 속성 값이 평가됩니다.

장르로, 영화 제목으로, 그리고 두 가지 모두로 검색하여 앱을 테스트합니다.

![https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2 의 결과를 보여주는 브라우저 창](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [이전](controller-methods-views.md)
> [다음](new-field.md)
