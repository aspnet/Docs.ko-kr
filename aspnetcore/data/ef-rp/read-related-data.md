---
title: 6부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 관련 데이터 읽기
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 6부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061069"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>6부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 관련 데이터 읽기

작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

이 자습서에서는 관련 데이터를 읽고 표시하는 방법을 보여 줍니다. 관련된 데이터는 EF Core에서 탐색 속성에 로드하는 데이터입니다.

다음 그림은 이 자습서에 대해 완료된 페이지를 보여 줍니다.

![과정 인덱스 페이지](read-related-data/_static/courses-index30.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>즉시, 명시적 및 지연 로드

여러 가지 방법으로 EF Core가 관련 데이터를 엔터티의 탐색 속성에 로드할 수 있습니다.

* [즉시 로드](/ef/core/querying/related-data#eager-loading). 즉시 로드는 한 형식의 엔터티에 대한 쿼리가 관련 엔터티도 로드하는 경우입니다. 엔터티를 읽을 때 관련된 데이터가 검색됩니다. 이는 일반적으로 필요한 데이터를 모두 검색하는 단일 조인 쿼리를 발생시킵니다. EF 코어는 일부 형식의 즉시 로드에 대해 여러 쿼리를 실행합니다. 여러 쿼리를 실행하는 것이 큰 단일 쿼리보다 더 효율적일 수 있습니다. 즉시 로드는 `Include` 및 `ThenInclude` 메서드로 지정됩니다.

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)
 
  즉시 로드는 컬렉션 탐색이 포함된 경우 여러 쿼리를 보냅니다.

  * 주 쿼리에 대해 한 개 쿼리 
  * 로드 트리에서 각 컬렉션 "에지"에 대해 한 개 쿼리

* `Load`를 사용한 별도 쿼리: 별도 쿼리로 데이터를 검색할 수 있으며, EF Core에서 탐색 속성을 “수정”합니다. “수정”한다는 것은 EF Core가 탐색 속성을 자동으로 채운다는 것을 의미합니다. `Load`로 쿼리를 구분하는 것은 즉시 로드보다 더 명시적인 로드입니다.

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

  **참고:** EF Core는 이전에 컨텍스트 인스턴스에 로드된 다른 엔터티로 탐색 속성을 자동으로 수정합니다. 탐색 속성에 대한 데이터가 명시적으로 포함되지 *않더라도* 관련 엔터티의 일부 또는 전부가 이전에 로드된 경우에도 속성이 채워질 수 있습니다.

* [명시적 로드](/ef/core/querying/related-data#explicit-loading). 엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다. 필요할 때 관련된 데이터를 검색하기 위한 코드를 작성해야 합니다. 별도 쿼리가 있는 명시적 로드의 경우 여러 쿼리가 데이터베이스로 전송됩니다. 명시적 로드에서 코드는 로드될 탐색 속성을 지정합니다. `Load` 메서드를 사용하여 명시적 로드를 수행합니다. 예를 들어:

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* [지연 로드](/ef/core/querying/related-data#lazy-loading). 엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다. 탐색 속성에 처음으로 액세스하려고 할 때 해당 탐색 속성에 필요한 데이터가 자동으로 검색됩니다. 탐색 속성에 처음으로 액세스할 때마다 쿼리가 데이터베이스에 전송됩니다. 개발자가 N+1 패턴을 사용하는 경우처럼 지연 로드를 사용하면 부모를 로드하고 자식을 열거하는 성능이 저하될 수 있습니다.

## <a name="create-course-pages"></a>과정 페이지 만들기

`Course` 엔터티는 `Department` 엔터티가 포함된 탐색 속성을 포함합니다.

![Course.Department](read-related-data/_static/dep-crs.png)

과정에 대해 할당된 부서의 이름을 표시하려면 다음을 수행합니다.

* 관련된 `Department` 엔터티를 `Course.Department` 탐색 속성에 로드합니다.
* `Department` 엔터티의 `Name` 속성에서 이름을 가져옵니다.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>과정 페이지 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 다음 예외가 포함된 [학생 페이지 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따릅니다.

  * *Pages/Courses* 폴더를 만듭니다.
  * 모델 클래스에 `Course`를 사용합니다.
  * 새 컨텍스트 클래스를 만드는 대신 기존 컨텍스트 클래스를 사용합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Pages/Courses* 폴더를 만듭니다.

* 다음 명령을 실행하여 과정 페이지를 스캐폴드합니다.

  **Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Linux 또는 macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* *Pages/Courses/Index.cshtml.cs* 를 열고 `OnGetAsync` 메서드를 검사합니다. 스캐폴딩 엔진은 `Department` 탐색 속성에 대한 즉시 로드를 지정했습니다. `Include` 메서드가 즉시 로드를 지정합니다.

* 앱을 실행하고 **과정** 링크를 선택합니다. Department 열에 도움이 되지 않는 `DepartmentID`가 표시됩니다.

### <a name="display-the-department-name"></a>부서 이름 표시

다음 코드로 Pages/Courses/Index.cshtml.cs를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

앞의 코드는 `Course` 속성을 `Courses`로 변경하고 `AsNoTracking`을 추가합니다. 반환된 엔터티는 추적되지 않으므로 `AsNoTracking`이 성능을 개선합니다. 현재 컨텍스트에서 업데이트되지 않으므로 엔터티를 추적할 필요가 없습니다.

다음 코드로 *Pages/Courses/Index.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

스캐폴드 코드에 다음 변경 내용을 적용했습니다.

* `Course` 속성 이름을 `Courses`로 변경했습니다.
* `CourseID` 속성 값을 보여 주는 **Number** 열을 추가했습니다. 일반적으로 최종 사용자에게 의미가 없으므로 기본적으로 기본 키는 스캐폴드되지 않습니다. 그러나 이 경우 기본 키는 의미가 있습니다.
* 부서 이름을 표시하도록 **Department** 열을 변경했습니다. 코드는 `Department` 탐색 속성으로 로드되는 `Department` 엔터티의 `Name` 속성을 표시합니다.

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

앱을 실행하고 **Courses(과정)** 탭을 선택하여 부서 이름이 있는 목록을 봅니다.

![과정 인덱스 페이지](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select로 관련된 데이터 로드

`OnGetAsync` 메서드는 `Include` 메서드로 관련된 데이터를 로드합니다. `Select` 메서드는 필요한 관련 데이터만 로드하는 대안입니다. `Department.Name`과 같은 단일 항목에서는 SQL INNER JOIN을 사용합니다. 컬렉션의 경우 다른 데이터베이스 액세스를 사용하지만 컬렉션의 `Include` 연산자도 사용합니다.

다음 코드는 `Select` 메서드로 관련된 데이터를 로드합니다.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

위의 코드는 엔터티 형식을 반환하지 않으므로 추적이 수행되지 않습니다. EF 추적에 대한 자세한 내용은 [추적 및 비 추적 쿼리](/ef/core/querying/tracking)를 참조하세요.

`CourseViewModel`는:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

전체 예제는 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 및 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)를 참조하세요.

## <a name="create-instructor-pages"></a>강사 페이지 만들기

이 섹션에서는 강사 페이지를 스캐폴드하고 강사 인덱스 페이지에 관련된 과정 및 등록을 추가합니다.

<a name="IP"></a>
![강사 인덱스 페이지](read-related-data/_static/instructors-index30.png)

이 페이지는 다음과 같은 방법으로 관련된 데이터를 읽고 표시합니다.

* 강사 목록은 `OfficeAssignment` 엔터티에서 관련된 데이터를 표시합니다(이전 이미지에서 Office). `Instructor` 및 `OfficeAssignment` 엔터티는 일대영 또는 일 관계에 있습니다. 즉시 로드는 `OfficeAssignment` 엔터티에 사용됩니다. 즉시 로드는 일반적으로 관련된 데이터를 표시해야 할 때 더 효율적입니다. 이 경우 강사를 위한 사무실 할당이 표시됩니다.
* 사용자가 강사를 선택하면 관련된 `Course` 엔터티가 표시됩니다. `Instructor` 및 `Course` 엔터티는 다대다 관계에 있습니다. `Course` 엔터티 및 관련 `Department` 엔터티에 대해 즉시 로드가 사용됩니다. 이 경우 선택한 강사에 대한 과정만 필요하므로 별도 쿼리가 더 효율적일 수 있습니다. 이 예제에서는 탐색 속성에 있는 엔터티에서 탐색 속성에 대한 즉시 로드를 사용하는 방법을 보여 줍니다.
* 사용자가 과정을 선택하면 `Enrollments` 엔터티의 관련 데이터가 표시됩니다. 이전 이미지에서는 학생 이름 및 학점이 표시됩니다. `Course` 및 `Enrollment` 엔터티는 일대다 관계에 있습니다.

### <a name="create-a-view-model"></a>뷰 모델 만들기

강사 페이지는 서로 다른 세 테이블의 데이터를 표시합니다. 세 개의 테이블을 나타내는 세 개의 속성을 포함하는 뷰 모델이 필요합니다.

다음 코드로 *SchoolViewModels/InstructorIndexData.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>강사 페이지 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 다음 예외가 포함된 [학생 페이지 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따릅니다.

  * *Pages/Instructors* 폴더를 만듭니다.
  * 모델 클래스에 `Instructor`를 사용합니다.
  * 새 컨텍스트 클래스를 만드는 대신 기존 컨텍스트 클래스를 사용합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Pages/Instructors* 폴더를 만듭니다.

* 다음 명령을 실행하여 강사 페이지를 스캐폴드합니다.

  **Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Linux 또는 macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

업데이트하기 전에 스캐폴드된 페이지의 모양을 확인하려면 앱을 실행하고 강사 페이지로 이동합니다.

다음 코드로 *Pages/Instructors/Index.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

`OnGetAsync` 메서드는 선택한 강사의 ID에 대해 경로 데이터(선택 사항)를 받아들입니다.

*Pages/Instructors/Index.cshtml.cs* 파일에서 쿼리를 검사합니다.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

이 코드는 다음 탐색 속성에 대한 즉시 로드를 지정합니다.

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

`CourseAssignments` 및 `Course`에 대해 `Include` 및 `ThenInclude` 메서드가 반복되는 것을 알 수 있습니다. 이 반복은 `Course` 엔터티의 두 가지 탐색 속성에 대해 즉시 로드를 지정하는 데 필요합니다.

다음 코드는 강사가 선택되었을 때 실행됩니다(`id != null`).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

선택한 강사는 뷰 모델의 강사 목록에서 검색됩니다. 뷰 모델의 `Courses` 속성은 해당 강사의 `CourseAssignments` 탐색 속성에서 `Course` 엔터티로 로드됩니다.

`Where` 메서드는 컬렉션도 반환합니다. 그러나 이 경우 필터는 단일 엔터티를 선택하므로 `Single` 메서드를 호출하여 컬렉션을 단일 `Instructor` 엔터티로 변환합니다. `Instructor` 엔터티는 `CourseAssignments` 속성에 대한 액세스를 제공합니다. `CourseAssignments`는 관련 `Course` 엔터티에 대한 액세스를 제공합니다.

![강사 및 과정 간 관계 m:M](complex-data-model/_static/courseassignment.png)

`Single` 메서드는 컬렉션에 한 개 항목만 있을 때 컬렉션에서 사용됩니다. `Single` 메서드는 컬렉션이 비어 있거나 둘 이상의 항목이 있는 경우 예외를 throw합니다. 대안은 `SingleOrDefault`입니다. 컬렉션이 비어 있는 경우 기본값을 반환합니다(이 경우 Null).

다음 코드는 과정을 선택할 때 뷰 모델의 `Enrollments` 속성을 채웁니다.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>강사 인덱스 페이지 업데이트

다음 코드로 *Pages/Instructors/Index.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

위의 코드로 다음이 변경됩니다.

* `page` 지시어를 `@page`에서 `@page "{id:int?}"`로 업데이트합니다. `"{id:int?}"`는 경로 템플릿입니다. 경로 템플릿은 데이터를 라우팅할 URL에서 정수 쿼리 문자열을 변경합니다. 예를 들어, `@page` 지시문만 있는 강사에 대해 **Select** 링크를 클릭하면 다음과 같은 URL이 생성됩니다.

  `https://localhost:5001/Instructors?id=2`

  페이지 지시문이 `@page "{id:int?}"`이면 URL은 다음과 같습니다.

  `https://localhost:5001/Instructors/2`

* `item.OfficeAssignment`가 Null이 아닌 경우에만 `item.OfficeAssignment.Location`을 표시하는 **Office** 열을 추가합니다. 이는 일대영 또는 일 관계이기 때문에 관련된 OfficeAssignment 엔터티가 있을 수 없습니다.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 각 강사가 가르치는 과정을 표시하는 **Courses** 열을 추가합니다. 이 razor 구문에 대한 자세한 내용은 [명시적 줄 전환](xref:mvc/views/razor#explicit-line-transition)을 참조하세요.

* 선택된 강사 및 과정의 `tr` 요소에 `class="success"`를 동적으로 추가하는 코드를 추가합니다. 부트스트랩 클래스를 사용하여 선택된 행에 대한 배경색을 설정합니다.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select** 로 레이블 지정된 새 하이퍼링크를 추가합니다. 이 링크는 선택한 강사의 ID를 `Index` 메서드에 보내고 배경색을 설정합니다.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* 선택된 강사에 대한 과정 테이블을 추가합니다.

* 선택된 과정에 대한 학생 등록 테이블을 추가합니다.

앱을 실행하고 **강사** 탭을 선택합니다. 페이지에 관련된 `OfficeAssignment` 엔터티의 `Location`(사무실)이 표시됩니다. `OfficeAssignment`가 Null이면 빈 테이블 셀이 표시됩니다.

강사의 **Select** 링크를 클릭합니다. 강사에게 할당된 행 스타일 변경 내용 및 과정이 표시됩니다.

과정을 선택하여 등록된 학생 및 해당 등급의 목록을 봅니다.

![강사 및 과정이 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Single 사용

`Single` 메서드는 `Where` 메서드를 별도로 호출하는 대신 `Where` 조건을 전달할 수 있습니다.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Where 조건과 함께 `Single`을 사용하는 것은 개인적으로 선호하는 방법을 선택하면 됩니다. 이 방법은 `Where` 메서드를 사용하는 것보다 장점이 없습니다.

## <a name="explicit-loading"></a>명시적 로드

현재 코드는 `Enrollments` 및 `Students`에 대한 즉시 로드를 지정합니다.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

사용자가 과정에 등록된 내용을 거의 보지 않는다고 가정해 보겠습니다. 이 경우 최적화는 요청된 경우에만 등록 데이터를 로드합니다. 이 섹션에서는 `Enrollments` 및 `Students`의 명시적 로드를 사용하도록 `OnGetAsync`가 업데이트됩니다.

다음 코드로 *Pages/Instructors/Index.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

위의 코드는 등록 및 학생 데이터에 대한 *ThenInclude* 메서드 호출을 삭제합니다. 과정을 선택하면 명시적 로드 코드가 다음을 검색합니다.

* 선택한 과정에 대한 `Enrollment` 엔터티
* 각 `Enrollment`에 대한 `Student` 엔터티

앞의 코드는 `.AsNoTracking()`을 주석으로 처리하는 것을 알 수 있습니다. 탐색 속성은 추적된 엔터티에 대해서만 명시적으로 로드할 수 있습니다.

앱을 테스트합니다. 사용자 관점에서 앱은 이전 버전과 동일하게 동작합니다.

## <a name="next-steps"></a>다음 단계

다음 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다.

>[!div class="step-by-step"]
>[이전 자습서](xref:data/ef-rp/complex-data-model)
>[다음 자습서](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에서는 관련된 데이터를 읽고 표시합니다. 관련된 데이터는 EF Core에서 탐색 속성에 로드하는 데이터입니다.

해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

다음 그림은 이 자습서에 대해 완료된 페이지를 보여 줍니다.

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>관련된 데이터의 즉시, 명시적 및 지연 로드

여러 가지 방법으로 EF Core가 관련 데이터를 엔터티의 탐색 속성에 로드할 수 있습니다.

* [즉시 로드](/ef/core/querying/related-data#eager-loading). 즉시 로드는 한 형식의 엔터티에 대한 쿼리가 관련 엔터티도 로드하는 경우입니다. 엔터티를 읽을 때 관련된 데이터가 검색됩니다. 이는 일반적으로 필요한 데이터를 모두 검색하는 단일 조인 쿼리를 발생시킵니다. EF 코어는 일부 형식의 즉시 로드에 대해 여러 쿼리를 실행합니다. 여러 쿼리를 실행하는 것이 단일 쿼리가 있는 EF6의 일부 쿼리보다 효율적일 수 있습니다. 즉시 로드는 `Include` 및 `ThenInclude` 메서드로 지정됩니다.

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)
 
  즉시 로드는 컬렉션 탐색이 포함된 경우 여러 쿼리를 보냅니다.

  * 주 쿼리에 대해 한 개 쿼리 
  * 로드 트리에서 각 컬렉션 "에지"에 대해 한 개 쿼리

* `Load`를 사용한 별도 쿼리: 별도 쿼리로 데이터를 검색할 수 있으며, EF Core에서 탐색 속성을 “수정”합니다. "수정"한다는 것은 EF Core가 탐색 속성을 자동으로 채운다는 것을 의미합니다. `Load`로 쿼리를 구분하는 것은 즉시 로드보다 더 명시적인 로드입니다.

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

  참고: EF Core는 이전에 컨텍스트 인스턴스에 로드된 다른 엔터티로 탐색 속성을 자동으로 수정합니다. 탐색 속성에 대한 데이터가 명시적으로 포함되지 *않더라도* 관련 엔터티의 일부 또는 전부가 이전에 로드된 경우에도 속성이 채워질 수 있습니다.

* [명시적 로드](/ef/core/querying/related-data#explicit-loading). 엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다. 필요할 때 관련된 데이터를 검색하기 위한 코드를 작성해야 합니다. 별도 쿼리가 있는 명시적 로드의 경우 여러 쿼리가 DB로 전송됩니다. 명시적 로드에서 코드는 로드될 탐색 속성을 지정합니다. `Load` 메서드를 사용하여 명시적 로드를 수행합니다. 예를 들어:

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* [지연 로드](/ef/core/querying/related-data#lazy-loading). [지연 로드가 버전 2.1의 EF Core에 추가되었습니다](/ef/core/querying/related-data#lazy-loading). 엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다. 탐색 속성에 처음으로 액세스하려고 할 때 해당 탐색 속성에 필요한 데이터가 자동으로 검색됩니다. 탐색 속성에 처음으로 액세스할 때마다 쿼리가 DB에 전송됩니다.

* `Select` 연산자는 필요한 관련된 데이터만 로드합니다.

## <a name="create-a-course-page-that-displays-department-name"></a>부서 이름을 표시하는 과정 페이지 만들기

과정 엔터티는 `Department` 엔터티가 포함된 탐색 속성을 포함합니다. `Department` 엔터티는 과정이 할당된 부서를 포함합니다.

과정 목록에 할당된 부서 이름을 표시하려면

* `Department` 엔터티에서 `Name` 속성을 가져옵니다.
* `Department` 엔터티는 `Course.Department` 탐색 속성에서 가져옵니다.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>과정 모델 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Course`를 모델 클래스로 사용합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

위의 명령은 `Course` 모델을 스캐폴드합니다. Visual Studio에서 프로젝트를 엽니다.

*Pages/Courses/Index.cshtml.cs* 를 열고 `OnGetAsync` 메서드를 검사합니다. 스캐폴딩 엔진은 `Department` 탐색 속성에 대한 즉시 로드를 지정했습니다. `Include` 메서드가 즉시 로드를 지정합니다.

앱을 실행하고 **과정** 링크를 선택합니다. Department 열에 도움이 되지 않는 `DepartmentID`가 표시됩니다.

`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

위의 코드는 `AsNoTracking`을 추가합니다. 반환된 엔터티는 추적되지 않으므로 `AsNoTracking`이 성능을 개선합니다. 현재 컨텍스트에서 업데이트되지 않으므로 엔터티가 추적되지 않습니다.

*Pages/Courses/Index.cshtml* 을 다음 강조 표시된 내용으로 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

스캐폴드 코드에 다음 변경 내용을 적용했습니다.

* 제목을 Index(인덱스)에서 Courses(과정)로 변경했습니다.
* `CourseID` 속성 값을 보여 주는 **Number** 열을 추가했습니다. 일반적으로 최종 사용자에게 의미가 없으므로 기본적으로 기본 키는 스캐폴드되지 않습니다. 그러나 이 경우 기본 키는 의미가 있습니다.
* 부서 이름을 표시하도록 **Department** 열을 변경했습니다. 코드는 `Department` 탐색 속성으로 로드되는 `Department` 엔터티의 `Name` 속성을 표시합니다.

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

앱을 실행하고 **Courses(과정)** 탭을 선택하여 부서 이름이 있는 목록을 봅니다.

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select로 관련된 데이터 로드

`OnGetAsync` 메서드는 `Include` 메서드로 관련된 데이터를 로드합니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select` 연산자는 필요한 관련된 데이터만 로드합니다. `Department.Name`과 같은 단일 항목에서는 SQL INNER JOIN을 사용합니다. 컬렉션의 경우 다른 데이터베이스 액세스를 사용하지만 컬렉션의 `Include` 연산자도 사용합니다.

다음 코드는 `Select` 메서드로 관련된 데이터를 로드합니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

전체 예제는 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 및 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)를 참조하세요.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>과정 및 등록을 보여 주는 강사 페이지 만들기

이 섹션에서는 강사 페이지가 생성됩니다.

<a name="IP"></a>
![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

이 페이지는 다음과 같은 방법으로 관련된 데이터를 읽고 표시합니다.

* 강사 목록은 `OfficeAssignment` 엔터티에서 관련된 데이터를 표시합니다(이전 이미지에서 Office). `Instructor` 및 `OfficeAssignment` 엔터티는 일대영 또는 일 관계에 있습니다. 즉시 로드는 `OfficeAssignment` 엔터티에 사용됩니다. 즉시 로드는 일반적으로 관련된 데이터를 표시해야 할 때 더 효율적입니다. 이 경우 강사를 위한 사무실 할당이 표시됩니다.
* 사용자가 강사(이전 이미지에서 Harui)를 선택하는 경우 관련된 `Course` 엔터티가 표시됩니다. `Instructor` 및 `Course` 엔터티는 다대다 관계에 있습니다. `Course` 엔터티 및 관련 `Department` 엔터티에 대해 즉시 로드가 사용됩니다. 이 경우 선택한 강사에 대한 과정만 필요하므로 별도 쿼리가 더 효율적일 수 있습니다. 이 예제에서는 탐색 속성에 있는 엔터티에서 탐색 속성에 대한 즉시 로드를 사용하는 방법을 보여 줍니다.
* 사용자가 과정(이전 이미지에서 Chemistry)을 선택하면 `Enrollments` 엔터티의 관련된 데이터가 표시됩니다. 이전 이미지에서는 학생 이름 및 학점이 표시됩니다. `Course` 및 `Enrollment` 엔터티는 일대다 관계에 있습니다.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>강사 인덱스 뷰에 대한 뷰 모델 만들기

강사 페이지는 서로 다른 세 테이블의 데이터를 표시합니다. 세 개 테이블을 나타내는 세 개 엔터티가 포함된 뷰 모델이 생성됩니다.

다음 코드로 *SchoolViewModels* 폴더에서 *InstructorIndexData.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>강사 모델 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Instructor`를 모델 클래스로 사용합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

위의 명령은 `Instructor` 모델을 스캐폴드합니다. 
앱을 실행하고 강사 페이지로 이동합니다.

*Pages/Instructors/Index.cshtml.cs* 를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync` 메서드는 선택한 강사의 ID에 대해 경로 데이터(선택 사항)를 받아들입니다.

*Pages/Instructors/Index.cshtml.cs* 파일에서 쿼리를 검사합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

쿼리에는 다음 두 include가 포함됩니다.

* `OfficeAssignment`: [강사 뷰](#IP)에 표시됩니다.
* `CourseAssignments`: 가르친 과정을 가져옵니다.

### <a name="update-the-instructors-index-page"></a>강사 인덱스 페이지 업데이트

*Pages/Instructors/Index.cshtml* 을 다음 표시로 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

위의 표시로 다음이 변경됩니다.

* `page` 지시문을 `@page`에서 `@page "{id:int?}"`로 업데이트합니다. `"{id:int?}"`는 경로 템플릿입니다. 경로 템플릿은 데이터를 라우팅할 URL에서 정수 쿼리 문자열을 변경합니다. 예를 들어, `@page` 지시문만 있는 강사에 대해 **Select** 링크를 클릭하면 다음과 같은 URL이 생성됩니다.

  `http://localhost:1234/Instructors?id=2`

  page 지시문이 `@page "{id:int?}"`이면, 이전 URL은 다음과 같습니다.

  `http://localhost:1234/Instructors/2`

* 페이지 제목은 **Instructors(강사)** 입니다.
* `item.OfficeAssignment`가 Null이 아닌 경우에만 `item.OfficeAssignment.Location`을 표시하는 **Office** 열을 추가했습니다. 이는 일대영 또는 일 관계이기 때문에 관련된 OfficeAssignment 엔터티가 있을 수 없습니다.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 각 강사가 가르치는 과정을 표시하는 **Courses** 열을 추가했습니다. 이 razor 구문에 대한 자세한 내용은 [명시적 줄 전환](xref:mvc/views/razor#explicit-line-transition)을 참조하세요.

* 선택된 강사의 `tr` 요소에 `class="success"`를 동적으로 추가하는 코드를 추가했습니다. 부트스트랩 클래스를 사용하여 선택된 행에 대한 배경색을 설정합니다.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select** 로 레이블 지정된 새 하이퍼링크를 추가했습니다. 이 링크는 선택한 강사의 ID를 `Index` 메서드에 보내고 배경색을 설정합니다.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

앱을 실행하고 **강사** 탭을 선택합니다. 페이지에 관련된 `OfficeAssignment` 엔터티의 `Location`(사무실)이 표시됩니다. OfficeAssignment가 Null이면 빈 테이블 셀이 표시됩니다.

**Select** 링크를 클릭합니다. 행 스타일이 변경됩니다.

### <a name="add-courses-taught-by-selected-instructor"></a>선택한 강사가 가르친 과정 추가

*Pages/Instructors/Index.cshtml.cs* 에서 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

`public int CourseID { get; set; }` 추가

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

업데이트된 쿼리를 검토합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

이전 쿼리는 `Department` 엔터티를 추가합니다.

다음 코드는 강사가 선택되었을 때 실행됩니다(`id != null`). 선택한 강사는 뷰 모델의 강사 목록에서 검색됩니다. 뷰 모델의 `Courses` 속성은 해당 강사의 `CourseAssignments` 탐색 속성에서 `Course` 엔터티로 로드됩니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where` 메서드는 컬렉션도 반환합니다. 앞의 `Where` 메서드에서 단일 `Instructor` 엔터티만 반환됩니다. `Single` 메서드는 컬렉션을 단일 `Instructor` 엔터티로 변환합니다. `Instructor` 엔터티는 `CourseAssignments` 속성에 대한 액세스를 제공합니다. `CourseAssignments`는 관련 `Course` 엔터티에 대한 액세스를 제공합니다.

![강사 및 과정 간 관계 m:M](complex-data-model/_static/courseassignment.png)

`Single` 메서드는 컬렉션에 한 개 항목만 있을 때 컬렉션에서 사용됩니다. `Single` 메서드는 컬렉션이 비어 있거나 둘 이상의 항목이 있는 경우 예외를 throw합니다. 대안은 `SingleOrDefault`입니다. 컬렉션이 비어 있는 경우 기본값을 반환합니다(이 경우 Null). 비어 있는 컬렉션에 `SingleOrDefault` 사용

* 예외가 발생합니다(null 참조에서 `Courses` 속성을 찾으려고 시도하므로).
* 예외 메시지로는 문제의 원인을 명확하게 알기 어렵습니다.

다음 코드는 과정을 선택할 때 뷰 모델의 `Enrollments` 속성을 채웁니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

다음 태그를 *Pages/Instructors/Index.cshtml* Razor 페이지 끝에 추가합니다.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

위의 표시는 강사가 선택된 경우 강사와 관련된 과정의 목록을 표시합니다.

앱을 테스트합니다. 강사 페이지에서 **Select** 링크를 클릭합니다.

### <a name="show-student-data"></a>학생 데이터 표시

이 섹션에서는 선택한 과정에 대한 학생 데이터를 표시하도록 앱이 업데이트됩니다.

*Pages/Instructors/Index.cshtml.cs* 에서 `OnGetAsync` 메서드의 쿼리를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

*Pages/Instructors/Index.cshtml* 을 업데이트합니다. 다음 표시를 파일 끝에 추가합니다.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

이전 표시는 선택한 과정을 등록한 학생의 목록을 표시합니다.

페이지를 새로 고치고 강사를 선택합니다. 과정을 선택하여 등록된 학생 및 해당 등급의 목록을 봅니다.

![강사 및 과정이 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Single 사용

`Single` 메서드는 `Where` 메서드를 별도로 호출하는 대신 `Where` 조건을 전달할 수 있습니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

위의 `Single` 접근 방식은 `Where`를 사용하는 것보다 장점을 제공하지 않습니다. 일부 개발자는 `Single` 방식을 선호합니다.

## <a name="explicit-loading"></a>명시적 로드

현재 코드는 `Enrollments` 및 `Students`에 대한 즉시 로드를 지정합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

사용자가 과정에 등록된 내용을 거의 보지 않는다고 가정해 보겠습니다. 이 경우 최적화는 요청된 경우에만 등록 데이터를 로드합니다. 이 섹션에서는 `Enrollments` 및 `Students`의 명시적 로드를 사용하도록 `OnGetAsync`가 업데이트됩니다.

`OnGetAsync`를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

위의 코드는 등록 및 학생 데이터에 대한 *ThenInclude* 메서드 호출을 삭제합니다. 과정을 선택하면 강조 표시된 코드가 다음을 검색합니다.

* 선택한 과정에 대한 `Enrollment` 엔터티
* 각 `Enrollment`에 대한 `Student` 엔터티

위의 코드에서는 `.AsNoTracking()`을 주석 처리했습니다. 탐색 속성은 추적된 엔터티에 대해서만 명시적으로 로드할 수 있습니다.

앱을 테스트합니다. 사용자 관점에서 앱은 이전 버전과 동일하게 동작합니다.

다음 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전(1부)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [이 자습서의 YouTube 버전(2부)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[이전](xref:data/ef-rp/complex-data-model)
>[다음](xref:data/ef-rp/update-related-data)

::: moniker-end
