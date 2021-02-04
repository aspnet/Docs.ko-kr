---
title: '자습서: 관련 데이터 읽기 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 관련 데이터 즉, Entity Framework에서 탐색 속성으로 로드하는 데이터를 읽고 표시합니다.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 5f0e8efdf57e0a8ba5483680224d33ea5d60f06a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530153"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>자습서: 관련 데이터 읽기 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서 School 데이터 모델을 완료했습니다. 이 자습서에서는 관련 데이터 즉, Entity Framework에서 탐색 속성으로 로드하는 데이터를 읽고 표시합니다.

다음 그림에서는 사용할 페이지를 보여 줍니다.

![강좌 인덱스 페이지](read-related-data/_static/courses-index.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 관련 데이터를 로드하는 방법 알아보기
> * 강좌 페이지 만들기
> * 강사 페이지 만들기
> * 명시적 로드에 대해 알아보기

## <a name="prerequisites"></a>사전 요구 사항

* [복합 데이터 모델 만들기](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>관련 데이터를 로드하는 방법 알아보기

Entity Framework와 같은 ORM(개체-관계형 매핑) 소프트웨어에서 관련된 데이터를 엔터티의 탐색 속성으로 로드할 수 있는 여러 가지 방법이 있습니다.

* 즉시 로드 엔터티를 읽을 때 관련된 데이터가 함께 검색됩니다. 이는 일반적으로 필요한 데이터를 모두 검색하는 단일 조인 쿼리를 발생시킵니다. `Include` 및 `ThenInclude` 메서드를 사용하여 Entity Framework Core에 즉시 로드를 지정합니다.

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)

  별도 쿼리에서 일부 데이터를 검색할 수 있으며 EF는 탐색 속성을 "수정합니다".  즉, EF는 이전에 검색된 엔터티의 탐색 속성에 속해 있는 개별적으로 검색된 엔터티를 자동으로 추가합니다. 관련된 데이터를 검색하는 쿼리의 경우 `ToList` 또는 `Single`과 같은 목록 또는 개체를 반환하는 메서드 대신 `Load` 메서드를 사용할 수 있습니다.

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

* 명시적 로드 엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다. 필요한 경우 관련된 데이터를 검색하는 코드를 작성합니다. 별도 쿼리가 있는 즉시 로드의 경우처럼 명시적 로드로 인해 여러 쿼리가 데이터베이스로 전송됩니다. 명시적 로드와의 차이점은 코드는 로드될 탐색 속성을 지정한다는 점입니다. Entity Framework Core 1.1에서 `Load` 메서드를 사용하여 명시적 로드를 수행할 수 있습니다. 예를 들어:

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* 지연 로드 엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다. 그러나 탐색 속성에 처음으로 액세스하려고 할 때 해당 탐색 속성에 필요한 데이터가 자동으로 검색됩니다. 탐색 속성에서 처음으로 데이터를 가져오려고 할 때마다 쿼리가 데이터베이스에 전송됩니다. Entity Framework Core 1.0은 지연 로드를 지원하지 않습니다.

### <a name="performance-considerations"></a>성능 고려 사항

검색된 모든 엔터티에 대해 관련된 데이터가 필요한 경우 데이터베이스에 전송된 단일 쿼리는 검색된 각 엔터티에 대한 별도 쿼리보다 일반적으로 더 효율적이므로 즉시 로드는 종종 최상의 성능을 제공합니다. 예를 들어 각 부서에 10개의 관련된 강좌가 있다고 가정합니다. 모든 관련된 데이터의 즉시 로드로 인해 데이터베이스에 대한 단일(조인) 쿼리 및 단일 왕복이 발생합니다. 각 부서의 강좌에 대한 별도 쿼리는 데이터베이스에 대한 11개 왕복을 발생시킵니다. 데이터베이스에 대한 추가 왕복은 대기 시간이 길 때 성능에 특히 악영향을 줍니다.

반면에 일부 시나리오에서 별도 쿼리는 더 효율적입니다. 하나의 쿼리에서 관련된 모든 데이터의 즉시 로드로 인해 SQL Server에서 효율적으로 처리할 수 없는 매우 복잡한 조인이 생성될 수 있습니다. 또는 처리하는 엔터티 집합의 하위 집합에 대해서만 엔터티의 탐색 속성에 액세스해야 하는 경우 별도 쿼리는 이전의 모든 즉시 로드에서 필요한 것보다 더 많은 데이터를 검색하므로 더 잘 수행할 수 있습니다. 성능이 중요한 경우 최상의 선택을 위해 두 가지 방식으로 성능을 테스트하는 것이 가장 좋습니다.

## <a name="create-a-courses-page"></a>강좌 페이지 만들기

강좌 엔터티는 강좌에 할당된 부서의 부서 엔터티를 포함하는 탐색 속성을 포함합니다. 강좌의 목록에 할당된 부서의 이름을 표시하려면 `Course.Department` 탐색 속성에 있는 부서 엔터티에서 이름 속성을 가져와야 합니다.

보기로 **MVC 컨트롤러에 대한 동일한 옵션을 사용하고, 다음 그림에서 표시된 것과 같이 학생 컨트롤러에 대해 앞에서 수행한 Entity Framework** 스캐폴더를 사용하여 강좌 엔터티 형식에 대해 CoursesController라는 컨트롤러를 만듭니다.

![강좌 컨트롤러 추가](read-related-data/_static/add-courses-controller.png)

*CoursesController.cs* 를 열고 `Index` 메서드를 검사합니다. 자동 스캐폴딩은 `Include` 메서드를 사용하여 `Department` 탐색 속성에 대해 즉시 로드를 지정했습니다.

`Index` 메서드를 강좌 엔터티를 반환하는 `IQueryable`에 대해 더욱 적절한 이름을 사용하는 다음 코드로 바꿉니다(`schoolContext` 대신 `courses`).

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

*Views/Courses/Index.cshtml* 을 열고 템플릿 코드를 다음 코드로 바꿉니다. 변경 내용이 강조 표시되어 있습니다.

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

스캐폴드 코드에 다음 변경 내용을 만들었습니다.

* 인덱스에서 강좌로 제목이 변경됐습니다.

* `CourseID` 속성 값을 보여 주는 **Number** 열을 추가했습니다. 일반적으로 최종 사용자에게 의미가 없으므로 기본적으로 기본 키는 스캐폴드되지 않습니다. 그러나 이 경우 기본 키는 의미가 있으며 표시하길 원합니다.

* 부서 이름을 표시하도록 **부서** 열을 변경했습니다. 코드는 `Department` 탐색 속성으로 로드되는 부서 엔터티의 `Name` 속성을 표시합니다.

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

앱을 실행하고 **강좌** 탭을 선택하여 부서 이름이 있는 목록을 봅니다.

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>강사 페이지 만들기

이 섹션에서는 강사 페이지를 표시하기 위해 강사 엔터티에 대한 컨트롤러 및 보기를 만듭니다.

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

이 페이지는 다음과 같은 방법으로 관련된 데이터를 읽고 표시합니다.

* 강사 목록은 OfficeAssignment 엔터티에서 관련된 데이터를 표시합니다. 강사와 OfficeAssignment 엔터티는 일대영 또는 일 관계에 있습니다. OfficeAssignment 엔터티에 대해 즉시 로드를 사용합니다. 이전에 설명한 대로 기본 테이블의 검색된 모든 행에 관련된 데이터가 필요한 경우 즉시 로드는 일반적으로 더 효율적입니다. 이 경우 표시된 모든 강사에 대한 사무실 할당을 표시하길 원합니다.

* 사용자가 강사를 선택하면 관련된 강좌 엔터티가 표시됩니다. 강사 및 강좌 엔터티는 다대다 관계에 있습니다. 강좌 엔터티 및 관련된 해당 부서 엔터티에 대해 즉시 로드를 사용합니다. 이 경우 선택한 강사에 대해서만 강좌가 필요하기 때문에 별도 쿼리가 더 효율적일 수 있습니다. 그러나 이 예제에서는 탐색 속성에 있는 엔터티 내에서 탐색 속성에 대한 즉시 로드를 사용하는 방법을 보여 줍니다.

* 사용자가 강좌를 선택하면 등록 엔터티 집합에서 관련된 데이터가 표시됩니다. 강좌 및 등록 엔터티는 일대다 관계에 있습니다. 등록 엔터티 및 관련된 해당 학생 엔터티에 대해 별도 쿼리를 사용합니다.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>강사 인덱스 보기에 대한 뷰 모델 만들기

강사 페이지는 서로 다른 세 테이블에서 데이터를 표시합니다. 따라서 각각이 테이블 중 하나에 대한 데이터를 보유하는 세 가지 속성을 포함하는 보기 모델을 만듭니다.

*SchoolViewModels* 폴더에서 *InstructorIndexData.cs* 를 만들고 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>강사 컨트롤러 및 뷰 만들기

다음 그림에 나와 있는 것처럼 EF 읽기/쓰기 동작으로 강사 컨트롤러를 만듭니다.

![강사 컨트롤러 추가](read-related-data/_static/add-instructors-controller.png)

*InstructorsController.cs* 를 열고 ViewModels 네임스페이스에 대해 using 문을 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

인덱스 메서드를 다음 코드로 바꿔 관련된 데이터의 즉시 로드를 수행하고 보기 모델에 배치합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

메서드는 선택한 강사 및 선택한 강좌의 ID 값을 제공하는 선택적 경로 데이터(`id`) 및 쿼리 문자열 매개 변수(`courseID`)를 허용합니다. 매개 변수는 페이지의 **선택** 하이퍼링크에서 제공됩니다.

코드는 보기 모델의 인스턴스를 만들고 강사 목록에 배치하여 시작합니다. 코드는 `Instructor.OfficeAssignment` 및 `Instructor.CourseAssignments` 탐색 속성에 대한 즉시 로드를 지정합니다. `CourseAssignments` 속성 내에서 `Course` 속성이 로드되고, 해당 속성 내에서 `Enrollments` 및 `Department` 속성이 로드되고, 각 `Enrollment` 엔터티 내에서 `Student` 속성이 로드됩니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

보기는 항상 OfficeAssignment 엔터티가 필요하므로 동일한 쿼리에서 인출하는 것이 더 효율적입니다. 강좌 엔터티는 강사가 웹 페이지에서 선택된 경우에 필요하므로 단일 쿼리는 페이지가 선택된 강좌와 함께 더 자주 표시되는 경우에만(함께 표시되지 않는 것보다) 여러 쿼리보다 낫습니다.

`Course`에서 두 개의 속성이 필요하므로 코드는 `CourseAssignments` 및 `Course`를 반복합니다. `ThenInclude` 호출의 첫 번째 문자열은 `CourseAssignment.Course`, `Course.Enrollments` 및 `Enrollment.Student`를 가져옵니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

코드의 해당 지점에서 다른 `ThenInclude`는 필요하지 않은 `Student`의 탐색 속성에 대한 것입니다. 하지만 `Include`를 호출하면 `Instructor` 속성으로 다시 시작하므로 체인을 통해 다시 이동해야 합니다. 이 때 `Course.Enrollments` 대신 `Course.Department`를 지정합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

다음 코드는 강사가 선택되었을 때 실행합니다. 선택한 강사는 보기 모델의 강사 목록에서 검색됩니다. 보기 모델의 `Courses` 속성은 해당 강사의 `CourseAssignments` 탐색 속성에서 강좌 엔터티로 로드됩니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

`Where` 메서드는 컬렉션을 반환하지만 이 경우 해당 메서드에 전달된 조건으로 반환되는 단일 강사 엔터티만 발생할 수 있습니다. `Single` 메서드는 컬렉션을 단일 강사 엔터티로 변환합니다. 이는 해당 엔터티의 `CourseAssignments` 속성에 대한 액세스를 제공합니다. `CourseAssignments` 속성은 `CourseAssignment` 엔터티를 포함합니다. 여기에서 관련된 `Course` 엔터티만을 원합니다.

하나의 항목만을 가질 컬렉션을 아는 경우 컬렉션에서 `Single` 메서드를 사용합니다. 단일 메서드는 전달된 컬렉션이 비어 있거나 둘 이상의 항목이 있는 경우 예외를 throw합니다. 대안은 `SingleOrDefault`입니다. 컬렉션이 비어 있는 경우 기본값을 반환합니다(이 경우 Null). 그러나 이 경우에 여전히 예외가 발생하며(null 참조에서 `Courses` 속성을 찾으려는 시도에서), 예외 메시지는 문제의 원인을 덜 명확하게 나타냅니다. `Single` 메서드를 호출하는 경우 `Where` 메서드를 별도로 호출하는 대신 Where 조건을 전달할 수도 있습니다.

```csharp
.Single(i => i.ID == id.Value)
```

위 코드를 아래 코드 대신 사용합니다.

```csharp
.Where(i => i.ID == id.Value).Single()
```

다음으로 강좌를 선택한 경우 선택한 강좌가 보기 모델의 강좌 목록에서 검색됩니다. 보기 모델의 `Enrollments` 속성은 해당 강좌의 `Enrollments` 탐색 속성에서 등록 엔터티로 로드됩니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>강사 인덱스 뷰 수정

*Views/Instructors/Index.cshtml* 에서 템플릿 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

기존 코드에 다음 변경 내용을 만들었습니다.

* 모델 클래스를 `InstructorIndexData`로 변경했습니다.

* 페이지 제목을 **인덱스** 에서 **강사** 로 변경했습니다.

* `item.OfficeAssignment`가 Null이 아닌 경우에만 `item.OfficeAssignment.Location`을 표시하는 **사무실** 열을 추가했습니다. (이는 일대영 또는 일 관계이기 때문에 관련된 OfficeAssignment 엔터티가 있을 수 없습니다.)

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 각 강사가 가르치는 과정을 표시하는 **Courses** 열을 추가했습니다. 자세한 내용은 Razor 구문 문서의 [명시적 줄 전환](xref:mvc/views/razor#explicit-line-transition) 섹션을 참조하세요.

* 선택된 강사의 `tr` 요소에 부트스트랩 CSS 클래스를 조건부로 추가하는 코드를 추가했습니다. 해당 클래스는 선택된 행의 배경색을 설정합니다.

* 선택된 강사의 ID가 `Index` 메서드에 전송되도록 하는 각 행의 다른 링크 앞에 새 하이퍼링크 레이블이 지정된 **Select** 를 즉시 추가했습니다.

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

앱을 실행하고 **강사** 탭을 선택합니다. 페이지는 관련된 OfficeAssignment 엔터티가 없는 경우 관련된 OfficeAssignment 엔터티의 위치 속성 및 빈 테이블 셀을 표시합니다.

![선택한 항목이 없는 강사 인덱스 페이지](read-related-data/_static/instructors-index-no-selection.png)

*Views/Instructors/Index.cshtml* 파일에서 닫는 테이블 요소(파일의 끝부분) 뒤에 다음 코드를 추가합니다. 이 코드는 강사가 선택된 경우 강사와 관련된 강좌의 목록을 표시합니다.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

이 코드는 보기 모델의 `Courses` 속성을 읽어 강좌의 목록을 표시합니다. 또한 선택된 강좌의 ID를 `Index` 동작 메서드에 전송하는 **Select** 하이퍼링크를 제공합니다.

페이지를 새로 고치고 강사를 선택합니다. 이제 선택된 강사에 할당된 강좌를 표시하는 표가 표시되고 각 강좌에 대해 할당된 부서의 이름이 표시됩니다.

![강사가 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index-instructor-selected.png)

방금 추가한 코드 블록 뒤에 다음 코드를 추가합니다. 해당 강좌가 선택된 경우에 강좌에 등록된 학생의 목록을 표시합니다.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

이 코드는 강좌에 등록한 학생의 목록을 표시하기 위해 보기 모델의 등록 속성을 읽습니다.

페이지를 다시 새로 고치고 강사를 선택합니다. 그런 다음, 강좌를 선택하여 등록된 학생 및 해당 등급의 목록을 봅니다.

![강사 및 과정이 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>명시적 로드 정보

*InstructorsController.cs* 에서 강사 목록을 검색한 경우 `CourseAssignments` 탐색 속성에 대한 즉시 로드를 지정했습니다.

사용자가 선택된 강사 및 강좌에서 등록을 드물게 확인하려고 한다는 것을 예상했다고 가정합니다. 이 경우 요청된 경우에만 등록 데이터를 로드할 수 있습니다. 명시적 로드를 수행하는 방법의 예를 보려면 `Index` 메서드를 등록에 대한 즉시 로드를 제거하고 해당 속성을 명시적으로 로드하는 다음 코드로 바꿉니다. 코드 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

새 코드는 강사 엔터티를 검색하는 코드에서 등록 데이터에 대한 *ThenInclude* 메서드 호출을 삭제합니다. 이 코드는 `AsNoTracking` 또한 삭제합니다.  강사 및 강좌가 선택된 경우 강조 표시된 코드는 선택된 강좌에 대한 등록 엔터티 및 각 등록에 대한 학생 엔터티를 검색합니다.

앱을 실행하고, 강사 인덱스 페이지로 이동하면 데이터가 검색되는 방법을 변경했더라도 페이지에 표시되는 내용에 차이가 없습니다.

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 관련 데이터를 로드하는 방법 알아보기
> * 강좌 페이지 만들기
> * 강사 페이지 만들기
> * 명시적 로드에 대해 알아보기

관련 데이터를 업데이트하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [관련 데이터 업데이트](update-related-data.md)
