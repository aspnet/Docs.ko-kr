---
title: '2부. ASP.NET Core에서 EF Core를 사용한 :::no-loc(Razor)::: Pages - CRUD'
author: rick-anderson
description: ':::no-loc(Razor)::: Pages 및 Entity Framework 자습서 시리즈의 2부입니다.'
ms.author: riande
ms.date: 07/22/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/crud
ms.openlocfilehash: c5b9be64ea30cce7a3178bfbb244ef893e9639d2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053867"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a><span data-ttu-id="3632e-103">2부. ASP.NET Core에서 EF Core를 사용한 :::no-loc(Razor)::: Pages - CRUD</span><span class="sxs-lookup"><span data-stu-id="3632e-103">Part 2, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - CRUD</span></span>

<span data-ttu-id="3632e-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3632e-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3632e-105">이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="3632e-106">리포지토리 없음</span><span class="sxs-lookup"><span data-stu-id="3632e-106">No repository</span></span>

<span data-ttu-id="3632e-107">일부 개발자는 서비스 계층 또는 리포지토리 패턴을 사용하여 UI(:::no-loc(Razor)::: Pages) 및 데이터 액세스 계층 간에 추상화 계층을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-107">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (:::no-loc(Razor)::: Pages) and the data access layer.</span></span> <span data-ttu-id="3632e-108">이 자습서에서는 이 작업을 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-108">This tutorial doesn't do that.</span></span> <span data-ttu-id="3632e-109">복잡성을 최소화하고 자습서의 초점을 EF Core로 유지하기 위해 EF Core 코드가 페이지 모델 클래스에 직접 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-109">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="3632e-110">세부 정보 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-110">Update the Details page</span></span>

<span data-ttu-id="3632e-111">학생 페이지의 스캐폴드된 코드에는 등록 데이터가 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-111">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="3632e-112">이 섹션에서는 세부 정보 페이지에 등록을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-112">In this section, you add enrollments to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="3632e-113">등록 읽기</span><span class="sxs-lookup"><span data-stu-id="3632e-113">Read enrollments</span></span>

