---
title: 7부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 관련 데이터 업데이트
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 7부입니다.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3ec88a862697c540a1a98e733c31d76922f81f7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060536"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a>7부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 관련 데이터 업데이트

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

이 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다. 다음 그림은 완료된 페이지의 일부를 보여 줍니다.

![강좌 편집 페이지](update-related-data/_static/course-edit30.png)
![강사 편집 페이지](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>과정 만들기 및 편집 페이지 업데이트

과정 만들기 및 편집 페이지의 스캐폴드된 코드에는 부서 ID(정수)를 표시하는 부서 드롭다운 목록이 있습니다. 드롭다운에는 부서 이름이 표시되므로 두 페이지에 모두 부서 이름 목록이 필요합니다. 이 목록을 제공하려면 만들기 및 편집 페이지에 기본 클래스를 사용합니다.

### <a name="create-a-base-class-for-course-create-and-edit"></a>과정 만들기 및 편집에 대한 기본 클래스 만들기

다음 코드로 *Pages/Courses/DepartmentNamePageModel.cs* 파일을 만듭니다.

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

위의 코드에서는 부서 이름의 목록을 포함하도록 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist)를 만듭니다. `selectedDepartment`가 지정된 경우 해당 부서는 `SelectList`에서 선택됩니다.

만들기 및 편집 페이지 모델 클래스는 `DepartmentNamePageModel`에서 파생됩니다.

### <a name="update-the-course-create-page-model"></a>과정 만들기 페이지 모델 업데이트

과정이 부서에 할당됩니다. 만들기 및 편집 페이지의 기본 클래스는 부서를 선택하기 위한 `SelectList`를 제공합니다. `SelectList`를 사용하는 드롭다운 목록은 `Course.DepartmentID` FK(외래 키) 속성을 설정합니다. EF Core는 `Course.DepartmentID` FK를 사용하여 `Department` 탐색 속성을 로드합니다.

![강좌 만들기](update-related-data/_static/ddl30.png)

다음 코드로 *Pages/Courses/Create.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

위의 코드는

* `DepartmentNamePageModel`에서 파생됩니다.
* [초과 게시](xref:data/ef-rp/crud#overposting)를 방지하도록 `TryUpdateModelAsync`를 사용합니다.
* `ViewData["DepartmentID"]`을 제거합니다. 기본 클래스의 `DepartmentNameSL`은 강력한 형식의 모델이며 Razor 페이지에서 사용됩니다. 강력한 형식의 모델은 약한 형식보다 선호됩니다. 자세한 내용은 [약한 형식의 데이터(ViewData 및 ViewBag)](xref:mvc/views/overview#VD_VB)를 참조하세요.

### <a name="update-the-course-create-no-locrazor-page"></a>과정 만들기 Razor 페이지 업데이트

다음 코드로 *Pages/Courses/Create.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

위의 코드로 다음이 변경됩니다.

* 캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.
* `"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).
* "부서 선택" 옵션을 추가합니다. 이렇게 변경하면 아직 부서가 선택되지 않은 경우 첫 번째 부서가 아닌 “부서 선택”이 드롭다운에 렌더링됩니다.
* 부서가 선택되지 않은 경우 유효성 검사 메시지를 추가합니다.

Razor 페이지는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)를 사용합니다.

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

만들기 페이지를 테스트합니다. 만들기 페이지는 부서 ID보다는 부서 이름을 표시합니다.

### <a name="update-the-course-edit-page-model"></a>과정 편집 페이지 모델 업데이트

다음 코드로 *Pages/Courses/Edit.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

변경 내용은 만들기 페이지 모델에서 만든 것과 비슷합니다. 앞의 코드에서 `PopulateDepartmentsDropDownList`는 드롭다운 목록에서 부서를 선택하는 부서 ID를 전달합니다.

### <a name="update-the-course-edit-no-locrazor-page"></a>과정 편집 Razor 페이지 업데이트

다음 코드로 *Pages/Courses/Edit.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

위의 코드로 다음이 변경됩니다.

* 강좌 ID를 표시합니다. 일반적으로 엔터티의 PK(기본 키)는 표시되지 않습니다. PK는 일반적으로 사용자에게 아무런 의미가 없습니다. 이 경우 PK는 강좌 번호입니다.
* 부서 드롭다운의 캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.
* `"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).

페이지는 강좌 번호에 대한 숨겨진 필드(`<input type="hidden">`)를 포함합니다. `asp-for="Course.CourseID"`로 `<label>` 태그 도우미를 추가하는 것은 숨겨진 필드에 대한 필요성을 제거하지 않습니다. `<input type="hidden">`은 사용자가 **저장** 을 클릭할 때 게시된 데이터에 포함되도록 강좌 번호에 필요합니다.

## <a name="update-the-course-details-and-delete-pages"></a>과정 세부 정보 및 삭제 페이지 업데이트

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__)은 추적이 필요하지 않은 경우 성능을 향상시킬 수 있습니다.

### <a name="update-the-course-page-models"></a>과정 페이지 모델 업데이트

다음 코드로 *Pages/Courses/Delete.cshtml.cs* 를 업데이트하여 `AsNoTracking`을 추가합니다.

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

*Pages/Courses/Details.cshtml.cs* 파일에서 동일하게 변경합니다.

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a>과정 Razor Pages 업데이트

다음 코드로 *Pages/Courses/Delete.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

세부 정보 페이지에 동일한 변경 내용을 만듭니다.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>강좌 페이지 테스트

만들기, 편집, 세부 정보 및 삭제 페이지를 테스트합니다.

## <a name="update-the-instructor-create-and-edit-pages"></a>강사 만들기 및 편집 페이지 업데이트

강사는 강좌 수에 관계 없이 가르칠 수 있습니다. 다음 이미지는 과정 확인란 배열이 포함된 강사 편집 페이지를 보여 줍니다.

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses30.png)

