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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060536"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="f5244-103">7부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 관련 데이터 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="f5244-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5244-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5244-105">이 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="f5244-106">다음 그림은 완료된 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="f5244-107">![강좌 편집 페이지](update-related-data/_static/course-edit30.png)
![강사 편집 페이지](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="f5244-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="f5244-108">과정 만들기 및 편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="f5244-109">과정 만들기 및 편집 페이지의 스캐폴드된 코드에는 부서 ID(정수)를 표시하는 부서 드롭다운 목록이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="f5244-110">드롭다운에는 부서 이름이 표시되므로 두 페이지에 모두 부서 이름 목록이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="f5244-111">이 목록을 제공하려면 만들기 및 편집 페이지에 기본 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="f5244-112">과정 만들기 및 편집에 대한 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="f5244-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="f5244-113">다음 코드로 *Pages/Courses/DepartmentNamePageModel.cs* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="f5244-114">위의 코드에서는 부서 이름의 목록을 포함하도록 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="f5244-115">`selectedDepartment`가 지정된 경우 해당 부서는 `SelectList`에서 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="f5244-116">만들기 및 편집 페이지 모델 클래스는 `DepartmentNamePageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="f5244-117">과정 만들기 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-117">Update the Course Create page model</span></span>

<span data-ttu-id="f5244-118">과정이 부서에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="f5244-119">만들기 및 편집 페이지의 기본 클래스는 부서를 선택하기 위한 `SelectList`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="f5244-120">`SelectList`를 사용하는 드롭다운 목록은 `Course.DepartmentID` FK(외래 키) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="f5244-121">EF Core는 `Course.DepartmentID` FK를 사용하여 `Department` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![강좌 만들기](update-related-data/_static/ddl30.png)

<span data-ttu-id="f5244-123">다음 코드로 *Pages/Courses/Create.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f5244-124">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f5244-124">The preceding code:</span></span>

* <span data-ttu-id="f5244-125">`DepartmentNamePageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="f5244-126">[초과 게시](xref:data/ef-rp/crud#overposting)를 방지하도록 `TryUpdateModelAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5244-127">`ViewData["DepartmentID"]`을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="f5244-128">기본 클래스의 `DepartmentNameSL`은 강력한 형식의 모델이며 Razor 페이지에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="f5244-129">강력한 형식의 모델은 약한 형식보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="f5244-130">자세한 내용은 [약한 형식의 데이터(ViewData 및 ViewBag)](xref:mvc/views/overview#VD_VB)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5244-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-no-locrazor-page"></a><span data-ttu-id="f5244-131">과정 만들기 Razor 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="f5244-132">다음 코드로 *Pages/Courses/Create.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="f5244-133">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5244-134">캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5244-135">`"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="f5244-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="f5244-136">"부서 선택" 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="f5244-137">이렇게 변경하면 아직 부서가 선택되지 않은 경우 첫 번째 부서가 아닌 “부서 선택”이 드롭다운에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="f5244-138">부서가 선택되지 않은 경우 유효성 검사 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="f5244-139">Razor 페이지는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="f5244-140">만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-140">Test the Create page.</span></span> <span data-ttu-id="f5244-141">만들기 페이지는 부서 ID보다는 부서 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="f5244-142">과정 편집 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-142">Update the Course Edit page model</span></span>

<span data-ttu-id="f5244-143">다음 코드로 *Pages/Courses/Edit.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="f5244-144">변경 내용은 만들기 페이지 모델에서 만든 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="f5244-145">앞의 코드에서 `PopulateDepartmentsDropDownList`는 드롭다운 목록에서 부서를 선택하는 부서 ID를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-no-locrazor-page"></a><span data-ttu-id="f5244-146">과정 편집 Razor 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="f5244-147">다음 코드로 *Pages/Courses/Edit.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="f5244-148">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5244-149">강좌 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-149">Displays the course ID.</span></span> <span data-ttu-id="f5244-150">일반적으로 엔터티의 PK(기본 키)는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="f5244-151">PK는 일반적으로 사용자에게 아무런 의미가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="f5244-152">이 경우 PK는 강좌 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="f5244-153">부서 드롭다운의 캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5244-154">`"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="f5244-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="f5244-155">페이지는 강좌 번호에 대한 숨겨진 필드(`<input type="hidden">`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="f5244-156">`asp-for="Course.CourseID"`로 `<label>` 태그 도우미를 추가하는 것은 숨겨진 필드에 대한 필요성을 제거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="f5244-157">`<input type="hidden">`은 사용자가 **저장** 을 클릭할 때 게시된 데이터에 포함되도록 강좌 번호에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="f5244-158">과정 세부 정보 및 삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="f5244-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__)은 추적이 필요하지 않은 경우 성능을 향상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="f5244-160">과정 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-160">Update the Course page models</span></span>

<span data-ttu-id="f5244-161">다음 코드로 *Pages/Courses/Delete.cshtml.cs* 를 업데이트하여 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="f5244-162">*Pages/Courses/Details.cshtml.cs* 파일에서 동일하게 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a><span data-ttu-id="f5244-163">과정 Razor Pages 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-163">Update the Course Razor pages</span></span>

<span data-ttu-id="f5244-164">다음 코드로 *Pages/Courses/Delete.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="f5244-165">세부 정보 페이지에 동일한 변경 내용을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="f5244-166">강좌 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="f5244-166">Test the Course pages</span></span>

<span data-ttu-id="f5244-167">만들기, 편집, 세부 정보 및 삭제 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="f5244-168">강사 만들기 및 편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="f5244-169">강사는 강좌 수에 관계 없이 가르칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="f5244-170">다음 이미지는 과정 확인란 배열이 포함된 강사 편집 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="f5244-172">확인란을 선택하면 강사에게 할당된 과정에 변경 내용이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="f5244-173">데이터베이스의 모든 과정에 해당하는 확인란이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="f5244-174">강사에게 할당된 과정이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="f5244-175">사용자는 확인란을 선택하거나 선택 취소하여 과정 할당을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="f5244-176">과정 수가 훨씬 더 많은 경우 다른 UI가 더 효과적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="f5244-177">그러나 여기에 표시된 다대다 관계를 관리하는 메서드는 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="f5244-178">관계를 만들거나 삭제하려면 조인 엔터티를 조작합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="f5244-179">할당된 과정 데이터에 대한 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="f5244-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="f5244-180">다음 코드로 *SchoolViewModels/AssignedCourseData.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="f5244-181">`AssignedCourseData` 클래스는 강사에게 할당된 과정에 대한 확인란을 만들 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="f5244-182">강사 페이지 모델 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="f5244-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="f5244-183">*Pages/Instructors/InstructorCoursesPageModel.cs* 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="f5244-184">`InstructorCoursesPageModel`은 편집 및 만들기 페이지 모델에 사용하는 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="f5244-185">`PopulateAssignedCourseData`는 `AssignedCourseDataList`를 채우도록 모든 `Course` 엔터티를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="f5244-186">각 강좌의 경우 코드는 `CourseID`, 제목 및 강사가 강좌에 할당되었는지 여부를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="f5244-187">[HashSet](/dotnet/api/system.collections.generic.hashset-1)는 효율적인 조회를 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="f5244-188">Razor 페이지에는 과정 엔터티의 컬렉션이 없으므로 모델 바인더는 `CourseAssignments` 탐색 속성을 자동으로 업데이트할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5244-189">`CourseAssignments` 탐색 속성을 업데이트하는 데 모델 바인더를 사용하는 대신 새 `UpdateInstructorCourses` 메서드에서 해당 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="f5244-190">따라서 모델 바인딩에서 `CourseAssignments` 속성을 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="f5244-191">선언된 속성을 포함하는 오버로드를 사용 중이고 `CourseAssignments`가 포함 목록에 있지 않으므로 `TryUpdateModel`을 호출하는 코드를 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="f5244-192">확인란이 선택되지 않은 경우 `UpdateInstructorCourses`의 코드는 빈 컬렉션으로 `CourseAssignments` 탐색 속성을 초기화하고 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="f5244-193">그런 다음, 코드는 데이터베이스의 모든 과정을 반복하고 현재 강사에게 할당된 것과 페이지에서 선택되었던 것에 대해 각 과정을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="f5244-194">효율적인 조회를 수행하기 위해 후자의 두 컬렉션은 `HashSet` 개체에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="f5244-195">강좌에 대한 확인란이 선택됐지만 강좌가 `Instructor.CourseAssignments` 탐색 속성에 없는 경우 강좌는 탐색 속성의 컬렉션에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="f5244-196">강좌에 대한 확인란이 선택되지 않았지만 강좌가 `Instructor.CourseAssignments` 탐색 속성에 있는 경우 강좌는 탐색 속성에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="f5244-197">사무실 위치 처리</span><span class="sxs-lookup"><span data-stu-id="f5244-197">Handle office location</span></span>

<span data-ttu-id="f5244-198">편집 페이지에서 처리해야 하는 또 다른 관계는 강사 엔터티와 `OfficeAssignment` 엔터티 간의 일대영/일 관계입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="f5244-199">강사 편집 코드는 다음 시나리오를 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="f5244-200">사용자가 사무실 할당을 해제하는 경우 `OfficeAssignment` 엔터티를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5244-201">사용자가 사무실 할당을 입력하고 비어 있던 경우 새 `OfficeAssignment` 엔터티를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5244-202">사용자가 사무실 할당을 변경하는 경우 `OfficeAssignment` 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="f5244-203">강사 편집 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="f5244-204">다음 코드로 *Pages/Instructors/Edit.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="f5244-205">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f5244-205">The preceding code:</span></span>

* <span data-ttu-id="f5244-206">`OfficeAssignment`, `CourseAssignment` 및 `CourseAssignment.Course` 탐색 속성에 대한 즉시 로드를 사용하여 데이터베이스에서 현재 `Instructor` 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="f5244-207">모델 바인더의 값으로 검색된 `Instructor` 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="f5244-208">`TryUpdateModel`은 [초과 게시](xref:data/ef-rp/crud#overposting)를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5244-209">사무실 위치가 비어 있는 경우 `Instructor.OfficeAssignment`를 Null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="f5244-210">`Instructor.OfficeAssignment`가 Null인 경우 `OfficeAssignment` 테이블의 관련된 행이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="f5244-211">`OnGetAsync`에서 `PopulateAssignedCourseData`를 호출하여 `AssignedCourseData` 뷰 모델 클래스를 통해 확인란에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="f5244-212">`OnPostAsync`에서 `UpdateInstructorCourses`를 호출하여 확인란의 정보를 편집 중인 Instructor 엔터티에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="f5244-213">`TryUpdateModel`이 실패하는 경우 `OnPostAsync`에서 `PopulateAssignedCourseData` 및 `UpdateInstructorCourses`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="f5244-214">이 메서드 호출은 오류 메시지와 함께 다시 표시될 때 페이지에 입력된 할당된 과정 데이터를 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-no-locrazor-page"></a><span data-ttu-id="f5244-215">강사 편집 Razor 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="f5244-216">다음 코드로 *Pages/Instructors/Edit.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="f5244-217">위의 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="f5244-218">각 열에는 확인란과 과정 번호 및 제목을 포함하는 캡션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="f5244-219">모든 확인란의 이름은 동일합니다("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="f5244-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="f5244-220">동일한 이름을 사용하면 모델 바인더에 그룹으로 취급하도록 알립니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="f5244-221">각 확인란의 값 특성은 `CourseID`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="f5244-222">페이지가 게시되면 모델 바인더는 선택된 확인란만의 `CourseID` 값으로 구성된 배열을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="f5244-223">확인란이 처음 렌더링되는 경우 강사에게 할당된 과정이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="f5244-224">참고: 강사 강좌 데이터를 편집하기 위해 여기에 적용되는 방법은 제한된 수의 강좌가 있는 경우에 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="f5244-225">훨씬 큰 컬렉션의 경우 다른 UI 및 다른 업데이트 메서드는 더욱 사용 가능하며 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="f5244-226">앱을 실행하고 업데이트된 강사 편집 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="f5244-227">일부 강좌 할당을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-227">Change some course assignments.</span></span> <span data-ttu-id="f5244-228">변경 내용은 인덱스 페이지에 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="f5244-229">강사 만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-229">Update the Instructor Create page</span></span>

<span data-ttu-id="f5244-230">편집 페이지와 유사한 코드를 사용하여 강사 만들기 페이지 모델 및 Razor 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="f5244-231">강사 만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="f5244-232">강사 삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="f5244-233">다음 코드로 *Pages/Instructors/Delete.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="f5244-234">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5244-235">`CourseAssignments` 탐색 속성에 대해 즉시 로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5244-236">`CourseAssignments`는 포함되어야 합니다. 또는 강사가 삭제될 때 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="f5244-237">읽을 필요가 없도록 하려면 데이터베이스에 계단식 삭제를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="f5244-238">삭제될 강사가 부서의 관리자로 할당된 경우 해당 부서에서 강사 할당을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="f5244-239">앱을 실행하고 삭제 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f5244-240">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f5244-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f5244-241">[이전 자습서](xref:data/ef-rp/read-related-data)
> [다음 자습서](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="f5244-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5244-242">이 자습서에서는 관련된 데이터 업데이트를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="f5244-243">해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f5244-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="f5244-244">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f5244-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f5244-245">다음 그림은 완료된 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="f5244-246">![강좌 편집 페이지](update-related-data/_static/course-edit.png)
![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="f5244-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="f5244-247">만들기 및 편집 강좌 페이지를 검사하고 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="f5244-248">새 강좌를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-248">Create a new course.</span></span> <span data-ttu-id="f5244-249">부서는 해당 이름이 아닌 해당 기본 키(정수)로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="f5244-250">새 강좌를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-250">Edit the new course.</span></span> <span data-ttu-id="f5244-251">테스트를 완료하면 새 강좌를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="f5244-252">공통 코드를 공유하는 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="f5244-252">Create a base class to share common code</span></span>

<span data-ttu-id="f5244-253">강좌/만들기 및 강좌/페이지 편집 각각은 부서 이름의 목록이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="f5244-254">만들기 및 편집 페이지에 대해 *Pages/Courses/DepartmentNamePageModel.cshtml.cs* 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="f5244-255">위의 코드에서는 부서 이름의 목록을 포함하도록 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="f5244-256">`selectedDepartment`가 지정된 경우 해당 부서는 `SelectList`에서 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="f5244-257">만들기 및 편집 페이지 모델 클래스는 `DepartmentNamePageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="f5244-258">강좌 페이지 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="f5244-258">Customize the Courses Pages</span></span>

<span data-ttu-id="f5244-259">새 강좌 엔터티가 만들어질 때 기존 부서에 대한 관계가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="f5244-260">강좌를 만드는 동안 부서를 추가하기 위해 만들기 및 편집에 대한 기본 클래스는 부서를 선택하기 위한 드롭다운 목록을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="f5244-261">드롭다운 목록은 `Course.DepartmentID` FK(외래 키) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="f5244-262">EF Core는 `Course.DepartmentID` FK를 사용하여 `Department` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![강좌 만들기](update-related-data/_static/ddl.png)

<span data-ttu-id="f5244-264">다음 코드로 만들기 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="f5244-265">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f5244-265">The preceding code:</span></span>

* <span data-ttu-id="f5244-266">`DepartmentNamePageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="f5244-267">[초과 게시](xref:data/ef-rp/crud#overposting)를 방지하도록 `TryUpdateModelAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5244-268">`ViewData["DepartmentID"]`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="f5244-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="f5244-269">`ViewData["DepartmentID"]`는 강력한 형식의 `DepartmentNameSL`로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="f5244-270">강력한 형식의 모델은 약한 형식보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="f5244-271">자세한 내용은 [약한 형식의 데이터(ViewData 및 ViewBag)](xref:mvc/views/overview#VD_VB)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5244-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="f5244-272">강좌 만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-272">Update the Courses Create page</span></span>

<span data-ttu-id="f5244-273">다음 코드로 *Pages/Courses/Create.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="f5244-274">위의 표시는 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="f5244-275">캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5244-276">`"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="f5244-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="f5244-277">"부서 선택" 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="f5244-278">이 변경 내용은 첫 번째 부서 대신 "부서 선택"을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="f5244-279">부서가 선택되지 않은 경우 유효성 검사 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="f5244-280">Razor 페이지는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="f5244-281">만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-281">Test the Create page.</span></span> <span data-ttu-id="f5244-282">만들기 페이지는 부서 ID보다는 부서 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="f5244-283">강좌 만들기 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="f5244-284">*Pages/Courses/Edit.cshtml.cs* 의 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="f5244-285">변경 내용은 만들기 페이지 모델에서 만든 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="f5244-286">위의 코드에서 `PopulateDepartmentsDropDownList`는 드롭다운 목록에 지정된 부서를 선택하는 부서 ID를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="f5244-287">다음 표시로 *Pages/Courses/Edit.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="f5244-288">위의 표시는 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="f5244-289">강좌 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-289">Displays the course ID.</span></span> <span data-ttu-id="f5244-290">일반적으로 엔터티의 PK(기본 키)는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="f5244-291">PK는 일반적으로 사용자에게 아무런 의미가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="f5244-292">이 경우 PK는 강좌 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="f5244-293">캡션을 **DepartmentID** 에서 **Department** 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5244-294">`"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="f5244-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="f5244-295">페이지는 강좌 번호에 대한 숨겨진 필드(`<input type="hidden">`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="f5244-296">`asp-for="Course.CourseID"`로 `<label>` 태그 도우미를 추가하는 것은 숨겨진 필드에 대한 필요성을 제거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="f5244-297">`<input type="hidden">`은 사용자가 **저장** 을 클릭할 때 게시된 데이터에 포함되도록 강좌 번호에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="f5244-298">업데이트된 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-298">Test the updated code.</span></span> <span data-ttu-id="f5244-299">강좌를 만들고, 편집하고, 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="f5244-300">세부 정보 및 삭제 페이지 모델에 AsNoTracking 추가</span><span class="sxs-lookup"><span data-stu-id="f5244-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="f5244-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__)은 추적이 필요하지 않은 경우 성능을 향상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="f5244-302">삭제 및 세부 정보 페이지 모델에 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="f5244-303">다음 코드에서는 업데이트된 삭제 페이지 모델을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="f5244-304">*Pages/Courses/Details.cshtml.cs* 파일에서 `OnGetAsync` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="f5244-305">삭제 및 세부 정보 페이지 수정</span><span class="sxs-lookup"><span data-stu-id="f5244-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="f5244-306">다음 표시로 삭제 Razor 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="f5244-307">세부 정보 페이지에 동일한 변경 내용을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="f5244-308">강좌 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="f5244-308">Test the Course pages</span></span>

<span data-ttu-id="f5244-309">만들기, 편집, 세부 정보 및 삭제를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="f5244-310">강사 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-310">Update the instructor pages</span></span>

<span data-ttu-id="f5244-311">다음 섹션에서는 강사 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="f5244-312">사무실 위치 추가</span><span class="sxs-lookup"><span data-stu-id="f5244-312">Add office location</span></span>

<span data-ttu-id="f5244-313">강사 레코드를 편집할 때 강사의 사무실 할당을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="f5244-314">`Instructor` 엔터티에는 `OfficeAssignment` 엔터티와 일대영 또는 일 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="f5244-315">강사 코드를 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-315">The instructor code must handle:</span></span>

* <span data-ttu-id="f5244-316">사용자가 사무실 할당을 해제하는 경우 `OfficeAssignment` 엔터티를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5244-317">사용자가 사무실 할당을 입력하고 비어 있던 경우 새 `OfficeAssignment` 엔터티를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5244-318">사용자가 사무실 할당을 변경하는 경우 `OfficeAssignment` 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="f5244-319">다음 코드로 강사 편집 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="f5244-320">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="f5244-320">The preceding code:</span></span>

* <span data-ttu-id="f5244-321">`OfficeAssignment` 탐색 속성에 대한 즉시 로드를 사용하여 데이터베이스에서 현재 `Instructor` 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="f5244-322">모델 바인더의 값으로 검색된 `Instructor` 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="f5244-323">`TryUpdateModel`은 [초과 게시](xref:data/ef-rp/crud#overposting)를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5244-324">사무실 위치가 비어 있는 경우 `Instructor.OfficeAssignment`를 Null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="f5244-325">`Instructor.OfficeAssignment`가 Null인 경우 `OfficeAssignment` 테이블의 관련된 행이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="f5244-326">강사 편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-326">Update the instructor Edit page</span></span>

<span data-ttu-id="f5244-327">*Pages/Instructors/Edit.cshtml* 을 사무실 위치로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="f5244-328">강사 사무실 위치를 변경할 수 있음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="f5244-329">강사 편집 페이지에 강좌 할당 추가</span><span class="sxs-lookup"><span data-stu-id="f5244-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="f5244-330">강사는 강좌 수에 관계 없이 가르칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="f5244-331">이 섹션에서는 강좌 할당을 변경하는 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="f5244-332">다음 그림에서는 업데이트된 강사 편집 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-332">The following image shows the updated instructor Edit page:</span></span>

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f5244-334">`Course` 및 `Instructor`에는 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="f5244-335">관계를 추가하고 제거하려면 `CourseAssignments` 조인 엔터티 집합에서 엔터티를 추가하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="f5244-336">확인란은 강사에게 할당된 강좌에 대한 변경 내용을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="f5244-337">데이터베이스의 모든 강좌에 대해 확인란이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="f5244-338">강사에게 할당된 강좌가 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="f5244-339">사용자는 확인란을 선택하거나 선택 취소하여 강좌 할당을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="f5244-340">강좌의 번호가 훨씬 더 큰 경우:</span><span class="sxs-lookup"><span data-stu-id="f5244-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="f5244-341">아마도 강좌를 표시하는 데 다른 사용자 인터페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="f5244-342">관계를 만들거나 삭제하는 조인 엔터티를 조작하는 방법은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="f5244-343">만들기 및 편집 강사 페이지를 지원하는 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="f5244-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="f5244-344">다음 코드로 *SchoolViewModels/AssignedCourseData.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="f5244-345">`AssignedCourseData` 클래스는 강사별 할당된 강좌에 대한 확인란을 만드는 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="f5244-346">*Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="f5244-347">`InstructorCoursesPageModel`은 편집 및 만들기 페이지 모델에 사용하는 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="f5244-348">`PopulateAssignedCourseData`는 `AssignedCourseDataList`를 채우도록 모든 `Course` 엔터티를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="f5244-349">각 강좌의 경우 코드는 `CourseID`, 제목 및 강사가 강좌에 할당되었는지 여부를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="f5244-350">[HashSet](/dotnet/api/system.collections.generic.hashset-1)는 효율적인 조회를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="f5244-351">강사 편집 페이지 모델</span><span class="sxs-lookup"><span data-stu-id="f5244-351">Instructors Edit page model</span></span>

<span data-ttu-id="f5244-352">다음 코드로 강사 편집 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="f5244-353">위의 코드는 사무실 할당 변경을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="f5244-354">강사 Razor 뷰를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="f5244-355">Visual Studio에서 코드를 붙여 넣을 때 줄 바꿈이 코드를 중단하는 방식으로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="f5244-356">자동 서식 지정을 실행 취소하려면 Ctrl+Z를 한 번 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="f5244-357">Ctrl+Z는 여기와 같은 모양이 되도록 줄 바꿈을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="f5244-358">들여쓰기는 완벽할 필요가 없지만 `@:</tr><tr>`, `@:<td>`, `@:</td>` 및 `@:</tr>` 줄은 표시된 것처럼 각각 한 줄에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="f5244-359">선택된 새 코드의 블록과 함께 Tab 키를 세 번 눌러 기존 코드와 함께 새 코드를 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="f5244-360">[이 링크를 통해](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html) 이 버그의 상태를 투표하거나 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="f5244-361">위의 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="f5244-362">각 열에는 확인란과 강좌 번호 및 제목을 포함하는 캡션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="f5244-363">모든 확인란에는 동일한 이름("selectedCourses")이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="f5244-364">동일한 이름을 사용하면 모델 바인더에 그룹으로 취급하도록 알립니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="f5244-365">각 확인란의 값 특성은 `CourseID`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="f5244-366">페이지가 게시되면 모델 바인더는 선택된 확인란에 대한 `CourseID` 값으로 구성된 배열을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="f5244-367">확인란이 처음 렌더링되는 경우 강사에게 할당된 강좌는 특성을 확인했습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="f5244-368">앱을 실행하고 업데이트된 강사 편집 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="f5244-369">일부 강좌 할당을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-369">Change some course assignments.</span></span> <span data-ttu-id="f5244-370">변경 내용은 인덱스 페이지에 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="f5244-371">참고: 강사 강좌 데이터를 편집하기 위해 여기에 적용되는 방법은 제한된 수의 강좌가 있는 경우에 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="f5244-372">훨씬 큰 컬렉션의 경우 다른 UI 및 다른 업데이트 메서드는 더욱 사용 가능하며 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="f5244-373">강사 만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-373">Update the instructors Create page</span></span>

<span data-ttu-id="f5244-374">다음 코드로 강사 만들기 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="f5244-375">위의 코드는 *Pages/Instructors/Edit.cshtml.cs* 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="f5244-376">다음 변경 내용으로 강사 만들기 Razor 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="f5244-377">강사 만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="f5244-378">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="f5244-378">Update the Delete page</span></span>

<span data-ttu-id="f5244-379">다음 코드로 삭제 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="f5244-380">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5244-381">`CourseAssignments` 탐색 속성에 대해 즉시 로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5244-382">`CourseAssignments`는 포함되어야 합니다. 또는 강사가 삭제될 때 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="f5244-383">읽을 필요가 없도록 하려면 데이터베이스에 계단식 삭제를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="f5244-384">삭제될 강사가 부서의 관리자로 할당된 경우 해당 부서에서 강사 할당을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5244-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5244-385">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f5244-385">Additional resources</span></span>

* [<span data-ttu-id="f5244-386">이 자습서의 YouTube 버전(1부)</span><span class="sxs-lookup"><span data-stu-id="f5244-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="f5244-387">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="f5244-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="f5244-388">[이전](xref:data/ef-rp/read-related-data)
> [다음](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="f5244-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