<span data-ttu-id="3632e-114">학생의 등록 데이터를 페이지에 표시하려면 해당 데이터를 읽어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-114">To display a student's enrollment data on the page, you need to read it.</span></span> <span data-ttu-id="3632e-115">*Pages/Students/Details.cshtml.cs* 의 스캐폴드된 코드는 등록 데이터 없이 학생 데이터만 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-115">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="3632e-116">`OnGetAsync` 메서드를 다음 코드로 바꿔서 선택한 학생에 대한 등록 데이터를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-116">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="3632e-117">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-117">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="3632e-118">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-118">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="3632e-119">이 메서드는 [읽기 관련 데이터](xref:data/ef-rp/read-related-data) 자습서에서 자세히 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-119">These methods are examined in detail in the [Reading related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="3632e-120">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-120">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="3632e-121">`AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-121">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="3632e-122">등록 표시</span><span class="sxs-lookup"><span data-stu-id="3632e-122">Display enrollments</span></span>

<span data-ttu-id="3632e-123">*Pages/Students/Details.cshtml* 의 코드를 다음 코드로 바꿔서 등록 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-123">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="3632e-124">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-124">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="3632e-125">위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-125">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="3632e-126">각 등록의 경우 강좌 제목과 등급을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-126">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="3632e-127">강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-127">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="3632e-128">앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-128">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="3632e-129">선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-129">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="3632e-130">단일 엔터티를 읽는 방법</span><span class="sxs-lookup"><span data-stu-id="3632e-130">Ways to read one entity</span></span>

<span data-ttu-id="3632e-131">생성된 코드는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용하여 하나의 엔터티를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-131">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="3632e-132">이 메서드는 검색된 항목이 없는 경우 Null을 반환합니다. 그렇지 않으면 쿼리 필터 조건을 충족하는 첫 번째 행을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-132">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="3632e-133">`FirstOrDefaultAsync`는 일반적으로 다음 대안보다 더 적합한 선택입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-133">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="3632e-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 쿼리 필터를 충족하는 엔터티가 둘 이상인 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="3632e-135">쿼리에서 두 개 이상의 행이 반환될 수 있는지 확인하기 위해 `SingleOrDefaultAsync`는 여러 행을 페치하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-135">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="3632e-136">쿼리가 고유 키를 검색하는 경우처럼 하나의 엔터티만 반환할 수 있는 경우에는 이 추가 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-136">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="3632e-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - PK(기본 키)가 있는 엔터티를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="3632e-138">PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 데이터베이스에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-138">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="3632e-139">이 메서드는 단일 엔터티를 조회하는 데 최적화되어 있지만 `FindAsync`를 사용하여 `Include`를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-139">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="3632e-140">따라서 관련 데이터가 필요한 경우 `FirstOrDefaultAsync`를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-140">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="3632e-141">경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="3632e-141">Route data vs. query string</span></span>

<span data-ttu-id="3632e-142">세부 정보 페이지의 URL은 `https://localhost:<port>/Students/Details?id=1`입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-142">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="3632e-143">엔터티의 기본 키 값은 쿼리 문자열에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-143">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="3632e-144">일부 개발자는 키 값을 경로 데이터로 전달하는 것을 선호합니다. `https://localhost:<port>/Students/Details/1`</span><span class="sxs-lookup"><span data-stu-id="3632e-144">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="3632e-145">자세한 내용은 [생성된 코드 업데이트](xref:tutorials/razor-pages/da1#update-the-generated-code)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3632e-145">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="3632e-146">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-146">Update the Create page</span></span>

<span data-ttu-id="3632e-147">만들기 페이지의 스캐폴드된 `OnPostAsync` 코드는 [초과 게시](#overposting)에 취약합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-147">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="3632e-148">*Pages/Students/Create.cshtml.cs* 의 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-148">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="3632e-149">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="3632e-149">TryUpdateModelAsync</span></span>

<span data-ttu-id="3632e-150">앞의 코드는 Student 개체를 만든 다음, 게시된 양식 필드를 사용하여 Student 개체의 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-150">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="3632e-151">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 메서드:</span><span class="sxs-lookup"><span data-stu-id="3632e-151">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="3632e-152">[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)에서 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_PageContext) 속성의 게시된 양식 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-152">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="3632e-153">나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="3632e-153">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="3632e-154">“Student” 접두사가 있는 양식 필드를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-154">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="3632e-155">예: `Student.FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="3632e-155">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="3632e-156">대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-156">It's not case sensitive.</span></span>
* <span data-ttu-id="3632e-157">[모델 바인딩](xref:mvc/models/model-binding) 시스템을 사용하여 양식 값을 문자열에서 `Student` 모델의 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-157">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="3632e-158">예를 들어 `EnrollmentDate`가 `DateTime`으로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-158">For example, `EnrollmentDate` is converted to `DateTime`.</span></span>

<span data-ttu-id="3632e-159">앱을 실행하고 학생 엔터티를 만들어 만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-159">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="3632e-160">초과 게시</span><span class="sxs-lookup"><span data-stu-id="3632e-160">Overposting</span></span>

<span data-ttu-id="3632e-161">게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-161">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="3632e-162">예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-162">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="3632e-163">앱에 만들기 또는 업데이트 :::no-loc(Razor)::: 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-163">Even if the app doesn't have a `Secret` field on the create or update :::no-loc(Razor)::: Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="3632e-164">해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-164">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="3632e-165">원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-165">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="3632e-166">해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 데이터베이스에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-166">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="3632e-167">다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-167">The following image shows the Fiddler tool adding the `Secret` field, with the value "OverPost", to the posted form values.</span></span>

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="3632e-169">값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-169">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="3632e-170">앱 디자이너가 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않더라도 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-170">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="3632e-171">뷰 모델</span><span class="sxs-lookup"><span data-stu-id="3632e-171">View model</span></span>

<span data-ttu-id="3632e-172">뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-172">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="3632e-173">애플리케이션 모델은 흔히 도메인 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-173">The application model is often called the domain model.</span></span> <span data-ttu-id="3632e-174">도메인 모델은 일반적으로 데이터베이스의 해당 엔터티에 필요한 모든 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-174">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="3632e-175">뷰 모델은 UI 페이지(예: 만들기 페이지)에 필요한 속성만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-175">The view model contains only the properties needed for the UI page, for example, the Create page.</span></span>

<span data-ttu-id="3632e-176">뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 :::no-loc(Razor)::: Pages 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-176">In addition to the view model, some apps use a binding model or input model to pass data between the :::no-loc(Razor)::: Pages page model class and the browser.</span></span> 

<span data-ttu-id="3632e-177">다음 `StudentVM` 뷰 모델을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="3632e-177">Consider the following `StudentVM` view model:</span></span>

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

<span data-ttu-id="3632e-178">다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-178">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

<span data-ttu-id="3632e-179">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-179">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="3632e-180">`SetValues`는 속성 이름 일치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-180">`SetValues` uses property name matching.</span></span> <span data-ttu-id="3632e-181">뷰 모델 형식:</span><span class="sxs-lookup"><span data-stu-id="3632e-181">The view model type:</span></span>

* <span data-ttu-id="3632e-182">모델 유형과 관련될 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-182">Doesn't need to be related to the model type.</span></span>
* <span data-ttu-id="3632e-183">일치하는 속성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-183">Needs to have properties that match.</span></span>

<span data-ttu-id="3632e-184">`StudentVM`을 사용하려면 만들기 페이지가 `Student` 대신 `StudentVM`을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-184">Using `StudentVM` requires the Create page use `StudentVM` rather than `Student`:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a><span data-ttu-id="3632e-185">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-185">Update the Edit page</span></span>

<span data-ttu-id="3632e-186">*Pages/Students/Edit.cshtml.cs* 에서 `OnGetAsync` 및 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-186">In *Pages/Students/Edit.cshtml.cs* , replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="3632e-187">코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-187">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="3632e-188">`FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-188">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="3632e-189">관련 데이터를 포함할 필요가 없는 경우에는 `FindAsync`가 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-189">When you don't have to include related data, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="3632e-190">`OnPostAsync`에는 `id` 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-190">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="3632e-191">현재 학생은 빈 학생을 만드는 대신 데이터베이스에서 페치합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-191">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="3632e-192">앱을 실행한 후 학생을 만들고 편집하여 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-192">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="3632e-193">엔터티 상태</span><span class="sxs-lookup"><span data-stu-id="3632e-193">Entity States</span></span>

<span data-ttu-id="3632e-194">데이터베이스 컨텍스트는 메모리의 엔터티가 해당하는 데이터베이스의 행과 동기화하는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-194">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="3632e-195">이 추적 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-195">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="3632e-196">예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-196">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="3632e-197">`SaveChangesAsync`가 호출되면 데이터베이스 컨텍스트가 SQL `INSERT` 명령을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-197">When `SaveChangesAsync` is called, the database context issues a SQL `INSERT` command.</span></span>

<span data-ttu-id="3632e-198">엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-198">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="3632e-199">`Added`: 엔터티가 아직 데이터베이스에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-199">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="3632e-200">`SaveChanges` 메서드는 `INSERT` 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-200">The `SaveChanges` method issues an `INSERT` statement.</span></span>

* <span data-ttu-id="3632e-201">`Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-201">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="3632e-202">엔터티는 데이터베이스에서 읽을 때 이 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-202">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="3632e-203">`Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-203">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="3632e-204">`SaveChanges` 메서드는 `UPDATE` 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-204">The `SaveChanges` method issues an `UPDATE` statement.</span></span>

* <span data-ttu-id="3632e-205">`Deleted`: 엔터티가 삭제되도록 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-205">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="3632e-206">`SaveChanges` 메서드는 `DELETE` 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-206">The `SaveChanges` method issues a `DELETE` statement.</span></span>

* <span data-ttu-id="3632e-207">`Detached`: 엔터티가 데이터베이스 컨텍스트에 의해 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-207">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="3632e-208">데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-208">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="3632e-209">엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-209">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="3632e-210">`SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL `UPDATE` 문이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-210">Calling `SaveChanges` generates a SQL `UPDATE` statement that updates only the changed properties.</span></span>

<span data-ttu-id="3632e-211">웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-211">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="3632e-212">페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-212">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="3632e-213">새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리가 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-213">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="3632e-214">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-214">Update the Delete page</span></span>

<span data-ttu-id="3632e-215">이 섹션에서는 `SaveChanges` 호출이 실패하는 경우의 사용자 지정 오류 메시지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-215">In this section, a custom error message is implemented when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="3632e-216">*Pages/Students/Delete.cshtml.cs* 의 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-216">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="3632e-217">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-217">The changes are highlighted:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

<span data-ttu-id="3632e-218">앞의 코드는 선택적 매개 변수 `saveChangesError`를 `OnGetAsync` 메서드 시그니처에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-218">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="3632e-219">`saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-219">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="3632e-220">일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-220">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="3632e-221">일시적인 네트워크 오류는 데이터베이스가 클라우드에 있을 때 발생 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-221">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="3632e-222">`saveChangesError` 매개 변수는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-222">The `saveChangesError` parameter is `false` when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="3632e-223">`OnGetAsync`가 `OnPostAsync`에 의해 호출되면 삭제 작업이 실패했으므로 `saveChangesError` 매개 변수는 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-223">When `OnGetAsync` is called by `OnPostAsync` because the delete operation failed, the `saveChangesError` parameter is `true`.</span></span>

<span data-ttu-id="3632e-224">`OnPostAsync` 메서드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-224">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="3632e-225">`SaveChanges`가 호출되면 SQL `DELETE` 명령이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-225">When `SaveChanges` is called, a SQL `DELETE` command is generated.</span></span> <span data-ttu-id="3632e-226">`Remove`가 실패하는 경우:</span><span class="sxs-lookup"><span data-stu-id="3632e-226">If `Remove` fails:</span></span>

* <span data-ttu-id="3632e-227">데이터베이스 예외가 catch되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-227">The database exception is caught.</span></span>
* <span data-ttu-id="3632e-228">삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-228">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="3632e-229">*Pages/Students/Delete.cshtml* 에 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-229">Add an error message to *Pages/Students/Delete.cshtml* :</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="3632e-230">앱을 실행하고 학생을 삭제하여 삭제 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-230">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3632e-231">다음 단계</span><span class="sxs-lookup"><span data-stu-id="3632e-231">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3632e-232">[이전 자습서](xref:data/ef-rp/intro)
> [다음 자습서](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="3632e-232">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3632e-233">이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-233">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="3632e-234">리포지토리 없음</span><span class="sxs-lookup"><span data-stu-id="3632e-234">No repository</span></span>

<span data-ttu-id="3632e-235">일부 개발자는 서비스 계층 또는 리포지토리 패턴을 사용하여 UI(:::no-loc(Razor)::: Pages) 및 데이터 액세스 계층 간에 추상화 계층을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-235">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (:::no-loc(Razor)::: Pages) and the data access layer.</span></span> <span data-ttu-id="3632e-236">이 자습서에서는 이 작업을 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-236">This tutorial doesn't do that.</span></span> <span data-ttu-id="3632e-237">복잡성을 최소화하고 자습서의 초점을 EF Core로 유지하기 위해 EF Core 코드가 페이지 모델 클래스에 직접 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-237">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="3632e-238">세부 정보 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-238">Update the Details page</span></span>

<span data-ttu-id="3632e-239">학생 페이지의 스캐폴드된 코드에는 등록 데이터가 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-239">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="3632e-240">이 섹션에서는 세부 정보 페이지에 등록을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-240">In this section, enrollments are added to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="3632e-241">등록 읽기</span><span class="sxs-lookup"><span data-stu-id="3632e-241">Read enrollments</span></span>

<span data-ttu-id="3632e-242">페이지에 학생의 등록 데이터를 표시하려면 등록 데이터를 읽어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-242">To display a student's enrollment data on the page, the enrollement data needs to be read.</span></span> <span data-ttu-id="3632e-243">*Pages/Students/Details.cshtml.cs* 의 스캐폴드된 코드는 등록 데이터 없이 학생 데이터만 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-243">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="3632e-244">`OnGetAsync` 메서드를 다음 코드로 바꿔서 선택한 학생에 대한 등록 데이터를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-244">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="3632e-245">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-245">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="3632e-246">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-246">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="3632e-247">이 메서드는 [읽기 관련 데이터](xref:data/ef-rp/read-related-data) 자습서에서 자세히 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-247">These methods are examined in detail in the [Reading related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="3632e-248">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-248">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="3632e-249">`AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-249">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="3632e-250">등록 표시</span><span class="sxs-lookup"><span data-stu-id="3632e-250">Display enrollments</span></span>

<span data-ttu-id="3632e-251">*Pages/Students/Details.cshtml* 의 코드를 다음 코드로 바꿔서 등록 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-251">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="3632e-252">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-252">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="3632e-253">위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-253">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="3632e-254">각 등록의 경우 강좌 제목과 등급을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-254">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="3632e-255">강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-255">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="3632e-256">앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-256">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="3632e-257">선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-257">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="3632e-258">단일 엔터티를 읽는 방법</span><span class="sxs-lookup"><span data-stu-id="3632e-258">Ways to read one entity</span></span>

<span data-ttu-id="3632e-259">생성된 코드는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용하여 하나의 엔터티를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-259">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="3632e-260">이 메서드는 검색된 항목이 없는 경우 Null을 반환합니다. 그렇지 않으면 쿼리 필터 조건을 충족하는 첫 번째 행을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-260">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="3632e-261">`FirstOrDefaultAsync`는 일반적으로 다음 대안보다 더 적합한 선택입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-261">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="3632e-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 쿼리 필터를 충족하는 엔터티가 둘 이상인 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="3632e-263">쿼리에서 두 개 이상의 행이 반환될 수 있는지 확인하기 위해 `SingleOrDefaultAsync`는 여러 행을 페치하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-263">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="3632e-264">쿼리가 고유 키를 검색하는 경우처럼 하나의 엔터티만 반환할 수 있는 경우에는 이 추가 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-264">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="3632e-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - PK(기본 키)가 있는 엔터티를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="3632e-266">PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 데이터베이스에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-266">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="3632e-267">이 메서드는 단일 엔터티를 조회하는 데 최적화되어 있지만 `FindAsync`를 사용하여 `Include`를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-267">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="3632e-268">따라서 관련 데이터가 필요한 경우 `FirstOrDefaultAsync`를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-268">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="3632e-269">경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="3632e-269">Route data vs. query string</span></span>

<span data-ttu-id="3632e-270">세부 정보 페이지의 URL은 `https://localhost:<port>/Students/Details?id=1`입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-270">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="3632e-271">엔터티의 기본 키 값은 쿼리 문자열에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-271">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="3632e-272">일부 개발자는 키 값을 경로 데이터로 전달하는 것을 선호합니다. `https://localhost:<port>/Students/Details/1`</span><span class="sxs-lookup"><span data-stu-id="3632e-272">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="3632e-273">자세한 내용은 [생성된 코드 업데이트](xref:tutorials/razor-pages/da1#update-the-generated-code)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3632e-273">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="3632e-274">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-274">Update the Create page</span></span>

<span data-ttu-id="3632e-275">만들기 페이지의 스캐폴드된 `OnPostAsync` 코드는 [초과 게시](#overposting)에 취약합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-275">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="3632e-276">*Pages/Students/Create.cshtml.cs* 의 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-276">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="3632e-277">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="3632e-277">TryUpdateModelAsync</span></span>

<span data-ttu-id="3632e-278">앞의 코드는 Student 개체를 만든 다음, 게시된 양식 필드를 사용하여 Student 개체의 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-278">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="3632e-279">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 메서드:</span><span class="sxs-lookup"><span data-stu-id="3632e-279">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="3632e-280">[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)에서 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_PageContext) 속성의 게시된 양식 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-280">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="3632e-281">나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="3632e-281">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="3632e-282">“Student” 접두사가 있는 양식 필드를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-282">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="3632e-283">예: `Student.FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="3632e-283">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="3632e-284">대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-284">It's not case sensitive.</span></span>
* <span data-ttu-id="3632e-285">[모델 바인딩](xref:mvc/models/model-binding) 시스템을 사용하여 양식 값을 문자열에서 `Student` 모델의 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-285">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="3632e-286">예를 들어 `EnrollmentDate`는 DateTime으로 변환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-286">For example, `EnrollmentDate` has to be converted to DateTime.</span></span>

<span data-ttu-id="3632e-287">앱을 실행하고 학생 엔터티를 만들어 만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-287">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="3632e-288">초과 게시</span><span class="sxs-lookup"><span data-stu-id="3632e-288">Overposting</span></span>

<span data-ttu-id="3632e-289">게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-289">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="3632e-290">예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-290">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="3632e-291">앱에 만들기 또는 업데이트 :::no-loc(Razor)::: 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-291">Even if the app doesn't have a `Secret` field on the create or update :::no-loc(Razor)::: Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="3632e-292">해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-292">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="3632e-293">원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-293">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="3632e-294">해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 데이터베이스에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-294">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="3632e-295">다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-295">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="3632e-297">값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-297">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="3632e-298">앱 디자이너가 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않더라도 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-298">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="3632e-299">뷰 모델</span><span class="sxs-lookup"><span data-stu-id="3632e-299">View model</span></span>

<span data-ttu-id="3632e-300">뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-300">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="3632e-301">애플리케이션 모델은 흔히 도메인 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-301">The application model is often called the domain model.</span></span> <span data-ttu-id="3632e-302">도메인 모델은 일반적으로 데이터베이스의 해당 엔터티에 필요한 모든 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-302">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="3632e-303">보기 모델은 사용되는 UI에 필요한 속성만 포함합니다(예: 만들기 페이지).</span><span class="sxs-lookup"><span data-stu-id="3632e-303">The view model contains only the properties needed for the UI that it is used for (for example, the Create page).</span></span>

<span data-ttu-id="3632e-304">뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 :::no-loc(Razor)::: Pages 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-304">In addition to the view model, some apps use a binding model or input model to pass data between the :::no-loc(Razor)::: Pages page model class and the browser.</span></span> 

<span data-ttu-id="3632e-305">다음 `Student` 뷰 모델을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="3632e-305">Consider the following `Student` view model:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

<span data-ttu-id="3632e-306">다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-306">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3632e-307">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-307">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="3632e-308">`SetValues`는 속성 이름 일치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-308">`SetValues` uses property name matching.</span></span> <span data-ttu-id="3632e-309">뷰 모델 형식은 모델 형식과 연결될 필요는 없으며 일치하는 속성만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-309">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="3632e-310">`StudentVM`을 사용하려면 `Student` 대신 `StudentVM`을 사용하도록 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml)을 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-310">Using `StudentVM` requires [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="3632e-311">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-311">Update the Edit page</span></span>

<span data-ttu-id="3632e-312">*Pages/Students/Edit.cshtml.cs* 에서 `OnGetAsync` 및 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-312">In *Pages/Students/Edit.cshtml.cs* , replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="3632e-313">코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-313">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="3632e-314">`FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-314">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="3632e-315">포함된 관련 데이터가 필요하지 않은 경우 `FindAsync`가 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-315">When included related data is not needed, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="3632e-316">`OnPostAsync`에는 `id` 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-316">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="3632e-317">현재 학생은 빈 학생을 만드는 대신 데이터베이스에서 페치합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-317">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="3632e-318">앱을 실행한 후 학생을 만들고 편집하여 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-318">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="3632e-319">엔터티 상태</span><span class="sxs-lookup"><span data-stu-id="3632e-319">Entity States</span></span>

<span data-ttu-id="3632e-320">데이터베이스 컨텍스트는 메모리의 엔터티가 해당하는 데이터베이스의 행과 동기화하는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-320">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="3632e-321">이 추적 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-321">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="3632e-322">예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-322">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="3632e-323">`SaveChangesAsync`가 호출되면 데이터베이스 컨텍스트가 SQL INSERT 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-323">When `SaveChangesAsync` is called, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="3632e-324">엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-324">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="3632e-325">`Added`: 엔터티가 아직 데이터베이스에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-325">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="3632e-326">`SaveChanges` 메서드가 INSERT 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-326">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="3632e-327">`Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-327">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="3632e-328">엔터티는 데이터베이스에서 읽을 때 이 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-328">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="3632e-329">`Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-329">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="3632e-330">`SaveChanges` 메서드는 UPDATE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-330">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="3632e-331">`Deleted`: 엔터티가 삭제되도록 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-331">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="3632e-332">`SaveChanges` 메서드는 DELETE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-332">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="3632e-333">`Detached`: 엔터티가 데이터베이스 컨텍스트에 의해 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-333">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="3632e-334">데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-334">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="3632e-335">엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-335">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="3632e-336">`SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL UPDATE 문이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-336">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="3632e-337">웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-337">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="3632e-338">페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-338">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="3632e-339">새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리가 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-339">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="3632e-340">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-340">Update the Delete page</span></span>

<span data-ttu-id="3632e-341">이 섹션에서는 `SaveChanges` 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-341">In this section, you implement a custom error message when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="3632e-342">*Pages/Students/Delete.cshtml.cs* 의 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-342">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="3632e-343">변경 내용이 강조 표시됩니다(`using` 문도 정리됨).</span><span class="sxs-lookup"><span data-stu-id="3632e-343">The changes are highlighted (other than cleanup of `using` statements).</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

<span data-ttu-id="3632e-344">앞의 코드는 선택적 매개 변수 `saveChangesError`를 `OnGetAsync` 메서드 시그니처에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-344">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="3632e-345">`saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-345">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="3632e-346">일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-346">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="3632e-347">일시적인 네트워크 오류는 데이터베이스가 클라우드에 있을 때 발생 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-347">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="3632e-348">`saveChangesError` 매개 변수는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 false입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-348">The `saveChangesError` parameter is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="3632e-349">`OnGetAsync`가 `OnPostAsync`에 의해 호출되면(삭제 작업이 실패했으므로) `saveChangesError` 매개 변수는 true입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-349">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

<span data-ttu-id="3632e-350">`OnPostAsync` 메서드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-350">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="3632e-351">`SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-351">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="3632e-352">`Remove`가 실패하는 경우:</span><span class="sxs-lookup"><span data-stu-id="3632e-352">If `Remove` fails:</span></span>

* <span data-ttu-id="3632e-353">데이터베이스 예외가 catch되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-353">The database exception is caught.</span></span>
* <span data-ttu-id="3632e-354">삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-354">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="3632e-355">삭제 :::no-loc(Razor)::: 페이지( *Pages/Students/Delete.cshtml* )에 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-355">Add an error message to the Delete :::no-loc(Razor)::: Page ( *Pages/Students/Delete.cshtml* ):</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="3632e-356">앱을 실행하고 학생을 삭제하여 삭제 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-356">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3632e-357">다음 단계</span><span class="sxs-lookup"><span data-stu-id="3632e-357">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3632e-358">[이전 자습서](xref:data/ef-rp/intro)
> [다음 자습서](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="3632e-358">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3632e-359">이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-359">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="3632e-360">복잡성을 최소화하고 이러한 자습서의 초점을 EF Core로 유지하기 위해 페이지 모델에 EF Core 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-360">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="3632e-361">일부 개발자는 UI(:::no-loc(Razor)::: Pages) 및 데이터 액세스 계층 간에 추상화 계층을 생성하도록 서비스 계층 또는 리포지토리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-361">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (:::no-loc(Razor)::: Pages) and the data access layer.</span></span>

<span data-ttu-id="3632e-362">이 자습서에서는 *Students* 폴더에서 만들기, 편집, 삭제 및 세부 정보 :::no-loc(Razor)::: Pages가 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-362">In this tutorial, the Create, Edit, Delete, and Details :::no-loc(Razor)::: Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="3632e-363">스캐폴드된 코드는 만들기, 편집 및 삭제 페이지에 대해 다음과 같은 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-363">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="3632e-364">HTTP GET 메서드 `OnGetAsync`로 요청된 데이터를 가져오고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-364">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="3632e-365">HTTP POST 메서드 `OnPostAsync`로 데이터의 변경 내용을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-365">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="3632e-366">인덱스 및 세부 정보 페이지는 HTTP GET 메서드 `OnGetAsync`로 요청된 데이터를 가져오고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-366">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="3632e-367">SingleOrDefaultAsync 및 FirstOrDefaultAsync</span><span class="sxs-lookup"><span data-stu-id="3632e-367">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="3632e-368">생성된 코드는 [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)보다 일반적으로 선호되는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-368">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="3632e-369">하나의 엔터티를 페치할 때 `SingleOrDefaultAsync`보다 `FirstOrDefaultAsync`가 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-369">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="3632e-370">쿼리에서 반환된 엔터티가 두 개 이상이 아닌지 코드에서 확인해야 하는 경우 제외.</span><span class="sxs-lookup"><span data-stu-id="3632e-370">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="3632e-371">`SingleOrDefaultAsync`는 더 많은 데이터를 페치하고 불필요한 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-371">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="3632e-372">`SingleOrDefaultAsync`는 필터 파트에 맞는 엔터티가 둘 이상인 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-372">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="3632e-373">`FirstOrDefaultAsync`는 필터 파트에 맞는 엔터티가 둘 이상인 경우 예외를 throw하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-373">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="3632e-374">FindAsync</span><span class="sxs-lookup"><span data-stu-id="3632e-374">FindAsync</span></span>

<span data-ttu-id="3632e-375">스캐폴드된 코드의 대부분에서 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___)를 `FirstOrDefaultAsync` 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-375">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="3632e-376">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="3632e-376">`FindAsync`:</span></span>

* <span data-ttu-id="3632e-377">PK(기본 키)가 있는 엔터티를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-377">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="3632e-378">PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 DB에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-378">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="3632e-379">단순하고 간결합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-379">Is simple and concise.</span></span>
* <span data-ttu-id="3632e-380">단일 엔터티를 조회하도록 최적화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-380">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="3632e-381">일부 상황에서는 성능 이점을 가질 수 있지만 일반적인 웹앱에서는 거의 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-381">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="3632e-382">[SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 대신 [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)를 암시적으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-382">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="3632e-383">하지만 다른 엔터티를 `Include`하려는 경우 `FindAsync`는 더 이상 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-383">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="3632e-384">즉, `FindAsync`를 취소하고 앱 진행에 따라 쿼리로 이동해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-384">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="3632e-385">세부 정보 사용자 지정 페이지</span><span class="sxs-lookup"><span data-stu-id="3632e-385">Customize the Details page</span></span>

<span data-ttu-id="3632e-386">`Pages/Students` 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-386">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="3632e-387">**편집** , **세부 정보** 및 **삭제** 링크는 *Pages/Students/Index.cshtml* 파일에서 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-387">The **Edit** , **Details** , and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="3632e-388">앱을 실행하고 **세부 정보** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-388">Run the app and select a **Details** link.</span></span> <span data-ttu-id="3632e-389">URL은 `http://localhost:5000/Students/Details?id=2` 양식입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-389">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="3632e-390">학생 ID는 쿼리 문자열(`?id=2`)을 사용하여 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-390">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="3632e-391">`"{id:int}"` 경로 템플릿을 사용하도록 편집, 세부 정보 및 삭제 :::no-loc(Razor)::: Pages를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-391">Update the Edit, Details, and Delete :::no-loc(Razor)::: Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="3632e-392">이러한 각 페이지에 대한 page 지시문을 `@page`에서 `@page "{id:int}"`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-392">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="3632e-393">정수 경로 값을 포함하지 **않는** “{id:int}” 경로 템플릿이 있는 페이지에 대한 요청은 HTTP 404(찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-393">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3632e-394">예를 들어 `http://localhost:5000/Students/Details`는 404 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-394">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="3632e-395">ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-395">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3632e-396">앱을 실행하고, 세부 정보 링크를 클릭하고, URL이 경로 데이터로 ID를 전달하는지 확인합니다(`http://localhost:5000/Students/Details/2`).</span><span class="sxs-lookup"><span data-stu-id="3632e-396">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="3632e-397">전체적으로 `@page`를 `@page "{id:int}"`로 변경하지 마십시오. 변경하면 홈 및 만들기 페이지에 대한 링크가 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-397">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="3632e-398">관련 데이터 추가</span><span class="sxs-lookup"><span data-stu-id="3632e-398">Add related data</span></span>

<span data-ttu-id="3632e-399">학생 인덱스 페이지에 대한 스캐폴드된 코드는 `Enrollments` 속성을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-399">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="3632e-400">이 섹션에서 `Enrollments` 컬렉션의 콘텐츠는 세부 정보 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-400">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="3632e-401">*Pages/Students/Details.cshtml.cs* 의 `OnGetAsync` 메서드는 `FirstOrDefaultAsync` 메서드를 사용하여 단일 `Student` 엔터티를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-401">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="3632e-402">다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-402">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="3632e-403">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-403">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="3632e-404">이러한 메서드는 읽기 관련 데이터 자습서에서 자세히 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-404">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="3632e-405">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-405">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="3632e-406">`AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-406">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="3632e-407">세부 정보 페이지에 관련된 등록 표시</span><span class="sxs-lookup"><span data-stu-id="3632e-407">Display related enrollments on the Details page</span></span>

<span data-ttu-id="3632e-408">*Pages/Students/Details.cshtml* 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-408">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="3632e-409">등록의 목록을 표시하려면 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-409">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="3632e-410">코드를 붙여넣은 후 코드 들여쓰기가 잘못된 경우 CTRL-K-D를 눌러 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-410">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="3632e-411">위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-411">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="3632e-412">각 등록의 경우 강좌 제목과 등급을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-412">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="3632e-413">강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-413">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="3632e-414">앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-414">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="3632e-415">선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-415">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="3632e-416">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-416">Update the Create page</span></span>

<span data-ttu-id="3632e-417">다음 코드로 *Pages/Students/Create.cshtml.cs* 의 `OnPostAsync` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-417">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="3632e-418">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="3632e-418">TryUpdateModelAsync</span></span>

<span data-ttu-id="3632e-419">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 코드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-419">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="3632e-420">위의 코드에서 `TryUpdateModelAsync<Student>`는 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)의 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_PageContext) 속성에서 게시된 양식 값을 사용하여 `emptyStudent` 개체를 업데이트하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-420">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="3632e-421">`TryUpdateModelAsync`는 나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="3632e-421">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="3632e-422">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="3632e-422">In the preceding sample:</span></span>

* <span data-ttu-id="3632e-423">두 번째 인수(`"student", // Prefix`)는 값 조회에 사용하는 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-423">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="3632e-424">대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-424">It's not case sensitive.</span></span>
* <span data-ttu-id="3632e-425">게시된 양식 값은 [모델 바인딩](xref:mvc/models/model-binding)을 사용한 `Student` 모델의 형식으로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-425">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="3632e-426">초과 게시</span><span class="sxs-lookup"><span data-stu-id="3632e-426">Overposting</span></span>

<span data-ttu-id="3632e-427">게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-427">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="3632e-428">예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-428">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="3632e-429">앱에 만들기/업데이트 :::no-loc(Razor)::: 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-429">Even if the app doesn't have a `Secret` field on the create/update :::no-loc(Razor)::: Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="3632e-430">해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-430">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="3632e-431">원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-431">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="3632e-432">해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 DB에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-432">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="3632e-433">다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-433">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="3632e-435">값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-435">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="3632e-436">앱 디자이너는 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-436">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="3632e-437">뷰 모델</span><span class="sxs-lookup"><span data-stu-id="3632e-437">View model</span></span>

<span data-ttu-id="3632e-438">뷰 모델은 일반적으로 애플리케이션에서 사용되는 모델에 포함된 속성의 하위 집합을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-438">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="3632e-439">애플리케이션 모델은 흔히 도메인 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-439">The application model is often called the domain model.</span></span> <span data-ttu-id="3632e-440">도메인 모델은 일반적으로 DB의 해당 엔터티에 필요한 모든 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-440">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="3632e-441">뷰 모델은 UI 계층에 필요한 속성만 포함합니다(예: 만들기 페이지).</span><span class="sxs-lookup"><span data-stu-id="3632e-441">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="3632e-442">뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 :::no-loc(Razor)::: Pages 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-442">In addition to the view model, some apps use a binding model or input model to pass data between the :::no-loc(Razor)::: Pages page model class and the browser.</span></span> <span data-ttu-id="3632e-443">다음 `Student` 뷰 모델을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="3632e-443">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="3632e-444">뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-444">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="3632e-445">뷰 모델은 확인(표시)하거나 업데이트하는 속성만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-445">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="3632e-446">다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-446">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3632e-447">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-447">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="3632e-448">`SetValues`는 속성 이름 일치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-448">`SetValues` uses property name matching.</span></span> <span data-ttu-id="3632e-449">뷰 모델 형식은 모델 형식과 연결될 필요는 없으며 일치하는 속성만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-449">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="3632e-450">`StudentVM`을 사용하려면 `Student` 대신 `StudentVM`을 사용하도록 [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml)을 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-450">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="3632e-451">:::no-loc(Razor)::: Pages에서 `PageModel` 파생 클래스는 뷰 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-451">In :::no-loc(Razor)::: Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="3632e-452">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-452">Update the Edit page</span></span>

<span data-ttu-id="3632e-453">편집 페이지를 위한 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-453">Update the page model for the Edit page.</span></span> <span data-ttu-id="3632e-454">주요 변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-454">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="3632e-455">코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-455">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="3632e-456">`OnPostAsync`에는 선택적 `id` 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-456">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="3632e-457">현재 학생은 빈 학생을 만드는 대신 DB에서 페치됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-457">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="3632e-458">`FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-458">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="3632e-459">`FindAsync`는 기본 키의 엔터티를 선택하는 경우 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-459">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="3632e-460">자세한 내용은 [FindAsync](#FindAsync)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3632e-460">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="3632e-461">편집 및 만들기 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="3632e-461">Test the Edit and Create pages</span></span>

<span data-ttu-id="3632e-462">몇 가지 학생 엔터티를 만들고 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-462">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="3632e-463">엔터티 상태</span><span class="sxs-lookup"><span data-stu-id="3632e-463">Entity States</span></span>

<span data-ttu-id="3632e-464">DB 컨텍스트는 메모리의 엔터티가 해당하는 DB의 행과 동기화하는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-464">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="3632e-465">DB 컨텍스트 동기화 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-465">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="3632e-466">예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-466">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="3632e-467">`SaveChangesAsync`가 호출되면 DB 컨텍스트가 SQL INSERT 명령을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-467">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="3632e-468">엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-468">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="3632e-469">`Added`: 엔터티가 아직 DB에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-469">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="3632e-470">`SaveChanges` 메서드가 INSERT 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-470">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="3632e-471">`Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-471">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="3632e-472">엔터티는 DB에서 읽을 때 이 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-472">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="3632e-473">`Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-473">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="3632e-474">`SaveChanges` 메서드는 UPDATE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-474">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="3632e-475">`Deleted`: 엔터티가 삭제되도록 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-475">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="3632e-476">`SaveChanges` 메서드는 DELETE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-476">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="3632e-477">`Detached`: 엔터티가 DB 컨텍스트에서 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-477">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="3632e-478">데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-478">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="3632e-479">엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-479">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="3632e-480">`SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL UPDATE 문이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-480">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="3632e-481">웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-481">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="3632e-482">페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-482">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="3632e-483">새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리를 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-483">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="3632e-484">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-484">Update the Delete page</span></span>

<span data-ttu-id="3632e-485">이 섹션에서는 `SaveChanges`에 대한 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현하도록 코드가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-485">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="3632e-486">가능한 오류 메시지를 포함하는 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-486">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="3632e-487">`OnGetAsync` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-487">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="3632e-488">위의 코드는 선택적 매개 변수 `saveChangesError`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-488">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="3632e-489">`saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-489">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="3632e-490">일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-490">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="3632e-491">일시적인 네트워크 오류는 클라우드에서 발생 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-491">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="3632e-492">`saveChangesError`는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 false입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-492">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="3632e-493">`OnGetAsync`가 `OnPostAsync`에 의해 호출되면(삭제 작업이 실패했으므로) `saveChangesError` 매개 변수는 true입니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-493">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="3632e-494">삭제 페이지 OnPostAsync 메서드</span><span class="sxs-lookup"><span data-stu-id="3632e-494">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="3632e-495">`OnPostAsync`를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-495">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3632e-496">앞의 코드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-496">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="3632e-497">`SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-497">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="3632e-498">`Remove`가 실패하는 경우:</span><span class="sxs-lookup"><span data-stu-id="3632e-498">If `Remove` fails:</span></span>

* <span data-ttu-id="3632e-499">DB 예외가 catch되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-499">The DB exception is caught.</span></span>
* <span data-ttu-id="3632e-500">삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-500">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-no-locrazor-page"></a><span data-ttu-id="3632e-501">삭제 :::no-loc(Razor)::: 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3632e-501">Update the Delete :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="3632e-502">다음 강조 표시된 오류 메시지를 삭제 :::no-loc(Razor)::: 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-502">Add the following highlighted error message to the Delete :::no-loc(Razor)::: Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="3632e-503">삭제를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-503">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="3632e-504">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="3632e-504">Common errors</span></span>

<span data-ttu-id="3632e-505">Students/Index 또는 다른 링크가 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-505">Students/Index or other links don't work:</span></span>

<span data-ttu-id="3632e-506">:::no-loc(Razor)::: 페이지에 올바른 `@page` 지시문이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-506">Verify the :::no-loc(Razor)::: Page contains the correct `@page` directive.</span></span> <span data-ttu-id="3632e-507">예를 들어 Students/Index :::no-loc(Razor)::: 페이지는 경로 템플릿을 포함해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="3632e-507">For example, The Students/Index :::no-loc(Razor)::: Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="3632e-508">각 :::no-loc(Razor)::: 페이지는 `@page` 지시문을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3632e-508">Each :::no-loc(Razor)::: Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="3632e-509">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3632e-509">Additional resources</span></span>

* [<span data-ttu-id="3632e-510">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="3632e-510">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="3632e-511">[이전](xref:data/ef-rp/intro)
> [다음](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="3632e-511">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end
