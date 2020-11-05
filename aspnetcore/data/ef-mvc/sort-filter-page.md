---
title: '자습서: 정렬, 필터링 및 페이징 추가 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 학생 인덱스 페이지에 정렬, 필터링 및 페이징 기능을 추가합니다. 단순 그룹화를 수행하는 페이지도 만듭니다.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 8e425d413471912c763c4892a90e9d12039efec4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053984"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>자습서: 정렬, 필터링 및 페이징 추가 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서는 학생 엔터티에 대한 기본적인 CRUD 작업을 위한 일련의 웹 페이지를 구현했습니다. 이 자습서에서는 학생 인덱스 페이지에 정렬, 필터링 및 페이징 기능을 추가합니다. 단순 그룹화를 수행하는 페이지도 만듭니다.

다음 그림에서는 작업이 완료되었을 때 페이지 모양을 보여 줍니다. 열 제목은 해당 열로 정렬하기 위해 사용자가 클릭할 수 있는 링크입니다. 열 제목을 반복해서 클릭하면 오름차순 및 내림차순으로 정렬 순서가 토글됩니다.

![학생 인덱스 페이지](sort-filter-page/_static/paging.png)

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 열 정렬 링크 추가
> * 검색 상자 추가
> * 학생 인덱스에 페이징 추가
> * Index 메서드에 페이징 추가
> * 페이징 링크 추가
> * 정보 페이지 만들기

## <a name="prerequisites"></a>사전 요구 사항

* [CRUD 기능 구현](crud.md)

## <a name="add-column-sort-links"></a>열 정렬 링크 추가

Student 인덱스 페이지에 정렬을 추가하려면 Students 컨트롤러의 `Index` 메서드를 변경하고 Student 인덱스 뷰에 코드를 추가합니다.

### <a name="add-sorting-functionality-to-the-index-method"></a>Index 메서드에 정렬 기능 추가

*StudentsController.cs* 에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

이 코드는 URL의 쿼리 문자열에서 `sortOrder` 매개 변수를 받습니다. 쿼리 문자열 값은 매개 변수로 ASP.NET Core MVC에 의해 작업 메서드에 제공됩니다. 매개 변수는 "Name" 또는 "Date" 문자열이며 필요에 따라 밑줄과 내림차순을 지정하는 문자열 "desc"가 옵니다. 기본 정렬 순서는 오름차순입니다.

인덱스 페이지를 처음 요청하면 쿼리 문자열은 없습니다. 학생은 성을 기준으로 오름차순으로 표시되며 이것은 `switch` 문의 제어 이동 사례로 설정된 기본값입니다. 사용자가 열 제목 하이퍼링크를 클릭하면 쿼리 문자열에 해당 `sortOrder` 값이 제공됩니다.

열 제목 하이퍼링크를 적절한 쿼리 문자열 값으로 구성하기 위해 뷰에 두 `ViewData` 요소(NameSortParm 및 DateSortParm)가 사용됩니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

이것은 3개로 구성된 문입니다. 첫 번째 문은 `sortOrder` 매개 변수는 Null인지, 비어 있는지 여부를 지정하고 Null이면 NameSortParm을 "name_desc"로 설정하고, 그렇지 않으면 빈 문자열로 설정합니다. 이러한 두 문을 사용하면 뷰에서 다음과 같이 열 제목 하이퍼링크를 설정할 수 있습니다.

|  현재 정렬 순서  | 성 하이퍼링크 | 날짜 하이퍼링크 |
|:--------------------:|:-------------------:|:--------------:|
| 성 오름차순  | descending          | ascending      |
| 성 내림차순 | ascending           | ascending      |
| 날짜 오름차순       | ascending           | descending     |
| 날짜 내림차순      | ascending           | ascending      |

메서드는 LINQ to Entities를 사용하여 정렬할 기준이 되는 열을 지정합니다. 이 코드는 switch 문 앞에 `IQueryable` 변수를 만들고 switch 문에서 변수를 수정한 다음, `switch` 문 다음에 `ToListAsync` 메서드를 호출합니다. `IQueryable` 변수를 작성하고 수정하면 데이터베이스에 쿼리가 보내지지 않습니다. `IQueryable` 개체를 `ToListAsync`와 같은 메서드를 호출하여 컬렉션으로 변환할 때까지 쿼리가 실행되지 않습니다. 따라서 이 코드는 `return View` 문까지 실행되지 않는 단일 쿼리가 됩니다.

