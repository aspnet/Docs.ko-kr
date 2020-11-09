---
title: '자습서: CRUD 기능 구현 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 MVC 스캐폴딩이 컨트롤러 및 보기에서 자동으로 만드는 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토하고 사용자 지정합니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-mvc/crud
ms.openlocfilehash: 043fe513f370cf63637733b66ca195e7887faab0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054296"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a><span data-ttu-id="7d25d-103">자습서: CRUD 기능 구현 - ASP.NET MVC 및 EF Core 사용</span><span class="sxs-lookup"><span data-stu-id="7d25d-103">Tutorial: Implement CRUD Functionality - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="7d25d-104">이전 자습서에서 Entity Framework 및 SQL Server LocalDB를 사용하여 데이터를 저장하고 표시하는 MVC 애플리케이션을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-104">In the previous tutorial, you created an MVC application that stores and displays data using the Entity Framework and SQL Server LocalDB.</span></span> <span data-ttu-id="7d25d-105">이 자습서에서는 MVC 스캐폴딩이 컨트롤러 및 보기에서 자동으로 만드는 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토하고 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-105">In this tutorial, you'll review and customize the CRUD (create, read, update, delete) code that the MVC scaffolding automatically creates for you in controllers and views.</span></span>

> [!NOTE]
> <span data-ttu-id="7d25d-106">컨트롤러와 데이터 액세스 계층 간에 추상화 계층을 만들기 위해 리포지토리 패턴을 구현하는 일반적인 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-106">It's a common practice to implement the repository pattern in order to create an abstraction layer between your controller and the data access layer.</span></span> <span data-ttu-id="7d25d-107">이러한 자습서를 간단하고 Entity Framework 자체를 사용하는 방법을 가르치는 데 초점을 두도록 유지하기 위해 리포지토리를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-107">To keep these tutorials simple and focused on teaching how to use the Entity Framework itself, they don't use repositories.</span></span> <span data-ttu-id="7d25d-108">EF를 사용하는 리포지토리에 대한 자세한 내용은 [이 시리즈의 마지막 자습서](advanced.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d25d-108">For information about repositories with EF, see [the last tutorial in this series](advanced.md).</span></span>

<span data-ttu-id="7d25d-109">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7d25d-110">세부 정보 사용자 지정 페이지</span><span class="sxs-lookup"><span data-stu-id="7d25d-110">Customize the Details page</span></span>
> * <span data-ttu-id="7d25d-111">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-111">Update the Create page</span></span>
> * <span data-ttu-id="7d25d-112">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-112">Update the Edit page</span></span>
> * <span data-ttu-id="7d25d-113">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-113">Update the Delete page</span></span>
> * <span data-ttu-id="7d25d-114">데이터베이스 연결 닫기</span><span class="sxs-lookup"><span data-stu-id="7d25d-114">Close database connections</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d25d-115">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="7d25d-115">Prerequisites</span></span>

* [<span data-ttu-id="7d25d-116">EF Core 및 ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="7d25d-116">Get started with EF Core and ASP.NET Core MVC</span></span>](intro.md)

## <a name="customize-the-details-page"></a><span data-ttu-id="7d25d-117">세부 정보 사용자 지정 페이지</span><span class="sxs-lookup"><span data-stu-id="7d25d-117">Customize the Details page</span></span>

<span data-ttu-id="7d25d-118">속성은 컬렉션을 보유하기 때문에 학생 인덱스 페이지에 대한 스캐폴드 코드는 `Enrollments` 속성을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-118">The scaffolded code for the Students Index page left out the `Enrollments` property, because that property holds a collection.</span></span> <span data-ttu-id="7d25d-119">**세부 정보** 페이지에서 HTML 테이블에 컬렉션의 콘텐츠를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-119">In the **Details** page, you'll display the contents of the collection in an HTML table.</span></span>

<span data-ttu-id="7d25d-120">*Controllers/StudentsController.cs* 에서 세부 정보 보기에 대한 작업 메서드는 `SingleOrDefaultAsync` 메서드를 사용하여 단일 `Student` 엔터티를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-120">In *Controllers/StudentsController.cs* , the action method for the Details view uses the `SingleOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="7d25d-121">`Include`를 호출하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-121">Add code that calls `Include`.</span></span> <span data-ttu-id="7d25d-122">다음 강조 표시된 코드에 표시된 것처럼 `ThenInclude` 및 `AsNoTracking` 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-122">`ThenInclude`,  and `AsNoTracking` methods, as shown in the following highlighted code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="7d25d-123">`Include` 및 `ThenInclude` 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내 `Enrollment.Course` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-123">The `Include` and `ThenInclude` methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span>  <span data-ttu-id="7d25d-124">[관련된 데이터 읽기](read-related-data.md) 자습서에서 이러한 메서드에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-124">You'll learn more about these methods in the [read related data](read-related-data.md) tutorial.</span></span>

<span data-ttu-id="7d25d-125">`AsNoTracking` 메서드는 반환된 엔터티가 현재 컨텍스트의 수명에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-125">The `AsNoTracking` method improves performance in scenarios where the entities returned won't be updated in the current context's lifetime.</span></span> <span data-ttu-id="7d25d-126">이 자습서의 끝에서 `AsNoTracking`에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-126">You'll learn more about `AsNoTracking` at the end of this tutorial.</span></span>

### <a name="route-data"></a><span data-ttu-id="7d25d-127">경로 데이터</span><span class="sxs-lookup"><span data-stu-id="7d25d-127">Route data</span></span>

<span data-ttu-id="7d25d-128">`Details` 메서드에 전달되는 키 값은 *경로 데이터* 에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-128">The key value that's passed to the `Details` method comes from *route data*.</span></span> <span data-ttu-id="7d25d-129">경로 데이터는 모델 바인더가 URL의 세그먼트에서 찾은 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-129">Route data is data that the model binder found in a segment of the URL.</span></span> <span data-ttu-id="7d25d-130">예를 들어 기본 경로는 컨트롤러, 작업 및 ID 세그먼트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-130">For example, the default route specifies controller, action, and id segments:</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

<span data-ttu-id="7d25d-131">다음 URL에서 기본 경로는 강사를 컨트롤러로, 인덱스를 작업으로, 1을 ID로 매핑합니다. 해당 내용은 경로 데이터 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-131">In the following URL, the default route maps Instructor as the controller, Index as the action, and 1 as the id; these are route data values.</span></span>

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

