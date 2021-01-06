---
title: '자습서: 관련 데이터 업데이트 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 외래 키 필드 및 탐색 속성을 업데이트하여 관련된 데이터를 업데이트합니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
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
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: f41d0b1d7ba46ef4867e0d160dc61f21042ce403
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053945"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>자습서: 관련 데이터 업데이트 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서는 관련 데이터를 표시했습니다. 이 자습서에서는 외래 키 필드 및 탐색 속성을 업데이트하여 관련된 데이터를 업데이트합니다.

다음 그림에서는 사용할 일부 페이지를 보여 줍니다.

![강좌 편집 페이지](update-related-data/_static/course-edit.png)

![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 강좌 페이지 사용자 지정
> * 강사 편집 페이지 추가
> * 편집 페이지에 강좌 추가
> * 삭제 페이지 업데이트
> * 만들기 페이지에 사무실 위치 및 강좌 추가

## <a name="prerequisites"></a>사전 요구 사항

* [관련 데이터 읽기](read-related-data.md)

## <a name="customize-courses-pages"></a>강좌 페이지 사용자 지정

새 강좌 엔터티가 만들어질 때 기존 부서에 대한 관계가 있어야 합니다. 이를 수행하기 위해 스캐폴드 코드는 컨트롤러 메서드 및 부서를 선택하기 위한 드롭다운 목록을 포함하는 만들기 및 편집 보기를 포함합니다. 드롭다운 목록은 `Course.DepartmentID` 외래 키 속성을 설정하고, 이는 적절한 부서 엔터티로 `Department` 탐색 속성을 로드하기 위해 필요한 모든 Entity Framework입니다. 스캐폴드 코드를 사용하지만 오류 처리를 추가하고 드롭다운 목록을 정렬하도록 약간 변경합니다.

*CoursesController.cs* 에서 4개의 만들기 및 편집 메서드를 삭제하고 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

`Edit` HttpPost 메서드 후에 드롭다운 목록에 대한 부서 정보를 로드하는 새 메서드를 만듭니다.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList` 메서드는 이름을 기준으로 정렬하는 모든 부서의 목록을 가져오고, 드롭다운 목록에 대한 `SelectList` 컬렉션을 만들고, 컬렉션을 `ViewBag`의 보기에 전달합니다. 메서드는 호출 코드가 드롭다운 목록이 렌더링될 때 선택될 항목을 지정하도록 허용하는 선택적 `selectedDepartment` 매개 변수를 허용합니다. 보기가 "DepartmentID"라는 이름을 `<select>` 태그 도우미에 전달하면 도우미는 "DepartmentID"라는 `SelectList`에 대한 `ViewBag` 개체에서 보는 것을 압니다.

새 강좌의 경우 부서는 아직 설정되지 않기 때문에 HttpGet `Create` 메서드는 선택한 항목을 설정하지 않고 `PopulateDepartmentsDropDownList` 메서드를 호출합니다.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet `Edit` 메서드는 편집 중인 강좌에 이미 할당되어 있는 부서의 ID에 따라 선택한 항목을 설정합니다.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

`Create` 및 `Edit` 모두에 대한 HttpPost 메서드는 오류가 발생한 후 페이지를 다시 표시할 때 선택한 항목을 설정하는 코드를 포함합니다. 이렇게 하면 오류 메시지를 표시하기 위해 페이지가 다시 표시될 때 선택한 부서에 관계 없이 선택된 상태로 유지됩니다.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>세부 정보 및 삭제 메서드에 AsNoTracking 추가

강좌 세부 정보 및 삭제 페이지의 성능을 최적화하기 위해 `Details` 및 HttpGet `Delete` 메서드에 `AsNoTracking` 호출을 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>강좌 보기 수정

*Views/Courses/Create.cshtml* 에서 **부서** 드롭다운 목록에 "부서 선택" 옵션을 추가하고, **DepartmentID** 에서  **부서** 로 캡션을 변경하고, 유효성 검사 메시지를 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

*Views/Courses/Edit.cshtml* 에서 부서 필드에 대해 *Create.cshtml* 에서 수행한 동일한 변경 내용을 만듭니다.

또한 *Views/Courses/Edit.cshtml* 에서 **제목** 필드 전에 강좌 번호 필드를 추가합니다. 강좌 번호는 기본 키이기 때문에 표시되지만 변경될 수 없습니다.

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

편집 보기에 강좌 번호에 대해 이미 숨겨진 필드(`<input type="hidden">`)가 있습니다. 사용자가 **편집** 페이지에서 **저장** 을 클릭할 때 강좌 번호가 게시된 데이터에 삽입되도록 하지 않으므로 `<label>` 태그 도우미를 추가하는 것은 숨겨진 필드에 대한 필요성을 없애지 않습니다.

*Views/Courses/Delete.cshtml* 에서 위쪽에 강좌 번호 필드를 추가하고 부서 ID를 부서 이름으로 변경합니다.

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

*Views/Courses/Details.cshtml* 에서 *Delete.cshtml* 에 대해 수행한 동일한 변경 내용을 만듭니다.

### <a name="test-the-course-pages"></a>강좌 페이지 테스트

앱을 실행하고, **강좌** 탭을 선택하고, **새로 만들기** 를 클릭하고, 새 강좌에 대한 데이터를 입력합니다.

![강좌 만들기 페이지](update-related-data/_static/course-create.png)

**만들기** 를 클릭합니다. 강좌 인덱스 페이지가 목록에 추가된 새 강좌로 표시됩니다. 인덱스 페이지 목록의 부서 이름은 관계가 올바르게 설정되었음을 표시하는 탐색 속성에서 제공됩니다.

강좌 인덱스 페이지의 강좌에서 **편집** 을 클릭합니다.

![강좌 편집 페이지](update-related-data/_static/course-edit.png)

페이지에서 데이터를 변경하고 **저장** 을 클릭합니다. 강좌 인덱스 페이지가 업데이트된 강좌 데이터로 표시됩니다.

## <a name="add-instructors-edit-page"></a>강사 편집 페이지 추가

강사 레코드를 편집할 때 강사의 사무실 할당을 업데이트할 수 있습니다. 강사 엔터티에는 OfficeAssignment 엔터티와 일대영 또는 일 관계가 있으며 코드가 다음과 같은 경우를 처리해야 함을 의미합니다.

* 사용자가 사무실 할당 선택을 취소하고 원래 값이 있었던 경우 OfficeAssignment 엔터티를 삭제합니다.

* 사용자가 사무실 할당 값을 입력하고 원래 값이 비어 있었던 경우 새 OfficeAssignment 엔터티를 만듭니다.

* 사용자가 사무실 할당의 값을 변경하는 경우 기존 OfficeAssignment 엔터티의 값을 변경합니다.

### <a name="update-the-instructors-controller"></a>강사 컨트롤러 업데이트

*InstructorsController.cs* 에서 강사 엔터티의 `OfficeAssignment` 탐색 속성을 로드하고 `AsNoTracking`을 호출하도록 HttpGet `Edit` 메서드에서 코드를 변경합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

HttpPost `Edit` 메서드를 다음 코드로 바꿔 사무실 할당 업데이트를 처리합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

코드는 다음을 수행합니다.

* 서명은 이제 HttpGet `Edit` 메서드와 동일하므로 메서드 이름을 `EditPost`로 변경합니다(`ActionName` 특성은 `/Edit/` URL이 여전히 사용됨을 지정함).

* `OfficeAssignment` 탐색 속성에 대한 즉시 로드를 사용하여 데이터베이스에서 현재 강사 엔터티를 가져옵니다. 이는 HttpGet `Edit` 메서드에서 수행한 것과 동일합니다.

* 모델 바인더의 값으로 검색된 강사 엔터티를 업데이트합니다. `TryUpdateModel` 오버로드를 통해 포함하려는 속성을 선언할 수 있습니다. 이렇게 하면 [두 번째 자습서](crud.md)에 설명된 대로 초과 게시를 방지합니다.

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* 사무실 위치가 비어 있는 경우 OfficeAssignment 테이블의 관련된 행이 삭제되도록 Instructor.OfficeAssignment 속성을 Null로 설정합니다.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* 변경 내용을 데이터베이스에 저장합니다.

### <a name="update-the-instructor-edit-view"></a>강사 편집 보기 업데이트

*Views/Instructors/Edit.cshtml* 에서 **저장** 단추 앞의 끝에 사무실 위치를 편집하기 위해 새 필드를 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

앱을 실행하고, **강사** 탭을 선택한 다음, 강사에서 **편집** 을 클릭합니다. **사무실 위치** 를 변경하고 **저장** 을 클릭합니다.

![강사 편집 페이지](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>편집 페이지에 강좌 추가

강사는 강좌 수에 관계 없이 가르칠 수 있습니다. 이제 다음 스크린샷에 표시된 것처럼 확인란 그룹을 사용하여 강좌 할당을 변경하는 기능을 추가하여 강사 편집 페이지를 향상시킵니다.

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

강좌와 강사 엔터티 간의 관계는 다대다입니다. 관계를 추가하고 제거하려면 CourseAssignments 조인 엔터티 집합 간에 엔터티를 추가하고 제거합니다.

강사에게 할당된 강좌를 변경할 수 있도록 하는 UI는 확인란의 그룹입니다. 데이터베이스의 모든 강좌에 대한 확인란이 표시되고 강사에게 현재 할당되어 있는 것이 선택됩니다. 사용자는 확인란을 선택하거나 선택 취소하여 강좌 할당을 변경할 수 있습니다. 강좌의 수가 훨씬 큰 경우 보기에서 데이터를 나타내는 다른 방법을 사용하길 원할 것입니다. 하지만 관계를 만들거나 삭제하기 위해 조인 엔터티를 조작하는 동일한 메서드를 사용합니다.

### <a name="update-the-instructors-controller"></a>강사 컨트롤러 업데이트

확인란의 목록에 대한 보기에 데이터를 제공하려면 보기 모델 클래스를 사용합니다.

*SchoolViewModels* 폴더에서 *AssignedCourseData.cs* 를 만들고 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

*InstructorsController.cs* 에서 HttpGet `Edit` 메서드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

코드는 `Courses` 탐색 속성에 대해 즉시 로드를 추가하고 새 `PopulateAssignedCourseData` 메서드를 호출하여 `AssignedCourseData` 보기 모델 클래스를 사용하여 확인란 배열에 대한 정보를 제공합니다.

`PopulateAssignedCourseData` 메서드의 코드는 보기 모델 클래스를 사용하는 강좌의 목록을 로드하기 위해 모든 강좌 엔터티를 통해 읽습니다. 각 강좌의 경우 코드는 강좌가 강사의 `Courses` 탐색 속성에 있는지 여부를 확인합니다. 강좌가 강사에게 할당되었는지 여부를 확인할 때 효율적인 조회를 만들기 위해 강사에게 할당된 강좌는 `HashSet` 컬렉션에 배치됩니다. `Assigned` 속성은 강사에게 할당된 강좌에 대해 true로 설정됩니다. 보기는 이 속성을 사용하여 선택된 것으로 표시되어야 하는 확인란을 결정합니다. 마지막으로 목록은 `ViewData`의 보기에 전달됩니다.

다음으로 사용자가 **저장** 을 클릭할 때 실행되는 코드를 추가합니다. `EditPost` 메서드를 다음 코드를 바꾸고, 강사 엔터티의 `Courses` 탐색 속성을 업데이트하는 새 메서드를 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

메서드 서명은 이제 HttpGet `Edit` 메서드와 다르므로 메서드 이름은 `EditPost`에서 `Edit`로 다시 변경됩니다.

보기에는 강좌 엔터티의 컬렉션이 없으므로 모델 바인더는 `CourseAssignments` 탐색 속성을 자동으로 업데이트할 수 없습니다. `CourseAssignments` 탐색 속성을 업데이트하는 데 모델 바인더를 사용하는 대신 새 `UpdateInstructorCourses` 메서드에서 해당 작업을 수행합니다. 따라서 모델 바인딩에서 `CourseAssignments` 속성을 제외해야 합니다. 명시적 승인이 필요한 오버로드를 사용 중이고 `CourseAssignments`가 포함 목록에 있지 않으므로 `TryUpdateModel`을 호출하는 코드를 변경할 필요가 없습니다.

확인란이 선택되지 않은 경우 `UpdateInstructorCourses`의 코드는 빈 컬렉션으로 `CourseAssignments` 탐색 속성을 초기화하고 다음을 반환합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

그런 다음, 코드는 데이터베이스의 모든 강좌를 반복하고 현재 강사에게 할당된 것과 보기에서 선택되었던 것에 대해 각 강좌를 확인합니다. 효율적인 조회를 수행하기 위해 후자의 두 컬렉션은 `HashSet` 개체에 저장됩니다.

강좌에 대한 확인란이 선택됐지만 강좌가 `Instructor.CourseAssignments` 탐색 속성에 없는 경우 강좌는 탐색 속성의 컬렉션에 추가됩니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

강좌에 대한 확인란이 선택되지 않았지만 강좌가 `Instructor.CourseAssignments` 탐색 속성에 있는 경우 강좌는 탐색 속성에서 제거됩니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>강사 보기 업데이트

*Views/Instructors/Edit.cshtml* 에서 **사무실** 필드에 대한 `div` 요소 후와 **저장** 단추에 대한 `div` 요소 전에 다음 코드를 즉시 추가하여 확인란의 배열로 **강좌** 필드를 추가합니다.

<a id="notepad"></a>
> [!NOTE]
> Visual Studio에서 코드를 붙여넣을 때 줄 바꿈이 코드를 중단하는 방식으로 변경될 수 있습니다. 붙여넣은 후 코드가 다르게 보이면 Ctrl+Z를 한 번 눌러 자동 서식 지정을 실행 취소합니다. 여기와 같은 모양이 되도록 줄 바꿈을 수정합니다. 들여쓰기는 완벽할 필요가 없지만 `@:</tr><tr>`, `@:<td>`, `@:</td>` 및 `@:</tr>` 줄은 표시된 것처럼 각각 한 줄에 있어야 합니다. 그렇지 않으면 런타임 오류가 발생합니다. 선택된 새 코드의 블록과 함께 Tab 키를 세 번 눌러 기존 코드와 함께 새 코드를 정렬합니다. 이 문제는 Visual Studio 2019에서 해결되었습니다.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

이 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다. 각 열은 강좌 번호 및 제목으로 구성된 캡션이 뒤에 오는 확인란입니다. 확인란은 모두 모델 바인더에게 그룹으로 간주된다는 것을 알리는 동일한 이름("selectedCourses")을 갖습니다. 각 확인란의 값 특성은 `CourseID`의 값으로 설정됩니다. 페이지가 게시되면 모델 바인더는 선택된 확인란에 대한 `CourseID` 값으로 구성된 배열을 컨트롤러에 전달합니다.

확인란이 처음으로 렌더링될 때 강사에게 할당된 강좌에 대한 것은 해당 항목을 선택하는 특성을 선택했습니다(선택된 것으로 표시).

앱을 실행하고, **강사** 탭을 선택하고, 강사에서 **편집** 을 클릭하여 **편집** 페이지를 봅니다.

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

일부 강좌 할당을 변경하고 저장을 클릭합니다. 변경 내용은 인덱스 페이지에 반영됩니다.

> [!NOTE]
> 강사 강좌 데이터를 편집하기 위해 여기에 적용되는 방법은 제한된 수의 강좌가 있는 경우에 잘 작동합니다. 훨씬 큰 컬렉션의 경우 다른 UI 및 다른 업데이트 메서드가 필요합니다.

## <a name="update-delete-page"></a>삭제 페이지 업데이트

*InstructorsController.cs* 에서 `DeleteConfirmed` 메서드를 삭제하고 해당 위치에 다음 코드를 삽입합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

이 코드로 다음이 변경됩니다.

* `CourseAssignments` 탐색 속성에 대해 즉시 로드를 수행합니다. 이를 포함해야 합니다. 그렇지 않으면 EF는 관련된 `CourseAssignment` 엔터티에 대해 알지 못하고 삭제하지 않습니다. 읽을 필요가 없도록 하려면 여기에서 데이터베이스에 계단식 삭제를 구성할 수 있습니다.

* 삭제될 강사가 부서의 관리자로 할당된 경우 해당 부서에서 강사 할당을 제거합니다.

## <a name="add-office-location-and-courses-to-create-page"></a>만들기 페이지에 사무실 위치 및 강좌 추가

*InstructorsController.cs* 에서 HttpGet 및 HttpPost `Create` 메서드를 삭제한 다음, 해당 위치에 다음 코드를 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

이 코드는 처음에 선택된 강좌가 없다는 것을 제외하고 `Edit` 메서드에 대해 확인한 것과 유사합니다. 선택된 강좌가 있을 수 있기 때문이 아니라 보기의 `foreach` 반복에 대해 빈 컬렉션을 제공하기 위해 HttpGet `Create` 메서드는 `PopulateAssignedCourseData` 메서드를 호출합니다(그렇지 않은 경우 보기 코드는 null 참조 예외를 throw함).

HttpPost `Create` 메서드는 유효성 검사 오류를 확인하고 데이터베이스에 새 강사를 추가하기 전에 `CourseAssignments` 탐색 속성에 선택된 각 강좌를 추가합니다. 모델 오류가 있더라도 강좌가 추가되므로 모델 오류가 있는 경우(예: 사용자가 잘못된 날짜를 키 지정함) 페이지는 오류 메시지와 함께 다시 표시되고, 선택한 모든 강좌는 자동으로 다시 저장됩니다.

`CourseAssignments` 탐색 속성에 강좌를 추가할 수 있도록 빈 컬렉션으로 속성을 초기화해야 합니다.

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

컨트롤러 코드에서 이 작업을 수행하는 대안으로 다음 예제와 같이 존재하지 않는 경우 자동으로 컬렉션을 만들도록 getter 속성을 변경하여 강사 모델에서 해당 작업을 수행할 수 있습니다.

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

이러한 방식으로 `CourseAssignments` 속성을 수정하는 경우 컨트롤러에서 명시적 속성 초기화 코드를 제거할 수 있습니다.

*Views/Instructor/Create.cshtml* 에서 사무실 위치 텍스트 상자와 제출 단추 전에 강좌에 대한 확인란을 추가합니다. 편집 페이지의 경우와 같이 [Visual Studio에서 붙여넣을 때 코드의 서식을 다시 지정하는 경우 서식 지정을 수정합니다](#notepad).

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

앱을 실행하고 강사를 만들어 테스트합니다.

## <a name="handling-transactions"></a>트랜잭션 처리

[CRUD 자습서](crud.md)에 설명된 대로 Entity Framework는 트랜잭션을 암시적으로 구현합니다. 더 많은 컨트롤이 필요한 시나리오의 경우, 예를 들어 트랜잭션의 Entity Framework 밖에서 수행한 작업을 포함하려는 경우 [트랜잭션](/ef/core/saving/transactions)을 참조하세요.

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 강좌 페이지 사용자 지정
> * 강사 편집 페이지 추가
> * 편집 페이지에 강좌 추가
> * 삭제 페이지 업데이트
> * 만들기 페이지에 사무실 위치 및 강좌 추가

동시성 충돌을 처리하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [동시성 충돌 처리](concurrency.md)