많은 수의 열로 이 코드가 길어질 수 있습니다. [이 시리즈의 마지막 자습서](advanced.md#dynamic-linq)에서는 문자열 변수에 `OrderBy` 열의 이름을 전달할 수 있는 코드를 작성하는 방법을 보여 줍니다.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Student 인덱스 뷰에 열 제목 하이퍼링크 추가

*Views/Students/Index.cshtml* 에 있는 코드를 다음 코드로 바꾸어 열 제목 하이퍼링크를 추가합니다. 변경된 선이 강조 표시됩니다.

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

이 코드는 `ViewData` 속성의 정보를 사용하여 하이퍼링크를 적절한 쿼리 문자열 값으로 설정합니다.

앱을 실행하여 **Students(학생)** 탭을 선택하고 **Last Name(성)** 및 **Enrollment Date(등록 날짜)** 열 머리글을 클릭하여 제대로 정렬되는지 확인합니다.

![이름 순서로 된 학생 인덱스 페이지](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>검색 상자 추가

학생 인덱스 페이지에 필터링을 추가하려면 뷰에 텍스트 상자와 [제출] 단추를 추가하고 `Index` 메서드에 해당 변경 내용을 적용합니다. 텍스트 상자를 통해 이름 및 성 필드에서 검색할 문자열을 입력할 수 있습니다.

### <a name="add-filtering-functionality-to-the-index-method"></a>Index 메서드에 필터링 기능 추가

*StudentsController.cs* 에서 `Index` 메서드를 다음 코드로 바꿉니다(변경 내용이 강조 표시됨).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

`searchString` 매개 변수를 `Index` 메서드에 추가했습니다. 검색 문자열 값은 Index 뷰에 추가할 텍스트 상자에서 가져옵니다. 또한 성과 이름에 검색 문자열이 포함된 학생만 선택하는 where 절이 LINQ 문에 추가되었습니다. where 절을 추가하는 문은 검색할 값이 있는 경우에만 실행됩니다.

> [!NOTE]
> 여기에서는 `IQueryable` 개체에서 `Where` 메서드를 호출하고 있으며 필터는 서버에서 처리됩니다. 일부 시나리오에서는 메모리 내 컬렉션에서 확장 메서드로 `Where` 메서드를 호출할 수 있습니다. (예를 들어, EF `DbSet` 대신에 `IEnumerable` 컬렉션을 반환하는 리포지토리 메서드를 참조하도록 `_context.Students`로 참조를 변경한다고 가정해 보겠습니다.) 결과는 일반적으로 동일하지만 경우에 따라 다를 수 있습니다.
>
>예를 들어 `Contains` 메서드의 .NET Framework 구현은 기본적으로 대/소문자 구분 비교를 수행하지만 SQL Server에서는 SQL Server 인스턴스의 데이터 정렬 설정에 따라 결정됩니다. 이 설정은 기본적으로 대/소문자를 구분하지 않습니다. `ToUpper` 메서드를 호출하여 테스트가 명시적으로 대/소문자를 구분하지 않도록 설정할 수 있습니다.  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* . 이렇게 하면 `IQueryable` 개체 대신 `IEnumerable` 컬렉션을 반환하는 리포지토리를 사용하도록 코드를 나중에 변경할 경우 결과가 동일하게 유지됩니다. (`IEnumerable` 컬렉션에서 `Contains` 메서드를 호출하면 .NET Framework 구현을 가져오고 `IQueryable` 개체에서 호출하면 데이터베이스 공급자 구현을 가져옵니다.) 그러나 이 솔루션에서는 성능 저하가 발생합니다. `ToUpper` 코드는 TSQL SELECT 문의 WHERE 절에 함수를 배치합니다. 그러면 최적화 프로그램이 인덱스를 사용할 수 없게 됩니다. SQL은 대부분 대/소문자를 구분하지 않도록 설치된다는 것을 고려하면 대/소문자 구분 데이터 저장소로 마이그레이션할 때까지 `ToUpper` 코드를 사용하지 않는 것이 좋습니다.

### <a name="add-a-search-box-to-the-student-index-view"></a>Students 인덱스 뷰에 검색 상자 추가

*Views/Student/Index.cshtml* 에서 캡션, 텍스트 상자 및 **검색** 단추를 만들기 위해 여는 테이블 태그 바로 앞에 강조 표시된 코드를 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

이 코드는 `<form>` [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하여 검색 텍스트 상자 및 단추를 추가합니다. 기본적으로는 `<form>` 태그 도우미는 폼 데이터를 POST로 제출합니다. 즉, 매개 변수를 URL에 쿼리 문자열로 전달하지 않고 HTTP 메시지 본문으로 전달합니다. HTTP GET을 지정하면 폼 데이터가 URL에 쿼리 문자열로 전달되고 이를 통해 사용자는 URL을 책갈피로 지정할 수 있습니다. W3C 지침에 따라 작업이 업데이트되지 않을 때 GET을 사용해야 합니다.

앱을 실행하고 **Students(학생)** 탭을 선택하며 검색 문자열을 입력한 후 [검색]을 클릭하여 필터링이 작동하는지 확인합니다.

![필터링이 있는 학생 인덱스 페이지](sort-filter-page/_static/filtering.png)

URL에 검색 문자열이 포함되어 있습니다.

```html
http://localhost:5813/Students?SearchString=an
```

이 페이지를 책갈피로 지정하면 책갈피를 사용할 때 필터링된 목록을 얻게 됩니다. `method="get"`을 `form` 태그에 추가하면 쿼리 문자열이 생성됩니다.

이 단계에서 열 제목 정렬 링크를 클릭하면 **검색** 상자에 입력한 필터 값이 손실됩니다. 다음 섹션에서 이 문제를 해결합니다.

## <a name="add-paging-to-students-index"></a>학생 인덱스에 페이징 추가

학생 인덱스 페이지에 페이징을 추가하려면 `Skip` 및 `Take` 문을 사용하는 `PaginatedList` 클래스를 만들어 항상 테이블의 모든 행을 검색하는 대신, 서버에서 데이터를 필터링합니다. 그런 다음, `Index` 메서드를 추가로 변경하고 `Index` 뷰에 페이징 단추를 추가합니다. 다음 그림에는 페이징 단추가 나와 있습니다.

![페이징 링크가 있는 학생 인덱스 페이지](sort-filter-page/_static/paging.png)

프로젝트 폴더에서 `PaginatedList.cs`를 만든 후 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

이 코드에서 `CreateAsync` 메서드는 페이지 크기 및 페이지 번호를 사용하고 적절한 `Skip` 및 `Take` 문을 `IQueryable`에 적용합니다. `IQueryable`에서 `ToListAsync`를 호출하면 요청된 페이지만 포함하는 목록을 반환합니다. **이전** 및 **다음** 페이징 단추를 사용 또는 사용하지 않도록 하는 데 `HasPreviousPage` 및 `HasNextPage` 속성을 사용할 수 있습니다.

생성자가 비동기 코드를 실행할 수 없으므로 `CreateAsync` 메서드가 생성자 대신 `PaginatedList<T>` 개체를 만드는 데 사용됩니다.

## <a name="add-paging-to-index-method"></a>Index 메서드에 페이징 추가

*StudentsController.cs* 에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

이 코드는 페이지 번호 매개 변수, 현재 정렬 순서 매개 변수 및 현재 필터 매개 변수를 메서드 시그니처에 추가합니다.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

페이지가 처음 표시되거나 사용자가 페이징 또는 정렬 링크를 클릭하지 않으면 모든 매개 변수가 Null이 됩니다.  페이징 링크를 클릭하면 페이지 변수에 표시할 페이지 번호가 포함됩니다.

페이징 동안 정렬 순서를 동일하게 유지하기 위해 페이징 링크에 포함되어야 하므로 CurrentSort라는 `ViewData` 요소는 현재 정렬 순서의 뷰를 제공합니다.

CurrentFilter라는 `ViewData` 요소는 현재 필터 문자열이 포함된 뷰를 제공합니다. 이 값은 페이징 중 필터 설정을 유지하기 위해 페이징 링크에 포함되어야 하며 페이지를 다시 표시할 때 텍스트 상자에 복원되어야 합니다.

페이징 중에 검색 문자열이 변경되면 새 필터로 인해 다른 데이터가 표시될 수 있으므로 페이지는 1로 재설정되어야 합니다. 텍스트 상자에 값을 입력하고 [제출] 단추를 누르면 검색 문자열이 변경됩니다. 이 경우 `searchString` 매개 변수는 Null이 아닙니다.

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

`Index` 메서드의 끝에서 `PaginatedList.CreateAsync` 메서드는 학생 쿼리를 페이징을 지원하는 컬렉션 형식의 단일 학생 페이지로 변환합니다. 그러면 단일 학생 페이지가 뷰에 전달됩니다.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

`PaginatedList.CreateAsync` 메서드는 페이지 번호를 사용합니다. 두 개의 물음표는 Null 병합 연산자를 나타냅니다. Null 병합 연산자는 nullable 형식의 기본값을 정의합니다. `(pageNumber ?? 1)` 식은 값이 있는 경우 `pageNumber` 값을 반환하고 `pageNumber`가 Null이면 1일 반환합니다.

## <a name="add-paging-links"></a>페이징 링크 추가

*Views/Students/Index.cshtml* 에서 기존 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

페이지 맨 위에 `@model` 문은 뷰가 `List<T>` 개체 대신 `PaginatedList<T>` 개체를 가져오는 것을 지정합니다.

열 머리글 링크는 쿼리 문자열을 사용하여 현재 검색 문자열을 컨트롤러에 전달하므로 사용자가 필터 결과 내에서 정렬할 수 있습니다.

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

태그 도우미에 페이징 단추가 표시됩니다.

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

앱을 실행하고 [학생] 페이지로 이동합니다.

![페이징 링크가 있는 학생 인덱스 페이지](sort-filter-page/_static/paging.png)

다른 정렬 순서의 페이징 링크를 클릭하여 페이징이 작동하는지 확인합니다. 그런 다음, 검색 문자열을 입력하고 페이징을 다시 시도하여 정렬 및 필터링을 통해 페이징이 제대로 작동하는지 확인합니다.

## <a name="create-an-about-page"></a>정보 페이지 만들기

Contoso University 웹 사이트의 **정보** 페이지에는 각 등록 날짜에 등록된 학생 수가 표시됩니다. 여기에는 그룹화와 그룹에 대한 간단한 계산이 필요합니다. 이 작업을 수행하기 위해 다음을 수행합니다.

* 뷰에 전달해야 하는 데이터에 대해 뷰 모델 클래스를 만듭니다.
* 홈 컨트롤러에서 About 메서드를 만듭니다.
* 정보 뷰를 만듭니다.

### <a name="create-the-view-model"></a>뷰 모델 만들기

*Models* 폴더에 *SchoolViewModels* 폴더를 만듭니다.

새 폴더에서 *EnrollmentDateGroup.cs* 클래스 파일을 추가하고 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>홈 컨트롤러 수정

*HomeController.cs* 에서 파일 맨 위에 다음 using 문을 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

클래스의 여는 중괄호 바로 뒤에 데이터베이스 컨텍스트에 대한 클래스 변수를 추가하고 ASP.NET Core DI에서 컨텍스트 인스턴스를 가져옵니다.

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

다음 코드를 통해 `About` 메서드를 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

LINQ 문은 등록 날짜별로 학생 엔터티를 그룹화하고 각 그룹의 엔터티 수를 계산하며 결과를 `EnrollmentDateGroup` 뷰 모델 개체의 컬렉션에 저장합니다.

### <a name="create-the-about-view"></a>정보 뷰 만들기

다음 코드를 사용하여 *Views/Home/About.cshtml* 파일을 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

앱을 실행하고 [정보] 페이지로 이동합니다. 각 등록 날짜에 대한 학생 수가 테이블에 표시됩니다.

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 열 정렬 링크 추가
> * 검색 상자 추가
> * 학생 인덱스에 페이징 추가
> * Index 메서드에 페이징 추가
> * 페이징 링크 추가
> * 정보 페이지 만들기

마이그레이션을 사용하여 데이터 모델 변경 내용을 처리하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [다음: 데이터 모델 변경 처리](migrations.md)