<span data-ttu-id="7d25d-132">URL의 마지막 부분("?courseID=2021")은 쿼리 문자열 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-132">The last part of the URL ("?courseID=2021") is a query string value.</span></span> <span data-ttu-id="7d25d-133">또한 모델 바인더는 쿼리 문자열 값으로 전달하는 경우 ID 값을 `Index` 메서드 `id` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-133">The model binder will also pass the ID value to the `Index` method `id` parameter if you pass it as a query string value:</span></span>

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

<span data-ttu-id="7d25d-134">인덱스 페이지에서 하이퍼링크 URL은 Razor 뷰의 태그 도우미 문에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-134">In the Index page, hyperlink URLs are created by tag helper statements in the Razor view.</span></span> <span data-ttu-id="7d25d-135">다음 Razor 코드에서 `id` 매개 변수는 기본 경로와 일치하므로 `id`는 경로 데이터에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-135">In the following Razor code, the `id` parameter matches the default route, so `id` is added to the route data.</span></span>

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

<span data-ttu-id="7d25d-136">`item.ID`가 6일 때 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-136">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit/6">Edit</a>
```

<span data-ttu-id="7d25d-137">다음 Razor 코드에서 `studentID`는 기본 경로의 매개 변수와 일치하지 않으므로 쿼리 문자열로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-137">In the following Razor code, `studentID` doesn't match a parameter in the default route, so it's added as a query string.</span></span>

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

<span data-ttu-id="7d25d-138">`item.ID`가 6일 때 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-138">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

<span data-ttu-id="7d25d-139">태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d25d-139">For more information about tag helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

### <a name="add-enrollments-to-the-details-view"></a><span data-ttu-id="7d25d-140">세부 정보 보기에 등록 추가</span><span class="sxs-lookup"><span data-stu-id="7d25d-140">Add enrollments to the Details view</span></span>

<span data-ttu-id="7d25d-141">*Views/Students/Details.cshtml* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-141">Open *Views/Students/Details.cshtml*.</span></span> <span data-ttu-id="7d25d-142">각 필드는 다음 예제와 같이 `DisplayNameFor` 및 `DisplayFor` 도우미를 사용하여 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-142">Each field is displayed using `DisplayNameFor` and `DisplayFor` helpers, as shown in the following example:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

<span data-ttu-id="7d25d-143">마지막 필드 뒤와 닫기 `</dl>` 태그 바로 앞에 등록의 목록을 표시하도록 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-143">After the last field and immediately before the closing `</dl>` tag, add the following code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

<span data-ttu-id="7d25d-144">코드를 붙여넣은 후 코드 들여쓰기가 잘못된 경우 CTRL-K-D를 눌러 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-144">If code indentation is wrong after you paste the code, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="7d25d-145">이 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-145">This code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="7d25d-146">각 등록의 경우 강좌 제목과 등급을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-146">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="7d25d-147">강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-147">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="7d25d-148">앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-148">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="7d25d-149">선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-149">You see the list of courses and grades for the selected student:</span></span>

![학생 세부 정보 페이지](crud/_static/student-details.png)

## <a name="update-the-create-page"></a><span data-ttu-id="7d25d-151">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-151">Update the Create page</span></span>

<span data-ttu-id="7d25d-152">*StudentsController.cs* 에서 try-catch 블록을 추가하고 `Bind` 특성에서 ID를 제거하여 HttpPost `Create` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-152">In *StudentsController.cs* , modify the HttpPost `Create` method by adding a try-catch block and removing ID from the `Bind` attribute.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

<span data-ttu-id="7d25d-153">이 코드는 ASP.NET Core MVC 모델 바인더에서 만든 학생 엔터티를 학생 엔터티 설정에 추가한 다음, 데이터베이스에 변경 내용을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-153">This code adds the Student entity created by the ASP.NET Core MVC model binder to the Students entity set and then saves the changes to the database.</span></span> <span data-ttu-id="7d25d-154">(모델 바인더는 양식에서 전송한 데이터를 쉽게 사용할 수 있도록 하는 ASP.NET Core MVC 기능을 참조합니다. 모델 바인더는 게시된 양식 값을 CLR 형식으로 변환하고 매개 변수의 동작 메서드에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-154">(Model binder refers to the ASP.NET Core MVC functionality that makes it easier for you to work with data submitted by a form; a model binder converts posted form values to CLR types and passes them to the action method in parameters.</span></span> <span data-ttu-id="7d25d-155">이 경우 모델 바인더는 양식 컬렉션에서 속성 값을 사용하여 학생 엔터티를 인스턴스화합니다.)</span><span class="sxs-lookup"><span data-stu-id="7d25d-155">In this case, the model binder instantiates a Student entity for you using property values from the Form collection.)</span></span>

<span data-ttu-id="7d25d-156">ID는 행이 삽입될 때 SQL 서버가 자동으로 설정하는 기본 키 값이므로 `Bind` 특성에서 `ID`를 제거했습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-156">You removed `ID` from the `Bind` attribute because ID is the primary key value which SQL Server will set automatically when the row is inserted.</span></span> <span data-ttu-id="7d25d-157">사용자의 입력은 ID 값을 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-157">Input from the user doesn't set the ID value.</span></span>

<span data-ttu-id="7d25d-158">`Bind` 특성 이외에 try-catch 블록은 스캐폴드 코드에 대해 만든 유일한 변경 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-158">Other than the `Bind` attribute, the try-catch block is the only change you've made to the scaffolded code.</span></span> <span data-ttu-id="7d25d-159">`DbUpdateException`에서 파생되는 예외가 변경 내용이 저장되는 동안 발견되는 경우 일반 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-159">If an exception that derives from `DbUpdateException` is caught while the changes are being saved, a generic error message is displayed.</span></span> <span data-ttu-id="7d25d-160">`DbUpdateException` 예외는 경우에 따라 프로그래밍 오류가 아니라 애플리케이션에 대한 외부적인 문제로 발생하므로 사용자는 다시 시도하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-160">`DbUpdateException` exceptions are sometimes caused by something external to the application rather than a programming error, so the user is advised to try again.</span></span> <span data-ttu-id="7d25d-161">이 샘플에서 구현되지 않지만 프로덕션 품질 애플리케이션은 예외를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-161">Although not implemented in this sample, a production quality application would log the exception.</span></span> <span data-ttu-id="7d25d-162">자세한 내용은 [모니터링 및 원격 분석(Azure로 실제 클라우드 앱 빌드)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)에서 **정보에 대한 로그** 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d25d-162">For more information, see the **Log for insight** section in [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).</span></span>

<span data-ttu-id="7d25d-163">`ValidateAntiForgeryToken` 특성은 CSRF(사이트 간 요청 위조) 공격을 방지하도록 돕습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-163">The `ValidateAntiForgeryToken` attribute helps prevent cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="7d25d-164">토큰은 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)에 의한 보기로 자동으로 주입되며 사용자에 의해 양식이 제출될 때 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-164">The token is automatically injected into the view by the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) and is included when the form is submitted by the user.</span></span> <span data-ttu-id="7d25d-165">토큰은 `ValidateAntiForgeryToken` 특성으로 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-165">The token is validated by the `ValidateAntiForgeryToken` attribute.</span></span> <span data-ttu-id="7d25d-166">자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d25d-166">For more information, see <xref:security/anti-request-forgery>.</span></span>

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a><span data-ttu-id="7d25d-167">초과 게시에 대한 보안 정보</span><span class="sxs-lookup"><span data-stu-id="7d25d-167">Security note about overposting</span></span>

<span data-ttu-id="7d25d-168">스캐폴드된 코드가 `Create` 메서드에 포함하는 `Bind` 특성은 만들기 시나리오에서 초과 게시를 방지하는 한 가지 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-168">The `Bind` attribute that the scaffolded code includes on the `Create` method is one way to protect against overposting in create scenarios.</span></span> <span data-ttu-id="7d25d-169">예를 들어 학생 엔터티가 이 웹 페이지에서 설정하는 것을 원하지 않는 `Secret` 속성을 포함한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-169">For example, suppose the Student entity includes a `Secret` property that you don't want this web page to set.</span></span>

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

<span data-ttu-id="7d25d-170">웹 페이지에 `Secret` 필드가 없는 경우에도 해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-170">Even if you don't have a `Secret` field on the web page, a hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="7d25d-171">학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하는 `Bind` 특성 없이 모델 바인더는 해당 `Secret` 양식 값을 선택하고 학생 엔터티 인스턴스를 만드는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-171">Without the `Bind` attribute limiting the fields that the model binder uses when it creates a Student instance, the model binder would pick up that `Secret` form value and use it to create the Student entity instance.</span></span> <span data-ttu-id="7d25d-172">그런 다음, 해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 데이터베이스에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-172">Then whatever value the hacker specified for the `Secret` form field would be updated in your database.</span></span> <span data-ttu-id="7d25d-173">다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-173">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![암호 필드를 추가하는 Fiddler](crud/_static/fiddler.png)

<span data-ttu-id="7d25d-175">값 "OverPost"는 웹 페이지가 속성을 설정할 수 있도록 의도하지 않았지만 삽입된 행의 `Secret` 속성에 성공적으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-175">The value "OverPost" would then be successfully added to the `Secret` property of the inserted row, although you never intended that the web page be able to set that property.</span></span>

<span data-ttu-id="7d25d-176">먼저 데이터베이스에서 엔터티를 읽은 다음, `TryUpdateModel`을 호출하고, 허용되는 명시적 속성 목록에 전달하여 편집 시나리오에서 초과 게시를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-176">You can prevent overposting in edit scenarios by reading the entity from the database first and then calling `TryUpdateModel`, passing in an explicit allowed properties list.</span></span> <span data-ttu-id="7d25d-177">이러한 자습서에서 사용되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-177">That's the method used in these tutorials.</span></span>

<span data-ttu-id="7d25d-178">대부분의 개발자가 선호하는 초과 게시를 방지하는 다른 방법은 모델 바인딩으로 엔터티 클래스를 사용하지 않고 보기 모델을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-178">An alternative way to prevent overposting that's preferred by many developers is to use view models rather than entity classes with model binding.</span></span> <span data-ttu-id="7d25d-179">보기 모델에서 업데이트하려는 속성만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-179">Include only the properties you want to update in the view model.</span></span> <span data-ttu-id="7d25d-180">MVC 모델 바인더가 완료되면 필요에 따라 AutoMapper와 같은 도구를 사용하여 엔터티 인스턴스에 보기 모델 속성을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-180">Once the MVC model binder has finished, copy the view model properties to the entity instance, optionally using a tool such as AutoMapper.</span></span> <span data-ttu-id="7d25d-181">엔터티 인스턴스의 `_context.Entry`를 사용하여 해당 상태를 `Unchanged`로 설정한 다음, 보기 모델에 포함된 각 엔터티 속성에서 `Property("PropertyName").IsModified`를 true로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-181">Use `_context.Entry` on the entity instance to set its state to `Unchanged`, and then set `Property("PropertyName").IsModified` to true on each entity property that's included in the view model.</span></span> <span data-ttu-id="7d25d-182">이 방법은 편집 및 만들기 시나리오에서 모두 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-182">This method works in both edit and create scenarios.</span></span>

### <a name="test-the-create-page"></a><span data-ttu-id="7d25d-183">만들기 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="7d25d-183">Test the Create page</span></span>

<span data-ttu-id="7d25d-184">*Views/Students/Create.cshtml* 에서 코드는 각 필드에 대해 `label`, `input` 및 `span`(유효성 검사 메시지의 경우) 태그 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-184">The code in *Views/Students/Create.cshtml* uses `label`, `input`, and `span` (for validation messages) tag helpers for each field.</span></span>

<span data-ttu-id="7d25d-185">앱을 실행하고, **학생** 탭을 선택하고, **새로 만들기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-185">Run the app, select the **Students** tab, and click **Create New**.</span></span>

<span data-ttu-id="7d25d-186">이름 및 날짜를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-186">Enter names and a date.</span></span> <span data-ttu-id="7d25d-187">브라우저가 그렇게 수행하도록 하는 경우 잘못된 날짜를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-187">Try entering an invalid date if your browser lets you do that.</span></span> <span data-ttu-id="7d25d-188">(일부 브라우저는 강제로 날짜 선택을 사용하도록 합니다.) 그런 다음, **만들기** 를 클릭하여 오류 메시지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-188">(Some browsers force you to use a date picker.) Then click **Create** to see the error message.</span></span>

![날자 유효성 검사 오류](crud/_static/date-error.png)

<span data-ttu-id="7d25d-190">이는 기본적으로 얻는 서버 쪽 유효성 검사입니다. 이후의 자습서에서 클라이언트 쪽 유효성 검사에 대한 코드를 생성하는 특성을 추가하는 방법도 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-190">This is server-side validation that you get by default; in a later tutorial you'll see how to add attributes that will generate code for client-side validation also.</span></span> <span data-ttu-id="7d25d-191">다음 강조 표시된 코드는 `Create` 메서드에서 모델 유효성 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-191">The following highlighted code shows the model validation check in the `Create` method.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

<span data-ttu-id="7d25d-192">유효한 값으로 날짜를 변경하고 **만들기** 를 클릭하여 **인덱스** 페이지에 표시되는 새 학생을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-192">Change the date to a valid value and click **Create** to see the new student appear in the **Index** page.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="7d25d-193">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-193">Update the Edit page</span></span>

<span data-ttu-id="7d25d-194">*StudentController.cs* 에서 HttpGet `Edit` 메서드(`HttpPost` 특성이 없는 것)는 `SingleOrDefaultAsync` 메서드를 사용하여 `Details` 메서드에서 본 것처럼 선택한 학생 엔터티를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-194">In *StudentController.cs* , the HttpGet `Edit` method (the one without the `HttpPost` attribute) uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the `Details` method.</span></span> <span data-ttu-id="7d25d-195">이 메서드를 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-195">You don't need to change this method.</span></span>

### <a name="recommended-httppost-edit-code-read-and-update"></a><span data-ttu-id="7d25d-196">권장되는 HttpPost 편집 코드: 읽기 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-196">Recommended HttpPost Edit code: Read and update</span></span>

<span data-ttu-id="7d25d-197">HttpPost 편집 작업 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-197">Replace the HttpPost Edit action method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

<span data-ttu-id="7d25d-198">이러한 변경 내용은 초과 게시를 방지하기 위해 보안 모범 사례를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-198">These changes implement a security best practice to prevent overposting.</span></span> <span data-ttu-id="7d25d-199">스캐폴더는 `Bind` 특성을 생성했고 모델 바인더에서 만든 엔터티를 `Modified` 플래그로 설정된 엔터티에 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-199">The scaffolder generated a `Bind` attribute and added the entity created by the model binder to the entity set with a `Modified` flag.</span></span> <span data-ttu-id="7d25d-200">`Bind` 특성은 `Include` 매개 변수에 나열되지 않은 필드에서 기존의 모든 데이터를 지우므로 해당 코드는 대부분의 시나리오에 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-200">That code isn't recommended for many scenarios because the `Bind` attribute clears out any pre-existing data in fields not listed in the `Include` parameter.</span></span>

<span data-ttu-id="7d25d-201">새 코드는 기존 엔터티를 읽고 `TryUpdateModel`을 호출하여 [게시된 양식 데이터에서 사용자 입력에 따라](xref:mvc/models/model-binding) 검색된 엔터티에서 필드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-201">The new code reads the existing entity and calls `TryUpdateModel` to update fields in the retrieved entity [based on user input in the posted form data](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7d25d-202">Entity Framework의 자동 변경 내용 추적은 양식 입력에 의해 변경된 필드에서 `Modified` 플래그를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-202">The Entity Framework's automatic change tracking sets the `Modified` flag on the fields that are changed by form input.</span></span> <span data-ttu-id="7d25d-203">`SaveChanges` 메서드가 호출되는 경우 Entity Framework는 SQL 문을 만들어 데이터베이스 행을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-203">When the `SaveChanges` method is called, the Entity Framework creates SQL statements to update the database row.</span></span> <span data-ttu-id="7d25d-204">동시성 충돌은 무시되고 사용자가 업데이트한 테이블 열만 데이터베이스에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-204">Concurrency conflicts are ignored, and only the table columns that were updated by the user are updated in the database.</span></span> <span data-ttu-id="7d25d-205">(이후의 자습서에서는 동시성 충돌을 처리하는 방법을 보여 줍니다.)</span><span class="sxs-lookup"><span data-stu-id="7d25d-205">(A later tutorial shows how to handle concurrency conflicts.)</span></span>

<span data-ttu-id="7d25d-206">초과 게시를 방지하는 모범 사례로 **편집** 페이지에서 업데이트 가능하도록 하려는 필드가 `TryUpdateModel` 매개 변수에서 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-206">As a best practice to prevent overposting, the fields that you want to be updateable by the **Edit** page are declared in the `TryUpdateModel` parameters.</span></span> <span data-ttu-id="7d25d-207">(매개 변수 목록에서 필드 목록 앞의 빈 문자열은 양식 필드 이름으로 사용하는 접두사에 대한 것입니다.) 현재 보호하는 추가 필드가 없지만 모델 바인더에서 바인딩하길 원하는 필드를 나열하는 것은 향후에 데이터 모델에 필드를 추가하는지 확인하며, 여기에서 명시적으로 추가할 때까지 자동으로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-207">(The empty string preceding the list of fields in the parameter list is for a prefix to use with the form fields names.) Currently there are no extra fields that you're protecting, but listing the fields that you want the model binder to bind ensures that if you add fields to the data model in the future, they're automatically protected until you explicitly add them here.</span></span>

<span data-ttu-id="7d25d-208">이러한 변경 내용의 결과로 HttpPost `Edit` 메서드의 메서드 서명은 HttpGet `Edit` 메서드와 동일하므로 메서드 `EditPost`의 이름을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-208">As a result of these changes, the method signature of the HttpPost `Edit` method is the same as the HttpGet `Edit` method; therefore you've renamed the method `EditPost`.</span></span>

### <a name="alternative-httppost-edit-code-create-and-attach"></a><span data-ttu-id="7d25d-209">대체 HttpPost 편집 코드: 만들기 및 연결</span><span class="sxs-lookup"><span data-stu-id="7d25d-209">Alternative HttpPost Edit code: Create and attach</span></span>

<span data-ttu-id="7d25d-210">권장된 HttpPost 편집 코드는 변경된 열만 업데이트되도록 하며 모델 바인딩에 포함되지 않기를 원하는 속성에 데이터를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-210">The recommended HttpPost edit code ensures that only changed columns get updated and preserves data in properties that you don't want included for model binding.</span></span> <span data-ttu-id="7d25d-211">그러나 읽기 우선 접근 방식에는 추가 데이터베이스 읽기가 필요하며 동시성 충돌 처리에 대한 더 복잡한 코드가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-211">However, the read-first approach requires an extra database read, and can result in more complex code for handling concurrency conflicts.</span></span> <span data-ttu-id="7d25d-212">대안은 모델 바인더에서 만든 엔터티를 EF 컨텍스트에 연결하고 수정된 것으로 표시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-212">An alternative is to attach an entity created by the model binder to the EF context and mark it as modified.</span></span> <span data-ttu-id="7d25d-213">(이 코드로 프로젝트를 업데이트하지 마십시오. 선택적 접근 방식을 설명하기 위해 표시되었습니다.)</span><span class="sxs-lookup"><span data-stu-id="7d25d-213">(Don't update your project with this code, it's only shown to illustrate an optional approach.)</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

<span data-ttu-id="7d25d-214">웹 페이지 UI가 엔터티에 모든 필드를 포함하고 그 중 하나를 업데이트할 수 있는 경우 이 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-214">You can use this approach when the web page UI includes all of the fields in the entity and can update any of them.</span></span>

<span data-ttu-id="7d25d-215">스캐폴드된 코드는 만들기 및 연결 방법을 사용하지만 `DbUpdateConcurrencyException` 예외를 catch하고 404 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-215">The scaffolded code uses the create-and-attach approach but only catches `DbUpdateConcurrencyException` exceptions and returns 404 error codes.</span></span>  <span data-ttu-id="7d25d-216">표시된 예제는 모든 데이터베이스 업데이트 예외를 catch하고 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-216">The example shown catches any database update exception and displays an error message.</span></span>

### <a name="entity-states"></a><span data-ttu-id="7d25d-217">엔터티 상태</span><span class="sxs-lookup"><span data-stu-id="7d25d-217">Entity States</span></span>

<span data-ttu-id="7d25d-218">데이터베이스 컨텍스트는 메모리의 엔터티가 데이터베이스의 해당 열과 동기화 상태인지 여부의 추적을 유지하고 이 정보는 `SaveChanges` 메서드를 호출할 때 발생하는 작업을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-218">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database, and this information determines what happens when you call the `SaveChanges` method.</span></span> <span data-ttu-id="7d25d-219">예를 들어 새 엔터티를 `Add` 메서드에 전달하는 경우 해당 엔터티의 상태가 `Added`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-219">For example, when you pass a new entity to the `Add` method, that entity's state is set to `Added`.</span></span> <span data-ttu-id="7d25d-220">그런 다음, `SaveChanges` 메서드를 호출하면 데이터베이스 컨텍스트는 SQL INSERT 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-220">Then when you call the `SaveChanges` method, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="7d25d-221">엔터티는 다음 상태 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-221">An entity may be in one of the following states:</span></span>

* <span data-ttu-id="7d25d-222">`Added`.</span><span class="sxs-lookup"><span data-stu-id="7d25d-222">`Added`.</span></span> <span data-ttu-id="7d25d-223">엔터티가 아직 데이터베이스에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-223">The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="7d25d-224">`SaveChanges` 메서드가 INSERT 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-224">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="7d25d-225">`Unchanged`.</span><span class="sxs-lookup"><span data-stu-id="7d25d-225">`Unchanged`.</span></span> <span data-ttu-id="7d25d-226">`SaveChanges` 메서드에서 이 엔터티로 아무 작업도 수행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-226">Nothing needs to be done with this entity by the `SaveChanges` method.</span></span> <span data-ttu-id="7d25d-227">데이터베이스에서 엔터티를 읽을 때 엔터티는 이 상태로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-227">When you read an entity from the database, the entity starts out with this status.</span></span>

* <span data-ttu-id="7d25d-228">`Modified`.</span><span class="sxs-lookup"><span data-stu-id="7d25d-228">`Modified`.</span></span> <span data-ttu-id="7d25d-229">일부 또는 모든 엔터티의 속성 값이 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-229">Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="7d25d-230">`SaveChanges` 메서드는 UPDATE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-230">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="7d25d-231">`Deleted`.</span><span class="sxs-lookup"><span data-stu-id="7d25d-231">`Deleted`.</span></span> <span data-ttu-id="7d25d-232">엔터티가 삭제되도록 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-232">The entity has been marked for deletion.</span></span> <span data-ttu-id="7d25d-233">`SaveChanges` 메서드는 DELETE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-233">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="7d25d-234">`Detached`.</span><span class="sxs-lookup"><span data-stu-id="7d25d-234">`Detached`.</span></span> <span data-ttu-id="7d25d-235">엔터티가 데이터베이스 컨텍스트에 의해 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-235">The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="7d25d-236">데스크톱 애플리케이션에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-236">In a desktop application, state changes are typically set automatically.</span></span> <span data-ttu-id="7d25d-237">엔터티를 읽고 해당 속성 값의 일부를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-237">You read an entity and make changes to some of its property values.</span></span> <span data-ttu-id="7d25d-238">이렇게 하면 해당 엔터티 상태가 자동으로 `Modified`로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-238">This causes its entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="7d25d-239">그런 다음, `SaveChanges`를 호출하면 Entity Framework는 변경한 실제 속성만 업데이트하는 SQL UPDATE 문을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-239">Then when you call `SaveChanges`, the Entity Framework generates a SQL UPDATE statement that updates only the actual properties that you changed.</span></span>

<span data-ttu-id="7d25d-240">웹앱에서는 페이지가 렌더링된 후 처음에 엔터티를 읽고 편집될 해당 데이터를 표시하는 `DbContext`가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-240">In a web app, the `DbContext` that initially reads an entity and displays its data to be edited is disposed after a page is rendered.</span></span> <span data-ttu-id="7d25d-241">HttpPost `Edit` 작업 메스드가 호출되면 새 웹 요청이 만들어지고 `DbContext`의 새 인스턴스를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-241">When the HttpPost `Edit` action method is called,  a new web request is made and you have a new instance of the `DbContext`.</span></span> <span data-ttu-id="7d25d-242">새로운 컨텍스트의 엔터티를 다시 읽는 경우 데스크톱 처리를 시뮬레이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-242">If you re-read the entity in that new context, you simulate desktop processing.</span></span>

<span data-ttu-id="7d25d-243">하지만 추가 읽기 작업을 수행하지 않으려는 경우 모델 바인더를 통해 생성된 엔터티 개체를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-243">But if you don't want to do the extra read operation, you have to use the entity object created by the model binder.</span></span>  <span data-ttu-id="7d25d-244">이 작업을 수행하는 가장 간단한 방법은 앞에 표시된 대체 HttpPost 편집 코드에서 수행되는 것처럼 엔터티 상태를 Modified로 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-244">The simplest way to do this is to set the entity state to Modified as is done in the alternative HttpPost Edit code shown earlier.</span></span> <span data-ttu-id="7d25d-245">그런 다음, `SaveChanges`를 호출하면 Entity Framework는 컨텍스트에서 변경한 속성을 알 수 없기 때문에 데이터베이스 행의 모든 열을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-245">Then when you call `SaveChanges`, the Entity Framework updates all columns of the database row, because the context has no way to know which properties you changed.</span></span>

<span data-ttu-id="7d25d-246">읽기 우선 접근 방식을 피하길 원하지만 SQL UPDATE 문이 사용자가 실제로 변경한 필드만 업데이트하기를 원하는 경우 코드는 더 복잡합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-246">If you want to avoid the read-first approach, but you also want the SQL UPDATE statement to update only the fields that the user actually changed, the code is more complex.</span></span> <span data-ttu-id="7d25d-247">HttpPost `Edit` 메서드가 호출될 때 사용할 수 있도록 특정한 방식으로(예: 숨겨진 필드를 사용하여) 원래 값을 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-247">You have to save the original values in some way (such as by using hidden fields) so that they're available when the HttpPost `Edit` method is called.</span></span> <span data-ttu-id="7d25d-248">그러면 원래 값을 사용하여 학생 엔터티를 만들고, 엔터티의 해당 원래 값으로 `Attach` 메서드를 호출하고, 엔터티의 값을 새 값으로 업데이트한 다음, `SaveChanges`를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-248">Then you can create a Student entity using the original values, call the `Attach` method with that original version of the entity, update the entity's values to the new values, and then call `SaveChanges`.</span></span>

### <a name="test-the-edit-page"></a><span data-ttu-id="7d25d-249">편집 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="7d25d-249">Test the Edit page</span></span>

<span data-ttu-id="7d25d-250">앱을 실행하고, **학생** 탭을 선택한 다음, **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-250">Run the app, select the **Students** tab, then click an **Edit** hyperlink.</span></span>

![학생 편집 페이지](crud/_static/student-edit.png)

<span data-ttu-id="7d25d-252">데이터의 일부를 변경하고 **저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-252">Change some of the data and click **Save**.</span></span> <span data-ttu-id="7d25d-253">**인덱스** 페이지가 열리고 변경된 데이터가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-253">The **Index** page opens and you see the changed data.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="7d25d-254">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-254">Update the Delete page</span></span>

<span data-ttu-id="7d25d-255">*StudentController.cs* 에서 HttpGet `Delete` 메서드에 대한 템플릿 코드는 `SingleOrDefaultAsync` 메서드를 사용하여 세부 정보 및 편집 메서드에서 본 것처럼 선택한 학생 엔터티를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-255">In *StudentController.cs* , the template code for the HttpGet `Delete` method uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the Details and Edit methods.</span></span> <span data-ttu-id="7d25d-256">그러나 `SaveChanges`에 대한 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현하려면 이 메서드 및 해당 보기에 일부 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-256">However, to implement a custom error message when the call to `SaveChanges` fails, you'll add some functionality to this method and its corresponding view.</span></span>

<span data-ttu-id="7d25d-257">업데이트 및 만들기 작업에 대해 본 것과 같이 삭제 작업에는 두 개의 작업 메서드가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-257">As you saw for update and create operations, delete operations require two action methods.</span></span> <span data-ttu-id="7d25d-258">GET 요청에 대한 응답에서 호출되는 메서드는 사용자에게 삭제 작업을 승인 또는 취소하는 기회를 제공하는 보기를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-258">The method that's called in response to a GET request displays a view that gives the user a chance to approve or cancel the delete operation.</span></span> <span data-ttu-id="7d25d-259">사용자가 승인하는 경우 POST 요청이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-259">If the user approves it, a POST request is created.</span></span> <span data-ttu-id="7d25d-260">이 경우 HttpPost `Delete` 메서드가 호출되고 해당 메서드는 삭제 작업을 실제로 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-260">When that happens, the HttpPost `Delete` method is called and then that method actually performs the delete operation.</span></span>

<span data-ttu-id="7d25d-261">try-catch 블록을 HttpPost `Delete` 메서드에 추가하여 데이터베이스가 업데이트될 때 발생할 수 있는 오류를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-261">You'll add a try-catch block to the HttpPost `Delete` method to handle any errors that might occur when the database is updated.</span></span> <span data-ttu-id="7d25d-262">오류가 발생하는 경우 HttpPost Delete 메서드는 오류가 발생했음을 나타내는 매개 변수를 전달하여 HttpGet Delete 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-262">If an error occurs, the HttpPost Delete method calls the HttpGet Delete method, passing it a parameter that indicates that an error has occurred.</span></span> <span data-ttu-id="7d25d-263">그런 다음, HttpGet Delete 메서드는 사용자에게 취소 또는 다시 시도하는 기회를 제공하는 오류 메시지와 함께 확인 페이지를 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-263">The HttpGet Delete method then redisplays the confirmation page along with the error message, giving the user an opportunity to cancel or try again.</span></span>

<span data-ttu-id="7d25d-264">HttpGet `Delete` 작업 메서드를 오류 보고를 관리하는 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-264">Replace the HttpGet `Delete` action method with the following code, which manages error reporting.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

<span data-ttu-id="7d25d-265">이 코드는 변경 내용 저장에 실패한 후 메서드가 호출되었는지 여부를 나타내는 선택적 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-265">This code accepts an optional parameter that indicates whether the method was called after a failure to save changes.</span></span> <span data-ttu-id="7d25d-266">HttpGet `Delete` 메서드가 이전 실패 없이 호출되는 경우 이 매개 변수는 false입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-266">This parameter is false when the HttpGet `Delete` method is called without a previous failure.</span></span> <span data-ttu-id="7d25d-267">데이터베이스 업데이트 오류에 대한 응답에서 HttpPost `Delete` 메서드로 호출되는 경우 매개 변수는 true이고 오류 메시지가 보기에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-267">When it's called by the HttpPost `Delete` method in response to a database update error, the parameter is true and an error message is passed to the view.</span></span>

### <a name="the-read-first-approach-to-httppost-delete"></a><span data-ttu-id="7d25d-268">HttpPost Delete에 대한 읽기 우선 방법</span><span class="sxs-lookup"><span data-stu-id="7d25d-268">The read-first approach to HttpPost Delete</span></span>

<span data-ttu-id="7d25d-269">HttpPost `Delete` 작업 메서드(`DeleteConfirmed`라는)를 실제 삭제 작업을 수행하고 모든 데이터베이스 업데이트 오류를 catch하는 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-269">Replace the HttpPost `Delete` action method (named `DeleteConfirmed`) with the following code, which performs the actual delete operation and catches any database update errors.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

<span data-ttu-id="7d25d-270">이 코드는 선택한 엔터티를 검색한 다음, `Remove` 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-270">This code retrieves the selected entity, then calls the `Remove` method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="7d25d-271">`SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-271">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span>