확인란을 선택하면 강사에게 할당된 과정에 변경 내용이 적용됩니다. 데이터베이스의 모든 과정에 해당하는 확인란이 표시됩니다. 강사에게 할당된 과정이 선택됩니다. 사용자는 확인란을 선택하거나 선택 취소하여 과정 할당을 변경할 수 있습니다. 과정 수가 훨씬 더 많은 경우 다른 UI가 더 효과적일 수 있습니다. 그러나 여기에 표시된 다대다 관계를 관리하는 메서드는 변경되지 않습니다. 관계를 만들거나 삭제하려면 조인 엔터티를 조작합니다.

### <a name="create-a-class-for-assigned-courses-data"></a>할당된 과정 데이터에 대한 클래스 만들기

다음 코드로 *SchoolViewModels/AssignedCourseData.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData` 클래스는 강사에게 할당된 과정에 대한 확인란을 만들 데이터를 포함합니다.

### <a name="create-an-instructor-page-model-base-class"></a>강사 페이지 모델 기본 클래스 만들기

*Pages/Instructors/InstructorCoursesPageModel.cs* 기본 클래스를 만듭니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

`InstructorCoursesPageModel`은 편집 및 만들기 페이지 모델에 사용하는 기본 클래스입니다. `PopulateAssignedCourseData`는 `AssignedCourseDataList`를 채우도록 모든 `Course` 엔터티를 읽습니다. 각 강좌의 경우 코드는 `CourseID`, 제목 및 강사가 강좌에 할당되었는지 여부를 설정합니다. [HashSet](/dotnet/api/system.collections.generic.hashset-1)는 효율적인 조회를 위해 사용됩니다.

Razor 페이지에는 과정 엔터티의 컬렉션이 없으므로 모델 바인더는 `CourseAssignments` 탐색 속성을 자동으로 업데이트할 수 없습니다. `CourseAssignments` 탐색 속성을 업데이트하는 데 모델 바인더를 사용하는 대신 새 `UpdateInstructorCourses` 메서드에서 해당 작업을 수행합니다. 따라서 모델 바인딩에서 `CourseAssignments` 속성을 제외해야 합니다. 선언된 속성을 포함하는 오버로드를 사용 중이고 `CourseAssignments`가 포함 목록에 있지 않으므로 `TryUpdateModel`을 호출하는 코드를 변경할 필요가 없습니다.

확인란이 선택되지 않은 경우 `UpdateInstructorCourses`의 코드는 빈 컬렉션으로 `CourseAssignments` 탐색 속성을 초기화하고 다음을 반환합니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

그런 다음, 코드는 데이터베이스의 모든 과정을 반복하고 현재 강사에게 할당된 것과 페이지에서 선택되었던 것에 대해 각 과정을 확인합니다. 효율적인 조회를 수행하기 위해 후자의 두 컬렉션은 `HashSet` 개체에 저장됩니다.

강좌에 대한 확인란이 선택됐지만 강좌가 `Instructor.CourseAssignments` 탐색 속성에 없는 경우 강좌는 탐색 속성의 컬렉션에 추가됩니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

강좌에 대한 확인란이 선택되지 않았지만 강좌가 `Instructor.CourseAssignments` 탐색 속성에 있는 경우 강좌는 탐색 속성에서 제거됩니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>사무실 위치 처리

편집 페이지에서 처리해야 하는 또 다른 관계는 강사 엔터티와 `OfficeAssignment` 엔터티 간의 일대영/일 관계입니다. 강사 편집 코드는 다음 시나리오를 처리해야 합니다. 

* 사용자가 사무실 할당을 해제하는 경우 `OfficeAssignment` 엔터티를 삭제합니다.
* 사용자가 사무실 할당을 입력하고 비어 있던 경우 새 `OfficeAssignment` 엔터티를 만듭니다.
* 사용자가 사무실 할당을 변경하는 경우 `OfficeAssignment` 엔터티를 업데이트합니다.

### <a name="update-the-instructor-edit-page-model"></a>강사 편집 페이지 모델 업데이트

다음 코드로 *Pages/Instructors/Edit.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

위의 코드는

* `OfficeAssignment`, `CourseAssignment` 및 `CourseAssignment.Course` 탐색 속성에 대한 즉시 로드를 사용하여 데이터베이스에서 현재 `Instructor` 엔터티를 가져옵니다.
* 모델 바인더의 값으로 검색된 `Instructor` 엔터티를 업데이트합니다. `TryUpdateModel`은 [초과 게시](xref:data/ef-rp/crud#overposting)를 방지합니다.
* 사무실 위치가 비어 있는 경우 `Instructor.OfficeAssignment`를 Null로 설정합니다. `Instructor.OfficeAssignment`가 Null인 경우 `OfficeAssignment` 테이블의 관련된 행이 삭제됩니다.
* `OnGetAsync`에서 `PopulateAssignedCourseData`를 호출하여 `AssignedCourseData` 뷰 모델 클래스를 통해 확인란에 대한 정보를 제공합니다.
* `OnPostAsync`에서 `UpdateInstructorCourses`를 호출하여 확인란의 정보를 편집 중인 Instructor 엔터티에 적용합니다.
* `TryUpdateModel`이 실패하는 경우 `OnPostAsync`에서 `PopulateAssignedCourseData` 및 `UpdateInstructorCourses`를 호출합니다. 이 메서드 호출은 오류 메시지와 함께 다시 표시될 때 페이지에 입력된 할당된 과정 데이터를 복원합니다.

### <a name="update-the-instructor-edit-no-locrazor-page"></a>강사 편집 Razor 페이지 업데이트

다음 코드로 *Pages/Instructors/Edit.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

위의 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다. 각 열에는 확인란과 과정 번호 및 제목을 포함하는 캡션이 있습니다. 모든 확인란의 이름은 동일합니다("selectedCourses"). 동일한 이름을 사용하면 모델 바인더에 그룹으로 취급하도록 알립니다. 각 확인란의 값 특성은 `CourseID`로 설정됩니다. 페이지가 게시되면 모델 바인더는 선택된 확인란만의 `CourseID` 값으로 구성된 배열을 전달합니다.

확인란이 처음 렌더링되는 경우 강사에게 할당된 과정이 선택됩니다.

참고: 강사 강좌 데이터를 편집하기 위해 여기에 적용되는 방법은 제한된 수의 강좌가 있는 경우에 잘 작동합니다. 훨씬 큰 컬렉션의 경우 다른 UI 및 다른 업데이트 메서드는 더욱 사용 가능하며 효율적입니다.

앱을 실행하고 업데이트된 강사 편집 페이지를 테스트합니다. 일부 강좌 할당을 변경합니다. 변경 내용은 인덱스 페이지에 반영됩니다.

### <a name="update-the-instructor-create-page"></a>강사 만들기 페이지 업데이트

편집 페이지와 유사한 코드를 사용하여 강사 만들기 페이지 모델 및 Razor 페이지를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

강사 만들기 페이지를 테스트합니다.

## <a name="update-the-instructor-delete-page"></a>강사 삭제 페이지 업데이트

다음 코드로 *Pages/Instructors/Delete.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

위의 코드로 다음이 변경됩니다.

* `CourseAssignments` 탐색 속성에 대해 즉시 로드를 사용합니다. `CourseAssignments`는 포함되어야 합니다. 또는 강사가 삭제될 때 삭제되지 않습니다. 읽을 필요가 없도록 하려면 데이터베이스에 계단식 삭제를 구성합니다.

* 삭제될 강사가 부서의 관리자로 할당된 경우 해당 부서에서 강사 할당을 제거합니다.

앱을 실행하고 삭제 페이지를 테스트합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [이전 자습서](xref:data/ef-rp/read-related-data)
> [다음 자습서](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에서는 관련된 데이터 업데이트를 보여 줍니다. 해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

다음 그림은 완료된 페이지의 일부를 보여 줍니다.

![강좌 편집 페이지](update-related-data/_static/course-edit.png)
![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

만들기 및 편집 강좌 페이지를 검사하고 테스트합니다. 새 강좌를 만듭니다. 부서는 해당 이름이 아닌 해당 기본 키(정수)로 선택됩니다. 새 강좌를 편집합니다. 테스트를 완료하면 새 강좌를 삭제합니다.

## <a name="create-a-base-class-to-share-common-code"></a>공통 코드를 공유하는 기본 클래스 만들기

강좌/만들기 및 강좌/페이지 편집 각각은 부서 이름의 목록이 필요합니다. 만들기 및 편집 페이지에 대해 *Pages/Courses/DepartmentNamePageModel.cshtml.cs* 기본 클래스를 만듭니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

위의 코드에서는 부서 이름의 목록을 포함하도록 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist)를 만듭니다. `selectedDepartment`가 지정된 경우 해당 부서는 `SelectList`에서 선택됩니다.

만들기 및 편집 페이지 모델 클래스는 `DepartmentNamePageModel`에서 파생됩니다.

## <a name="customize-the-courses-pages"></a>강좌 페이지 사용자 지정

새 강좌 엔터티가 만들어질 때 기존 부서에 대한 관계가 있어야 합니다. 강좌를 만드는 동안 부서를 추가하기 위해 만들기 및 편집에 대한 기본 클래스는 부서를 선택하기 위한 드롭다운 목록을 포함합니다. 드롭다운 목록은 `Course.DepartmentID` FK(외래 키) 속성을 설정합니다. EF Core는 `Course.DepartmentID` FK를 사용하여 `Department` 탐색 속성을 로드합니다.

![강좌 만들기](update-related-data/_static/ddl.png)

다음 코드로 만들기 페이지 모델을 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

위의 코드는

* `DepartmentNamePageModel`에서 파생됩니다.
* [초과 게시](xref:data/ef-rp/crud#overposting)를 방지하도록 `TryUpdateModelAsync`를 사용합니다.
* `ViewData["DepartmentID"]`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).

`ViewData["DepartmentID"]`는 강력한 형식의 `DepartmentNameSL`로 대체됩니다. 강력한 형식의 모델은 약한 형식보다 선호됩니다. 자세한 내용은 [약한 형식의 데이터(ViewData 및 ViewBag)](xref:mvc/views/overview#VD_VB)를 참조하세요.

### <a name="update-the-courses-create-page"></a>강좌 만들기 페이지 업데이트

다음 코드로 *Pages/Courses/Create.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

위의 표시는 다음과 같이 변경합니다.

* 캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.
* `"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).
* "부서 선택" 옵션을 추가합니다. 이 변경 내용은 첫 번째 부서 대신 "부서 선택"을 렌더링합니다.
* 부서가 선택되지 않은 경우 유효성 검사 메시지를 추가합니다.

