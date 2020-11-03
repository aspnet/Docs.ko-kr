---
title: 6부. ASP.NET Core의 컨트롤러 메서드 및 보기
author: rick-anderson
description: 6부. ASP.NET Core MVC 앱에 모델 추가
ms.author: riande
ms.date: 12/13/2018
no-loc:
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 745703aaa4ceb39c75789bab0bde4564f3d79a30
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678556"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>6부. ASP.NET Core의 컨트롤러 메서드 및 보기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

동영상 앱을 적절하게 시작했지만 프레젠테이션은 이상적이지 않습니다. 예를 들어 **ReleaseDate** 는 두 단어여야 합니다.

![인덱스 보기: 릴리스 날짜는 한 단어(공백 없음)이며 모든 동영상 릴리스 날짜는 오전 12시를 표시합니다.](working-with-sql/_static/m55.png)

*Models/Movie.cs* 파일을 열고 아래에 강조 표시된 줄을 추가합니다.

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)에 대해서는 다음 자습서에서 다룹니다. [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 특성은 필드의 이름으로 표시할 내용을 지정합니다(이 경우 "ReleaseDate" 대신 "Release Date") [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 특성은 필드에 저장된 시간 정보가 표시되지 않도록 데이터의 형식(날짜)을 지정합니다.

Entity Framework Core가 `Price`를 데이터베이스의 통화에 올바르게 매핑하기 위해서는 `[Column(TypeName = "decimal(18, 2)")]` 데이터 주석이 필요합니다. 자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.

`Movies` 컨트롤러로 이동하고 **Edit** 링크 위에 마우스 포인터를 올려놓으면 대상 URL이 표시됩니다.

![브라우저 창에서 편집 링크에 마우스를 가져가면 https://localhost:5001/Movies/Edit/5 의 링크 Url이 표시됩니다.](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

**Edit** , **Details** 및 **Delete** 링크는 *Views/Movies/Index.cshtml* 파일의 Core MVC 앵커 태그 도우미에 의해서 생성됩니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다. 위의 코드에서는 `AnchorTagHelper`가 컨트롤러 작업 메서드 및 경로 ID로부터 HTML `href` 특성 값을 동적으로 생성합니다. 선호하는 브라우저에서 **소스 보기** 나 개발자 도구를 사용하여 생성된 태그를 확인합니다. 생성된 HTML의 일부는 다음과 같습니다.

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

*Startup.cs* 파일에 설정된 [라우팅](xref:mvc/controllers/routing) 형식을 다시 기억해보세요.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core는 `https://localhost:5001/Movies/Edit/4`를 매개 변수 `Id`가 4인 `Movies` 컨트롤러의 `Edit` 작업 메서드에 대한 요청으로 해석합니다. 컨트롤러 메서드는 작업 메서드라고도 합니다.

[태그 도우미](xref:mvc/views/tag-helpers/intro)는 ASP.NET Core의 가장 인기 있는 새로운 기능 중 하나입니다. 자세한 내용은 [추가 자료](#additional-resources)를 참조하세요.

<a name="get-post"></a>

`Movies` 컨트롤러를 열고 두 `Edit` 작업 메서드를 검사합니다. 다음 코드는 동영상을 페치하여 *Edit.cshtml* Razor 파일에서 생성된 편집 양식에 기입하는 `HTTP GET Edit` 메서드를 보여 줍니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

다음 코드는 게시된 영화 값을 처리하는 `HTTP POST Edit` 메서드를 보여줍니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

다음 코드는 게시된 영화 값을 처리하는 `HTTP POST Edit` 메서드를 보여줍니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[Bind]` 특성은 [과도한 게시](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)를 방지하기 위한 한 가지 방법입니다. 변경하려는 속성만 `[Bind]` 특성에 포함해야 합니다. 자세한 내용은 [과도한 게시로부터 컨트롤러 보호](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)를 참조하세요. [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)는 과도한 게시를 방지하기 위한 대안을 제공합니다.

두 번째 `Edit` 작업 메서드 앞에 `[HttpPost]` 특성이 지정되어 있는 것에 유의하세요.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

`HttpPost` 특성은 이 `Edit` 메서드가 `POST` 요청에 *대해서만* 호출될 수 있음을 지정합니다. 첫 번째 Edit 메서드에도 `[HttpGet]` 특성을 적용할 수 있지만 `[HttpGet]`이 기본값이므로 그럴 필요가 없습니다.

`ValidateAntiForgeryToken` 특성은 [요청 위조 방지](xref:security/anti-request-forgery)를 위해 사용되며 편집 보기 파일( *Views/Movies/Edit.cshtml* )에서 생성된 위조 방지 토큰과 쌍을 이룹니다. 편집 보기 파일은 [Form 태그 도우미](xref:mvc/views/working-with-forms)를 통해서 위조 방지 토큰을 생성합니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Form 태그 도우미](xref:mvc/views/working-with-forms)는 Movies 컨트롤러의 `Edit` 메서드에서 `[ValidateAntiForgeryToken]`가 생성한 위조 방지 토큰과 일치하는 숨겨진 위조 방지 토큰을 생성합니다. 자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

`HttpGet Edit` 메서드는 영화 `ID` 매개 변수를 받아서, Entity Framework `FindAsync` 메서드를 사용하여 영화를 검색하고, 선택된 영화를 Edit 보기에 반환합니다. 영화를 찾을 수 없을 경우 `NotFound`(HTTP 404)를 반환합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

스캐폴딩 시스템은 Edit 보기를 만들 때 `Movie` 클래스를 검토하고 클래스의 각 속성에 대해 `<label>` 및 `<input>` 요소를 렌더링하기 위한 코드를 만들었습니다. 다음 예제는 Visual Studio 스캐폴딩 시스템이 생성한 Edit 보기를 보여줍니다.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

파일의 맨 위에서 보기 템플릿에 `@model MvcMovie.Models.Movie` 문이 지정된 방식을 살펴보세요. `@model MvcMovie.Models.Movie`는 이 보기가 보기 템플릿에 대한 모델로 `Movie` 형식을 기대하고 있음을 지정합니다.

스캐폴드 코드는 몇 가지 태그 도우미 메서드를 사용하여 HTML 마크업을 간소화합니다. [레이블 태그 도우미](xref:mvc/views/working-with-forms)는 필드 이름을 표시합니다(“Title”, “ReleaseDate”, “Genre” 또는 “Price”). [입력 태그 도우미](xref:mvc/views/working-with-forms)는 HTML `<input>` 요소를 렌더링합니다. [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms)는 해당 속성과 연결된 모든 유효성 검사 메시지를 표시합니다.

응용 프로그램을 실행하고 `/Movies` URL로 이동합니다. **Edit** 링크를 클릭합니다. 브라우저에서 페이지의 소스를 봅니다. `<form>` 요소에 대해 생성된 HTML은 다음과 같습니다.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>` 요소는 `action` 특성이 `/Movies/Edit/id` URL에 게시되도록 설정된 `HTML <form>` 요소의 내부에 위치합니다. 양식 데이터는 `Save` 단추를 클릭하면 서버에 게시됩니다. `</form>` 요소를 닫기 전 마지막 줄은 [Form 태그 도우미](xref:mvc/views/working-with-forms)에서 생성된 숨겨진 [XSRF](xref:security/anti-request-forgery) 토큰을 나타냅니다.

## <a name="processing-the-post-request"></a>POST 요청 처리

다음 목록은 `Edit` 작업 메서드의 `[HttpPost]` 버전을 보여줍니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[ValidateAntiForgeryToken]` 특성은 [Form 태그 도우미](xref:mvc/views/working-with-forms)의 위조 방지 토큰 생성기에서 생성된 숨겨진 [XSRF](xref:security/anti-request-forgery) 토큰의 유효성을 검사합니다.

[모델 바인딩](xref:mvc/models/model-binding) 시스템은 게시된 양식 값을 가져와서 `movie` 매개 변수로 전달되는 `Movie` 개체를 만듭니다. `ModelState.IsValid` 속성은 양식에서 제출된 데이터를 사용하여 `Movie` 개체를 수정(편집 또는 업데이트)할 수 있는지를 확인합니다. 데이터가 유효하면 저장됩니다. 수정된(편집된) 영화 데이터는 데이터베이스 컨텍스트의 `SaveChangesAsync` 메서드를 호출하여 데이터베이스에 저장됩니다. 데이터를 저장한 후 코드는 사용자를 `MoviesController` 클래스의 `Index` 작업 메서드로 다시 전달하며, 여기에서는 방금 수행한 변경 사항을 포함한 영화 컬렉션이 표시됩니다.

양식을 서버에 게시하기 전에 클라이언트 쪽 유효성 검사가 필드의 모든 유효성 검사 규칙을 확인합니다. 유효성 검사 오류가 있으면 오류 메시지를 표시하고 양식을 게시하지 않습니다. JavaScript를 사용하지 않을 경우 클라이언트 쪽 유효성 검사가 수행되지 않으나 서버에서 유효하지 않은 게시 값을 탐지하며 양식 값이 오류 메시지와 함께 다시 표시됩니다. 이 자습서의 뒷부분에서 [모델 유효성 검사](xref:mvc/models/validation)를 더 자세히 다룹니다. *Views/Movies/Edit.cshtml* 보기 템플릿의 [Validation 태그 도우미](xref:mvc/views/working-with-forms)는 적절한 오류 메시지의 표시를 담당합니다.

![보기 편집: abc의 올바르지 않은 가격 값 예외에서는 필드 가격이 숫자여야 한다고 표시합니다. xyz의 잘못된 출시일 값 예외에서는 올바른 날짜를 입력하라고 표시합니다.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

영화 컨트롤러의 모든 `HttpGet` 메서드는 유사한 패턴을 따릅니다. 영화 개체(`Index`의 경우 개체 목록)를 가져오고 해당 개체(모델)를 보기에 전달합니다. `Create` 메서드는 빈 영화 개체를 `Create` 보기에 전달합니다. 생성, 편집, 삭제 또는 어떤 식으로든 데이터를 수정하는 모든 메서드는 메서드의 `[HttpPost]` 오버로드에서 해당 작업을 수행합니다. `HTTP GET` 메서드에서 데이터를 수정하는 것은 보안상 위험합니다. `HTTP GET` 메서드에서 데이터를 수정하는 것은 GET 요청이 응용 프로그램의 상태를 변경해서는 안 된다는 HTTP 모범 사례와 구조적 [REST](http://rest.elkstein.org/) 패턴에도 위배됩니다. 다시 말해 GET 작업 수행은 부작용 없이 안전하고 영속 데이터를 수정하지 않는 방법으로 이루어져야 합니다.

## <a name="additional-resources"></a>추가 자료

* [세계화 및 지역화](xref:fundamentals/localization)
* [태그 도우미 소개](xref:mvc/views/tag-helpers/intro)
* [태그 도우미 작성](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* [과도한 게시로부터 컨트롤러 보호](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Form 태그 도우미](xref:mvc/views/working-with-forms)
* [입력 태그 도우미](xref:mvc/views/working-with-forms)
* [레이블 태그 도우미](xref:mvc/views/working-with-forms)
* [선택 태그 도우미](xref:mvc/views/working-with-forms)
* [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [이전](working-with-sql.md)
> [다음](search.md)  