### <a name="the-create-and-attach-approach-to-httppost-delete"></a><span data-ttu-id="7d25d-272">HttpPost Delete에 대한 만들기 및 연결 방법</span><span class="sxs-lookup"><span data-stu-id="7d25d-272">The create-and-attach approach to HttpPost Delete</span></span>

<span data-ttu-id="7d25d-273">대규모 애플리케이션의 성능 향상이 우선 순위인 경우 기본 키 값만을 사용하여 학생 엔터티를 인스턴스화한 다음, 엔터티 상태를 `Deleted`로 설정하여 불필요한 SQL 쿼리를 피할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-273">If improving performance in a high-volume application is a priority, you could avoid an unnecessary SQL query by instantiating a Student entity using only the primary key value and then setting the entity state to `Deleted`.</span></span> <span data-ttu-id="7d25d-274">Entity Framework에서 엔터티를 삭제하기 위해 필요한 모든 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-274">That's all that the Entity Framework needs in order to delete the entity.</span></span> <span data-ttu-id="7d25d-275">(프로젝트에 이 코드를 배치하지 마십시오. 대안을 설명하기 위한 것입니다.)</span><span class="sxs-lookup"><span data-stu-id="7d25d-275">(Don't put this code in your project; it's here just to illustrate an alternative.)</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