Razor 페이지는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)를 사용합니다.

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

만들기 페이지를 테스트합니다. 만들기 페이지는 부서 ID보다는 부서 이름을 표시합니다.

### <a name="update-the-courses-edit-page"></a>강좌 만들기 페이지를 업데이트합니다.

*Pages/Courses/Edit.cshtml.cs* 의 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

변경 내용은 만들기 페이지 모델에서 만든 것과 비슷합니다. 위의 코드에서 `PopulateDepartmentsDropDownList`는 드롭다운 목록에 지정된 부서를 선택하는 부서 ID를 전달합니다.

다음 표시로 *Pages/Courses/Edit.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

위의 표시는 다음과 같이 변경합니다.

* 강좌 ID를 표시합니다. 일반적으로 엔터티의 PK(기본 키)는 표시되지 않습니다. PK는 일반적으로 사용자에게 아무런 의미가 없습니다. 이 경우 PK는 강좌 번호입니다.
* 캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.
* `"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).

페이지는 강좌 번호에 대한 숨겨진 필드(`<input type="hidden">`)를 포함합니다. `asp-for="Course.CourseID"`로 `<label>` 태그 도우미를 추가하는 것은 숨겨진 필드에 대한 필요성을 제거하지 않습니다. `<input type="hidden">`은 사용자가 **저장** 을 클릭할 때 게시된 데이터에 포함되도록 강좌 번호에 필요합니다.

업데이트된 코드를 테스트합니다. 강좌를 만들고, 편집하고, 삭제합니다.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>세부 정보 및 삭제 페이지 모델에 AsNoTracking 추가

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__)은 추적이 필요하지 않은 경우 성능을 향상시킬 수 있습니다. 삭제 및 세부 정보 페이지 모델에 `AsNoTracking`을 추가합니다. 다음 코드에서는 업데이트된 삭제 페이지 모델을 보여 줍니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

*Pages/Courses/Details.cshtml.cs* 파일에서 `OnGetAsync` 메서드를 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>삭제 및 세부 정보 페이지 수정

다음 표시로 삭제 Razor 페이지를 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

세부 정보 페이지에 동일한 변경 내용을 만듭니다.

### <a name="test-the-course-pages"></a>강좌 페이지 테스트

만들기, 편집, 세부 정보 및 삭제를 테스트합니다.

## <a name="update-the-instructor-pages"></a>강사 페이지 업데이트

다음 섹션에서는 강사 페이지를 업데이트합니다.

### <a name="add-office-location"></a>사무실 위치 추가

강사 레코드를 편집할 때 강사의 사무실 할당을 업데이트할 수 있습니다. `Instructor` 엔터티에는 `OfficeAssignment` 엔터티와 일대영 또는 일 관계가 있습니다. 강사 코드를 처리해야 합니다.

* 사용자가 사무실 할당을 해제하는 경우 `OfficeAssignment` 엔터티를 삭제합니다.
* 사용자가 사무실 할당을 입력하고 비어 있던 경우 새 `OfficeAssignment` 엔터티를 만듭니다.
* 사용자가 사무실 할당을 변경하는 경우 `OfficeAssignment` 엔터티를 업데이트합니다.

다음 코드로 강사 편집 페이지 모델을 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

위의 코드는

* `OfficeAssignment` 탐색 속성에 대한 즉시 로드를 사용하여 데이터베이스에서 현재 `Instructor` 엔터티를 가져옵니다.
* 모델 바인더의 값으로 검색된 `Instructor` 엔터티를 업데이트합니다. `TryUpdateModel`은 [초과 게시](xref:data/ef-rp/crud#overposting)를 방지합니다.
* 사무실 위치가 비어 있는 경우 `Instructor.OfficeAssignment`를 Null로 설정합니다. `Instructor.OfficeAssignment`가 Null인 경우 `OfficeAssignment` 테이블의 관련된 행이 삭제됩니다.

### <a name="update-the-instructor-edit-page"></a>강사 편집 페이지 업데이트

*Pages/Instructors/Edit.cshtml* 을 사무실 위치로 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

강사 사무실 위치를 변경할 수 있음을 확인합니다.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>강사 편집 페이지에 강좌 할당 추가

강사는 강좌 수에 관계 없이 가르칠 수 있습니다. 이 섹션에서는 강좌 할당을 변경하는 기능을 추가합니다. 다음 그림에서는 업데이트된 강사 편집 페이지를 보여 줍니다.

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

`Course` 및 `Instructor`에는 다대다 관계가 있습니다. 관계를 추가하고 제거하려면 `CourseAssignments` 조인 엔터티 집합에서 엔터티를 추가하고 제거합니다.

확인란은 강사에게 할당된 강좌에 대한 변경 내용을 활성화합니다. 데이터베이스의 모든 강좌에 대해 확인란이 표시됩니다. 강사에게 할당된 강좌가 확인됩니다. 사용자는 확인란을 선택하거나 선택 취소하여 강좌 할당을 변경할 수 있습니다. 강좌의 번호가 훨씬 더 큰 경우:

* 아마도 강좌를 표시하는 데 다른 사용자 인터페이스를 사용합니다.
* 관계를 만들거나 삭제하는 조인 엔터티를 조작하는 방법은 변경되지 않습니다.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>만들기 및 편집 강사 페이지를 지원하는 클래스 추가

다음 코드로 *SchoolViewModels/AssignedCourseData.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData` 클래스는 강사별 할당된 강좌에 대한 확인란을 만드는 데이터를 포함합니다.

*Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* 기본 클래스를 만듭니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel`은 편집 및 만들기 페이지 모델에 사용하는 기본 클래스입니다. `PopulateAssignedCourseData`는 `AssignedCourseDataList`를 채우도록 모든 `Course` 엔터티를 읽습니다. 각 강좌의 경우 코드는 `CourseID`, 제목 및 강사가 강좌에 할당되었는지 여부를 설정합니다. [HashSet](/dotnet/api/system.collections.generic.hashset-1)는 효율적인 조회를 만드는 데 사용됩니다.

### <a name="instructors-edit-page-model"></a>강사 편집 페이지 모델

다음 코드로 강사 편집 페이지 모델을 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

위의 코드는 사무실 할당 변경을 처리합니다.

강사 Razor 뷰를 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Visual Studio에서 코드를 붙여 넣을 때 줄 바꿈이 코드를 중단하는 방식으로 변경됩니다. 자동 서식 지정을 실행 취소하려면 Ctrl+Z를 한 번 누릅니다. Ctrl+Z는 여기와 같은 모양이 되도록 줄 바꿈을 수정합니다. 들여쓰기는 완벽할 필요가 없지만 `@:</tr><tr>`, `@:<td>`, `@:</td>` 및 `@:</tr>` 줄은 표시된 것처럼 각각 한 줄에 있어야 합니다. 선택된 새 코드의 블록과 함께 Tab 키를 세 번 눌러 기존 코드와 함께 새 코드를 정렬합니다. [이 링크를 통해](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html) 이 버그의 상태를 투표하거나 검토합니다.

위의 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다. 각 열에는 확인란과 강좌 번호 및 제목을 포함하는 캡션이 있습니다. 모든 확인란에는 동일한 이름("selectedCourses")이 있습니다. 동일한 이름을 사용하면 모델 바인더에 그룹으로 취급하도록 알립니다. 각 확인란의 값 특성은 `CourseID`로 설정됩니다. 페이지가 게시되면 모델 바인더는 선택된 확인란에 대한 `CourseID` 값으로 구성된 배열을 전달합니다.

확인란이 처음 렌더링되는 경우 강사에게 할당된 강좌는 특성을 확인했습니다.

앱을 실행하고 업데이트된 강사 편집 페이지를 테스트합니다. 일부 강좌 할당을 변경합니다. 변경 내용은 인덱스 페이지에 반영됩니다.

참고: 강사 강좌 데이터를 편집하기 위해 여기에 적용되는 방법은 제한된 수의 강좌가 있는 경우에 잘 작동합니다. 훨씬 큰 컬렉션의 경우 다른 UI 및 다른 업데이트 메서드는 더욱 사용 가능하며 효율적입니다.

### <a name="update-the-instructors-create-page"></a>강사 만들기 페이지 업데이트

다음 코드로 강사 만들기 페이지 모델을 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

위의 코드는 *Pages/Instructors/Edit.cshtml.cs* 코드와 비슷합니다.

다음 변경 내용으로 강사 만들기 Razor 페이지를 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

강사 만들기 페이지를 테스트합니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

다음 코드로 삭제 페이지 모델을 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

위의 코드로 다음이 변경됩니다.

* `CourseAssignments` 탐색 속성에 대해 즉시 로드를 사용합니다. `CourseAssignments`는 포함되어야 합니다. 또는 강사가 삭제될 때 삭제되지 않습니다. 읽을 필요가 없도록 하려면 데이터베이스에 계단식 삭제를 구성합니다.

* 삭제될 강사가 부서의 관리자로 할당된 경우 해당 부서에서 강사 할당을 제거합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전(1부)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [이 자습서의 YouTube 버전(2부)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [이전](xref:data/ef-rp/read-related-data)
> [다음](xref:data/ef-rp/concurrency)

::: moniker-end