<span data-ttu-id="7d25d-276">엔터티에 삭제되어야 하는 관련된 데이터가 있는 경우 계단식 삭제가 데이터베이스에 구성되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-276">If the entity has related data that should also be deleted, make sure that cascade delete is configured in the database.</span></span> <span data-ttu-id="7d25d-277">엔터티 삭제에 대한 이 접근 방식을 사용하여 EF는 삭제될 관련된 엔터티가 있다는 것을 모를 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-277">With this approach to entity deletion, EF might not realize there are related entities to be deleted.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="7d25d-278">삭제 보기 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-278">Update the Delete view</span></span>

<span data-ttu-id="7d25d-279">*Views/Student/Delete.cshtml* 에서 다음 예제와 같이 h2 제목과 h3 제목 사이에 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-279">In *Views/Student/Delete.cshtml* , add an error message between the h2 heading and the h3 heading, as shown in the following example:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

<span data-ttu-id="7d25d-280">앱을 실행하고, **학생** 탭을 선택하고, **삭제** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-280">Run the app, select the **Students** tab, and click a **Delete** hyperlink:</span></span>

![삭제 확인 페이지](crud/_static/student-delete.png)

<span data-ttu-id="7d25d-282">**삭제** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-282">Click **Delete**.</span></span> <span data-ttu-id="7d25d-283">삭제된 학생 없이 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-283">The Index page is displayed without the deleted student.</span></span> <span data-ttu-id="7d25d-284">(동시성 자습서의 작업에서 오류 처리 코드의 예를 볼 수 있습니다.)</span><span class="sxs-lookup"><span data-stu-id="7d25d-284">(You'll see an example of the error handling code in action in the concurrency tutorial.)</span></span>

## <a name="close-database-connections"></a><span data-ttu-id="7d25d-285">데이터베이스 연결 닫기</span><span class="sxs-lookup"><span data-stu-id="7d25d-285">Close database connections</span></span>

<span data-ttu-id="7d25d-286">데이터베이스 연결이 유지하는 리소스를 해제하려면 컨텍스트 인스턴스는 작업을 완료했을 때 가능한 빨리 삭제되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-286">To free up the resources that a database connection holds, the context instance must be disposed as soon as possible when you are done with it.</span></span> <span data-ttu-id="7d25d-287">ASP.NET Core 기본 제공 [종속성 주입](../../fundamentals/dependency-injection.md)은 해당 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-287">The ASP.NET Core built-in [dependency injection](../../fundamentals/dependency-injection.md) takes care of that task for you.</span></span>

<span data-ttu-id="7d25d-288">*Startup.cs* 에서 [AddDbContext 확장 메서드](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs)를 호출하여 ASP.NET Core DI 컨테이너에서 `DbContext` 클래스를 프로비전합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-288">In *Startup.cs* , you call the [AddDbContext extension method](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) to provision the `DbContext` class in the ASP.NET Core DI container.</span></span> <span data-ttu-id="7d25d-289">해당 메서드는 서비스 수명을 기본적으로 `Scoped`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-289">That method sets the service lifetime to `Scoped` by default.</span></span> <span data-ttu-id="7d25d-290">`Scoped`는 웹 요청 수명과 일치하는 컨텍스트 개체 수명을 의미하며, `Dispose` 메서드는 웹 요청이 끝날 때 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-290">`Scoped` means the context object lifetime coincides with the web request life time, and the `Dispose` method will be called automatically at the end of the web request.</span></span>

## <a name="handle-transactions"></a><span data-ttu-id="7d25d-291">트랜잭션 처리</span><span class="sxs-lookup"><span data-stu-id="7d25d-291">Handle transactions</span></span>

<span data-ttu-id="7d25d-292">기본적으로 Entity Framework는 트랜잭션을 암시적으로 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-292">By default the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="7d25d-293">여러 행 또는 테이블을 변경한 다음, `SaveChanges`를 호출하는 시나리오에서 Entity Framework는 모든 변경 내용이 성공했는지 또는 모두 실패했는지를 자동으로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-293">In scenarios where you make changes to multiple rows or tables and then call `SaveChanges`, the Entity Framework automatically makes sure that either all of your changes succeed or they all fail.</span></span> <span data-ttu-id="7d25d-294">일부 변경 내용이 먼저 완료된 다음, 오류가 발생하는 경우 해당 변경 내용이 자동으로 롤백됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-294">If some changes are done first and then an error happens, those changes are automatically rolled back.</span></span> <span data-ttu-id="7d25d-295">더 많은 컨트롤이 필요한 시나리오의 경우, 예를 들어 트랜잭션의 Entity Framework 밖에서 수행한 작업을 포함하려는 경우 [트랜잭션](/ef/core/saving/transactions)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d25d-295">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="no-tracking-queries"></a><span data-ttu-id="7d25d-296">비 추적 쿼리</span><span class="sxs-lookup"><span data-stu-id="7d25d-296">No-tracking queries</span></span>

<span data-ttu-id="7d25d-297">데이터베이스 컨텍스트가 테이블 행을 검색하고 해당 내용을 나타내는 엔터티 개체를 만드는 경우 기본적으로 메모리의 엔터티가 데이터베이스의 해당 내용과 동기화 상태인지 여부의 추적을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-297">When a database context retrieves table rows and creates entity objects that represent them, by default it keeps track of whether the entities in memory are in sync with what's in the database.</span></span> <span data-ttu-id="7d25d-298">메모리의 데이터는 캐시의 역할을 하고 엔터티를 업데이트할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-298">The data in memory acts as a cache and is used when you update an entity.</span></span> <span data-ttu-id="7d25d-299">컨텍스트 인스턴스는 일반적으로 수명이 짧으며(각 요청에 대해 새 것이 만들어지고 삭제됨) 엔터티를 읽는 컨텍스트는 일반적으로 해당 엔터티가 다시 사용되기 전에 삭제되므로 이 캐싱은 웹 애플리케이션에 종종 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-299">This caching is often unnecessary in a web application because context instances are typically short-lived (a new one is created and disposed for each request) and the context that reads an entity is typically disposed before that entity is used again.</span></span>

<span data-ttu-id="7d25d-300">`AsNoTracking` 메서드를 호출하여 메모리에서 엔터티 개체의 추적을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-300">You can disable tracking of entity objects in memory by calling the `AsNoTracking` method.</span></span> <span data-ttu-id="7d25d-301">이러한 작업을 수행할 수 있는 일반적인 시나리오는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-301">Typical scenarios in which you might want to do that include the following:</span></span>

* <span data-ttu-id="7d25d-302">컨텍스트 수명 동안 엔터티를 업데이트할 필요가 없으며 EF에서 [별도 쿼리에 의해 검색된 엔터티가 포함된 탐색 속성을 자동으로 로드](read-related-data.md)하도록 할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-302">During the context lifetime you don't need to update any entities, and you don't need EF to [automatically load navigation properties with  entities retrieved by separate queries](read-related-data.md).</span></span> <span data-ttu-id="7d25d-303">이러한 조건은 자주 컨트롤러의 HttpGet 작업 메서드에서 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-303">Frequently these conditions are met in a controller's HttpGet action methods.</span></span>

* <span data-ttu-id="7d25d-304">많은 양의 데이터를 검색하는 쿼리를 실행하며 반환된 데이터의 작은 부분만 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-304">You are running a query that retrieves a large volume of data, and only a small portion of the returned data will be updated.</span></span> <span data-ttu-id="7d25d-305">큰 쿼리에 대한 추적을 해제하고 업데이트되어야 하는 몇 가지 엔터티에 대한 쿼리를 나중에 실행하는 것이 보다 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-305">It may be more efficient to turn off tracking for the large query, and run a query later for the few entities that need to be updated.</span></span>

* <span data-ttu-id="7d25d-306">업데이트하기 위해 엔터티를 연결하려고 하지만 이전에 다른 목적을 위해 동일한 엔터티를 검색했습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-306">You want to attach an entity in order to update it, but earlier you retrieved the same entity for a different purpose.</span></span> <span data-ttu-id="7d25d-307">엔터티는 데이터베이스 컨텍스트에서 이미 추적 중이므로 변경하려는 엔터티를 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-307">Because the entity is already being tracked by the database context, you can't attach the entity that you want to change.</span></span> <span data-ttu-id="7d25d-308">이 상황을 처리하는 한 가지 방법은 이전 쿼리에서 `AsNoTracking`을 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-308">One way to handle this situation is to call `AsNoTracking` on the earlier query.</span></span>

<span data-ttu-id="7d25d-309">자세한 내용은 [추적과 비추적 비교](/ef/core/querying/tracking)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7d25d-309">For more information, see [Tracking vs. No-Tracking](/ef/core/querying/tracking).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="7d25d-310">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="7d25d-310">Get the code</span></span>

[<span data-ttu-id="7d25d-311">완성된 애플리케이션을 다운로드하거나 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-311">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="7d25d-312">다음 단계</span><span class="sxs-lookup"><span data-stu-id="7d25d-312">Next steps</span></span>

<span data-ttu-id="7d25d-313">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-313">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7d25d-314">세부 정보 페이지 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7d25d-314">Customized the Details page</span></span>
> * <span data-ttu-id="7d25d-315">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-315">Updated the Create page</span></span>
> * <span data-ttu-id="7d25d-316">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-316">Updated the Edit page</span></span>
> * <span data-ttu-id="7d25d-317">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7d25d-317">Updated the Delete page</span></span>
> * <span data-ttu-id="7d25d-318">데이터베이스 연결 닫기</span><span class="sxs-lookup"><span data-stu-id="7d25d-318">Closed database connections</span></span>

<span data-ttu-id="7d25d-319">정렬, 필터링 및 페이징을 추가하여 **인덱스** 페이지의 기능을 확장하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="7d25d-319">Advance to the next tutorial to learn how to expand the functionality of the **Index** page by adding sorting, filtering, and paging.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7d25d-320">다음: 정렬, 필터링 및 페이징</span><span class="sxs-lookup"><span data-stu-id="7d25d-320">Next: Sorting, filtering, and paging</span></span>](sort-filter-page.md)
