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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061069"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="8ab20-103">6부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 관련 데이터 읽기</span><span class="sxs-lookup"><span data-stu-id="8ab20-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="8ab20-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ab20-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8ab20-105">이 자습서에서는 관련 데이터를 읽고 표시하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="8ab20-106">관련된 데이터는 EF Core에서 탐색 속성에 로드하는 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="8ab20-107">다음 그림은 이 자습서에 대해 완료된 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-107">The following illustrations show the completed pages for this tutorial:</span></span>

![과정 인덱스 페이지](read-related-data/_static/courses-index30.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="8ab20-110">즉시, 명시적 및 지연 로드</span><span class="sxs-lookup"><span data-stu-id="8ab20-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="8ab20-111">여러 가지 방법으로 EF Core가 관련 데이터를 엔터티의 탐색 속성에 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8ab20-112">[즉시 로드](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="8ab20-113">즉시 로드는 한 형식의 엔터티에 대한 쿼리가 관련 엔터티도 로드하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="8ab20-114">엔터티를 읽을 때 관련된 데이터가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="8ab20-115">이는 일반적으로 필요한 데이터를 모두 검색하는 단일 조인 쿼리를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8ab20-116">EF 코어는 일부 형식의 즉시 로드에 대해 여러 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="8ab20-117">여러 쿼리를 실행하는 것이 큰 단일 쿼리보다 더 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="8ab20-118">즉시 로드는 `Include` 및 `ThenInclude` 메서드로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="8ab20-120">즉시 로드는 컬렉션 탐색이 포함된 경우 여러 쿼리를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="8ab20-121">주 쿼리에 대해 한 개 쿼리</span><span class="sxs-lookup"><span data-stu-id="8ab20-121">One query for the main query</span></span> 
  * <span data-ttu-id="8ab20-122">로드 트리에서 각 컬렉션 "에지"에 대해 한 개 쿼리</span><span class="sxs-lookup"><span data-stu-id="8ab20-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="8ab20-123">`Load`를 사용한 별도 쿼리: 별도 쿼리로 데이터를 검색할 수 있으며, EF Core에서 탐색 속성을 “수정”합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="8ab20-124">“수정”한다는 것은 EF Core가 탐색 속성을 자동으로 채운다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="8ab20-125">`Load`로 쿼리를 구분하는 것은 즉시 로드보다 더 명시적인 로드입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="8ab20-127">**참고:** EF Core는 이전에 컨텍스트 인스턴스에 로드된 다른 엔터티로 탐색 속성을 자동으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8ab20-128">탐색 속성에 대한 데이터가 명시적으로 포함되지 *않더라도* 관련 엔터티의 일부 또는 전부가 이전에 로드된 경우에도 속성이 채워질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="8ab20-129">[명시적 로드](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="8ab20-130">엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ab20-131">필요할 때 관련된 데이터를 검색하기 위한 코드를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="8ab20-132">별도 쿼리가 있는 명시적 로드의 경우 여러 쿼리가 데이터베이스로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="8ab20-133">명시적 로드에서 코드는 로드될 탐색 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8ab20-134">`Load` 메서드를 사용하여 명시적 로드를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8ab20-135">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="8ab20-135">For example:</span></span>

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8ab20-137">[지연 로드](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ab20-138">엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ab20-139">탐색 속성에 처음으로 액세스하려고 할 때 해당 탐색 속성에 필요한 데이터가 자동으로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8ab20-140">탐색 속성에 처음으로 액세스할 때마다 쿼리가 데이터베이스에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="8ab20-141">개발자가 N+1 패턴을 사용하는 경우처럼 지연 로드를 사용하면 부모를 로드하고 자식을 열거하는 성능이 저하될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="8ab20-142">과정 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="8ab20-142">Create Course pages</span></span>

<span data-ttu-id="8ab20-143">`Course` 엔터티는 `Department` 엔터티가 포함된 탐색 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="8ab20-145">과정에 대해 할당된 부서의 이름을 표시하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="8ab20-146">관련된 `Department` 엔터티를 `Course.Department` 탐색 속성에 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="8ab20-147">`Department` 엔터티의 `Name` 속성에서 이름을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="8ab20-148">과정 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="8ab20-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab20-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab20-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ab20-150">다음 예외가 포함된 [학생 페이지 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="8ab20-151">*Pages/Courses* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="8ab20-152">모델 클래스에 `Course`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="8ab20-153">새 컨텍스트 클래스를 만드는 대신 기존 컨텍스트 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab20-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab20-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8ab20-155">*Pages/Courses* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="8ab20-156">다음 명령을 실행하여 과정 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="8ab20-157">**Windows:**</span><span class="sxs-lookup"><span data-stu-id="8ab20-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="8ab20-158">**Linux 또는 macOS:**</span><span class="sxs-lookup"><span data-stu-id="8ab20-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="8ab20-159">*Pages/Courses/Index.cshtml.cs* 를 열고 `OnGetAsync` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="8ab20-160">스캐폴딩 엔진은 `Department` 탐색 속성에 대한 즉시 로드를 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="8ab20-161">`Include` 메서드가 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="8ab20-162">앱을 실행하고 **과정** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="8ab20-163">Department 열에 도움이 되지 않는 `DepartmentID`가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="8ab20-164">부서 이름 표시</span><span class="sxs-lookup"><span data-stu-id="8ab20-164">Display the department name</span></span>

<span data-ttu-id="8ab20-165">다음 코드로 Pages/Courses/Index.cshtml.cs를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="8ab20-166">앞의 코드는 `Course` 속성을 `Courses`로 변경하고 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="8ab20-167">반환된 엔터티는 추적되지 않으므로 `AsNoTracking`이 성능을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="8ab20-168">현재 컨텍스트에서 업데이트되지 않으므로 엔터티를 추적할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="8ab20-169">다음 코드로 *Pages/Courses/Index.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="8ab20-170">스캐폴드 코드에 다음 변경 내용을 적용했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="8ab20-171">`Course` 속성 이름을 `Courses`로 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="8ab20-172">`CourseID` 속성 값을 보여 주는 **Number** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8ab20-173">일반적으로 최종 사용자에게 의미가 없으므로 기본적으로 기본 키는 스캐폴드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8ab20-174">그러나 이 경우 기본 키는 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="8ab20-175">부서 이름을 표시하도록 **Department** 열을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8ab20-176">코드는 `Department` 탐색 속성으로 로드되는 `Department` 엔터티의 `Name` 속성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8ab20-177">앱을 실행하고 **Courses(과정)** 탭을 선택하여 부서 이름이 있는 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![과정 인덱스 페이지](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="8ab20-179">Select로 관련된 데이터 로드</span><span class="sxs-lookup"><span data-stu-id="8ab20-179">Loading related data with Select</span></span>

<span data-ttu-id="8ab20-180">`OnGetAsync` 메서드는 `Include` 메서드로 관련된 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="8ab20-181">`Select` 메서드는 필요한 관련 데이터만 로드하는 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="8ab20-182">`Department.Name`과 같은 단일 항목에서는 SQL INNER JOIN을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="8ab20-183">컬렉션의 경우 다른 데이터베이스 액세스를 사용하지만 컬렉션의 `Include` 연산자도 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="8ab20-184">다음 코드는 `Select` 메서드로 관련된 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="8ab20-185">위의 코드는 엔터티 형식을 반환하지 않으므로 추적이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="8ab20-186">EF 추적에 대한 자세한 내용은 [추적 및 비 추적 쿼리](/ef/core/querying/tracking)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab20-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="8ab20-187">`CourseViewModel`는:</span><span class="sxs-lookup"><span data-stu-id="8ab20-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="8ab20-188">전체 예제는 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 및 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab20-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="8ab20-189">강사 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="8ab20-189">Create Instructor pages</span></span>

<span data-ttu-id="8ab20-190">이 섹션에서는 강사 페이지를 스캐폴드하고 강사 인덱스 페이지에 관련된 과정 및 등록을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="8ab20-191">![강사 인덱스 페이지](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="8ab20-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="8ab20-192">이 페이지는 다음과 같은 방법으로 관련된 데이터를 읽고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8ab20-193">강사 목록은 `OfficeAssignment` 엔터티에서 관련된 데이터를 표시합니다(이전 이미지에서 Office).</span><span class="sxs-lookup"><span data-stu-id="8ab20-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="8ab20-194">`Instructor` 및 `OfficeAssignment` 엔터티는 일대영 또는 일 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8ab20-195">즉시 로드는 `OfficeAssignment` 엔터티에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="8ab20-196">즉시 로드는 일반적으로 관련된 데이터를 표시해야 할 때 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="8ab20-197">이 경우 강사를 위한 사무실 할당이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="8ab20-198">사용자가 강사를 선택하면 관련된 `Course` 엔터티가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="8ab20-199">`Instructor` 및 `Course` 엔터티는 다대다 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8ab20-200">`Course` 엔터티 및 관련 `Department` 엔터티에 대해 즉시 로드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="8ab20-201">이 경우 선택한 강사에 대한 과정만 필요하므로 별도 쿼리가 더 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="8ab20-202">이 예제에서는 탐색 속성에 있는 엔터티에서 탐색 속성에 대한 즉시 로드를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="8ab20-203">사용자가 과정을 선택하면 `Enrollments` 엔터티의 관련 데이터가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="8ab20-204">이전 이미지에서는 학생 이름 및 학점이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="8ab20-205">`Course` 및 `Enrollment` 엔터티는 일대다 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="8ab20-206">뷰 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="8ab20-206">Create a view model</span></span>

<span data-ttu-id="8ab20-207">강사 페이지는 서로 다른 세 테이블의 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="8ab20-208">세 개의 테이블을 나타내는 세 개의 속성을 포함하는 뷰 모델이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="8ab20-209">다음 코드로 *SchoolViewModels/InstructorIndexData.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="8ab20-210">강사 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="8ab20-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab20-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab20-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ab20-212">다음 예외가 포함된 [학생 페이지 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="8ab20-213">*Pages/Instructors* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="8ab20-214">모델 클래스에 `Instructor`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="8ab20-215">새 컨텍스트 클래스를 만드는 대신 기존 컨텍스트 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab20-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab20-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8ab20-217">*Pages/Instructors* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="8ab20-218">다음 명령을 실행하여 강사 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="8ab20-219">**Windows:**</span><span class="sxs-lookup"><span data-stu-id="8ab20-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="8ab20-220">**Linux 또는 macOS:**</span><span class="sxs-lookup"><span data-stu-id="8ab20-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ab20-221">업데이트하기 전에 스캐폴드된 페이지의 모양을 확인하려면 앱을 실행하고 강사 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="8ab20-222">다음 코드로 *Pages/Instructors/Index.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="8ab20-223">`OnGetAsync` 메서드는 선택한 강사의 ID에 대해 경로 데이터(선택 사항)를 받아들입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="8ab20-224">*Pages/Instructors/Index.cshtml.cs* 파일에서 쿼리를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="8ab20-225">이 코드는 다음 탐색 속성에 대한 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="8ab20-226">`CourseAssignments` 및 `Course`에 대해 `Include` 및 `ThenInclude` 메서드가 반복되는 것을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="8ab20-227">이 반복은 `Course` 엔터티의 두 가지 탐색 속성에 대해 즉시 로드를 지정하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="8ab20-228">다음 코드는 강사가 선택되었을 때 실행됩니다(`id != null`).</span><span class="sxs-lookup"><span data-stu-id="8ab20-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="8ab20-229">선택한 강사는 뷰 모델의 강사 목록에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8ab20-230">뷰 모델의 `Courses` 속성은 해당 강사의 `CourseAssignments` 탐색 속성에서 `Course` 엔터티로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="8ab20-231">`Where` 메서드는 컬렉션도 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="8ab20-232">그러나 이 경우 필터는 단일 엔터티를 선택하므로 `Single` 메서드를 호출하여 컬렉션을 단일 `Instructor` 엔터티로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="8ab20-233">`Instructor` 엔터티는 `CourseAssignments` 속성에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="8ab20-234">`CourseAssignments`는 관련 `Course` 엔터티에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![강사 및 과정 간 관계 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="8ab20-236">`Single` 메서드는 컬렉션에 한 개 항목만 있을 때 컬렉션에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="8ab20-237">`Single` 메서드는 컬렉션이 비어 있거나 둘 이상의 항목이 있는 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="8ab20-238">대안은 `SingleOrDefault`입니다. 컬렉션이 비어 있는 경우 기본값을 반환합니다(이 경우 Null).</span><span class="sxs-lookup"><span data-stu-id="8ab20-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="8ab20-239">다음 코드는 과정을 선택할 때 뷰 모델의 `Enrollments` 속성을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="8ab20-240">강사 인덱스 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="8ab20-240">Update the instructors Index page</span></span>

<span data-ttu-id="8ab20-241">다음 코드로 *Pages/Instructors/Index.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="8ab20-242">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="8ab20-243">`page` 지시어를 `@page`에서 `@page "{id:int?}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="8ab20-244">`"{id:int?}"`는 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="8ab20-245">경로 템플릿은 데이터를 라우팅할 URL에서 정수 쿼리 문자열을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="8ab20-246">예를 들어, `@page` 지시문만 있는 강사에 대해 **Select** 링크를 클릭하면 다음과 같은 URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="8ab20-247">페이지 지시문이 `@page "{id:int?}"`이면 URL은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="8ab20-248">`item.OfficeAssignment`가 Null이 아닌 경우에만 `item.OfficeAssignment.Location`을 표시하는 **Office** 열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8ab20-249">이는 일대영 또는 일 관계이기 때문에 관련된 OfficeAssignment 엔터티가 있을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="8ab20-250">각 강사가 가르치는 과정을 표시하는 **Courses** 열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8ab20-251">이 razor 구문에 대한 자세한 내용은 [명시적 줄 전환](xref:mvc/views/razor#explicit-line-transition)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab20-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="8ab20-252">선택된 강사 및 과정의 `tr` 요소에 `class="success"`를 동적으로 추가하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="8ab20-253">부트스트랩 클래스를 사용하여 선택된 행에 대한 배경색을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="8ab20-254">**Select** 로 레이블 지정된 새 하이퍼링크를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="8ab20-255">이 링크는 선택한 강사의 ID를 `Index` 메서드에 보내고 배경색을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="8ab20-256">선택된 강사에 대한 과정 테이블을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="8ab20-257">선택된 과정에 대한 학생 등록 테이블을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="8ab20-258">앱을 실행하고 **강사** 탭을 선택합니다. 페이지에 관련된 `OfficeAssignment` 엔터티의 `Location`(사무실)이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="8ab20-259">`OfficeAssignment`가 Null이면 빈 테이블 셀이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="8ab20-260">강사의 **Select** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="8ab20-261">강사에게 할당된 행 스타일 변경 내용 및 과정이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="8ab20-262">과정을 선택하여 등록된 학생 및 해당 등급의 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-262">Select a course to see the list of enrolled students and their grades.</span></span>

![강사 및 과정이 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="8ab20-264">Single 사용</span><span class="sxs-lookup"><span data-stu-id="8ab20-264">Using Single</span></span>

<span data-ttu-id="8ab20-265">`Single` 메서드는 `Where` 메서드를 별도로 호출하는 대신 `Where` 조건을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="8ab20-266">Where 조건과 함께 `Single`을 사용하는 것은 개인적으로 선호하는 방법을 선택하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="8ab20-267">이 방법은 `Where` 메서드를 사용하는 것보다 장점이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="8ab20-268">명시적 로드</span><span class="sxs-lookup"><span data-stu-id="8ab20-268">Explicit loading</span></span>

<span data-ttu-id="8ab20-269">현재 코드는 `Enrollments` 및 `Students`에 대한 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="8ab20-270">사용자가 과정에 등록된 내용을 거의 보지 않는다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="8ab20-271">이 경우 최적화는 요청된 경우에만 등록 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="8ab20-272">이 섹션에서는 `Enrollments` 및 `Students`의 명시적 로드를 사용하도록 `OnGetAsync`가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="8ab20-273">다음 코드로 *Pages/Instructors/Index.cshtml.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="8ab20-274">위의 코드는 등록 및 학생 데이터에 대한 *ThenInclude* 메서드 호출을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="8ab20-275">과정을 선택하면 명시적 로드 코드가 다음을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="8ab20-276">선택한 과정에 대한 `Enrollment` 엔터티</span><span class="sxs-lookup"><span data-stu-id="8ab20-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="8ab20-277">각 `Enrollment`에 대한 `Student` 엔터티</span><span class="sxs-lookup"><span data-stu-id="8ab20-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="8ab20-278">앞의 코드는 `.AsNoTracking()`을 주석으로 처리하는 것을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="8ab20-279">탐색 속성은 추적된 엔터티에 대해서만 명시적으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="8ab20-280">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-280">Test the app.</span></span> <span data-ttu-id="8ab20-281">사용자 관점에서 앱은 이전 버전과 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ab20-282">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8ab20-282">Next steps</span></span>

<span data-ttu-id="8ab20-283">다음 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="8ab20-284">[이전 자습서](xref:data/ef-rp/complex-data-model)
>[다음 자습서](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="8ab20-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8ab20-285">이 자습서에서는 관련된 데이터를 읽고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="8ab20-286">관련된 데이터는 EF Core에서 탐색 속성에 로드하는 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="8ab20-287">해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="8ab20-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="8ab20-288">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="8ab20-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8ab20-289">다음 그림은 이 자습서에 대해 완료된 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-289">The following illustrations show the completed pages for this tutorial:</span></span>

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="8ab20-292">관련된 데이터의 즉시, 명시적 및 지연 로드</span><span class="sxs-lookup"><span data-stu-id="8ab20-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="8ab20-293">여러 가지 방법으로 EF Core가 관련 데이터를 엔터티의 탐색 속성에 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8ab20-294">[즉시 로드](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="8ab20-295">즉시 로드는 한 형식의 엔터티에 대한 쿼리가 관련 엔터티도 로드하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="8ab20-296">엔터티를 읽을 때 관련된 데이터가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="8ab20-297">이는 일반적으로 필요한 데이터를 모두 검색하는 단일 조인 쿼리를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8ab20-298">EF 코어는 일부 형식의 즉시 로드에 대해 여러 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="8ab20-299">여러 쿼리를 실행하는 것이 단일 쿼리가 있는 EF6의 일부 쿼리보다 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="8ab20-300">즉시 로드는 `Include` 및 `ThenInclude` 메서드로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="8ab20-302">즉시 로드는 컬렉션 탐색이 포함된 경우 여러 쿼리를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="8ab20-303">주 쿼리에 대해 한 개 쿼리</span><span class="sxs-lookup"><span data-stu-id="8ab20-303">One query for the main query</span></span> 
  * <span data-ttu-id="8ab20-304">로드 트리에서 각 컬렉션 "에지"에 대해 한 개 쿼리</span><span class="sxs-lookup"><span data-stu-id="8ab20-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="8ab20-305">`Load`를 사용한 별도 쿼리: 별도 쿼리로 데이터를 검색할 수 있으며, EF Core에서 탐색 속성을 “수정”합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="8ab20-306">"수정"한다는 것은 EF Core가 탐색 속성을 자동으로 채운다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="8ab20-307">`Load`로 쿼리를 구분하는 것은 즉시 로드보다 더 명시적인 로드입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="8ab20-309">참고: EF Core는 이전에 컨텍스트 인스턴스에 로드된 다른 엔터티로 탐색 속성을 자동으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8ab20-310">탐색 속성에 대한 데이터가 명시적으로 포함되지 *않더라도* 관련 엔터티의 일부 또는 전부가 이전에 로드된 경우에도 속성이 채워질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="8ab20-311">[명시적 로드](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="8ab20-312">엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ab20-313">필요할 때 관련된 데이터를 검색하기 위한 코드를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="8ab20-314">별도 쿼리가 있는 명시적 로드의 경우 여러 쿼리가 DB로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="8ab20-315">명시적 로드에서 코드는 로드될 탐색 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8ab20-316">`Load` 메서드를 사용하여 명시적 로드를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8ab20-317">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="8ab20-317">For example:</span></span>

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8ab20-319">[지연 로드](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ab20-320">[지연 로드가 버전 2.1의 EF Core에 추가되었습니다](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="8ab20-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ab20-321">엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ab20-322">탐색 속성에 처음으로 액세스하려고 할 때 해당 탐색 속성에 필요한 데이터가 자동으로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8ab20-323">탐색 속성에 처음으로 액세스할 때마다 쿼리가 DB에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="8ab20-324">`Select` 연산자는 필요한 관련된 데이터만 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="8ab20-325">부서 이름을 표시하는 과정 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="8ab20-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="8ab20-326">과정 엔터티는 `Department` 엔터티가 포함된 탐색 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="8ab20-327">`Department` 엔터티는 과정이 할당된 부서를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="8ab20-328">과정 목록에 할당된 부서 이름을 표시하려면</span><span class="sxs-lookup"><span data-stu-id="8ab20-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="8ab20-329">`Department` 엔터티에서 `Name` 속성을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="8ab20-330">`Department` 엔터티는 `Course.Department` 탐색 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="8ab20-332">과정 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="8ab20-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab20-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab20-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="8ab20-334">[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Course`를 모델 클래스로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab20-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab20-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="8ab20-336">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ab20-337">위의 명령은 `Course` 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="8ab20-338">Visual Studio에서 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="8ab20-339">*Pages/Courses/Index.cshtml.cs* 를 열고 `OnGetAsync` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="8ab20-340">스캐폴딩 엔진은 `Department` 탐색 속성에 대한 즉시 로드를 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="8ab20-341">`Include` 메서드가 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="8ab20-342">앱을 실행하고 **과정** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="8ab20-343">Department 열에 도움이 되지 않는 `DepartmentID`가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="8ab20-344">`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="8ab20-345">위의 코드는 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="8ab20-346">반환된 엔터티는 추적되지 않으므로 `AsNoTracking`이 성능을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="8ab20-347">현재 컨텍스트에서 업데이트되지 않으므로 엔터티가 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="8ab20-348">*Pages/Courses/Index.cshtml* 을 다음 강조 표시된 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="8ab20-349">스캐폴드 코드에 다음 변경 내용을 적용했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="8ab20-350">제목을 Index(인덱스)에서 Courses(과정)로 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="8ab20-351">`CourseID` 속성 값을 보여 주는 **Number** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8ab20-352">일반적으로 최종 사용자에게 의미가 없으므로 기본적으로 기본 키는 스캐폴드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8ab20-353">그러나 이 경우 기본 키는 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="8ab20-354">부서 이름을 표시하도록 **Department** 열을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8ab20-355">코드는 `Department` 탐색 속성으로 로드되는 `Department` 엔터티의 `Name` 속성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8ab20-356">앱을 실행하고 **Courses(과정)** 탭을 선택하여 부서 이름이 있는 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="8ab20-358">Select로 관련된 데이터 로드</span><span class="sxs-lookup"><span data-stu-id="8ab20-358">Loading related data with Select</span></span>

<span data-ttu-id="8ab20-359">`OnGetAsync` 메서드는 `Include` 메서드로 관련된 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="8ab20-360">`Select` 연산자는 필요한 관련된 데이터만 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="8ab20-361">`Department.Name`과 같은 단일 항목에서는 SQL INNER JOIN을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="8ab20-362">컬렉션의 경우 다른 데이터베이스 액세스를 사용하지만 컬렉션의 `Include` 연산자도 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="8ab20-363">다음 코드는 `Select` 메서드로 관련된 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="8ab20-364">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="8ab20-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="8ab20-365">전체 예제는 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 및 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab20-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="8ab20-366">과정 및 등록을 보여 주는 강사 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="8ab20-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="8ab20-367">이 섹션에서는 강사 페이지가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="8ab20-368">![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="8ab20-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="8ab20-369">이 페이지는 다음과 같은 방법으로 관련된 데이터를 읽고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8ab20-370">강사 목록은 `OfficeAssignment` 엔터티에서 관련된 데이터를 표시합니다(이전 이미지에서 Office).</span><span class="sxs-lookup"><span data-stu-id="8ab20-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="8ab20-371">`Instructor` 및 `OfficeAssignment` 엔터티는 일대영 또는 일 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8ab20-372">즉시 로드는 `OfficeAssignment` 엔터티에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="8ab20-373">즉시 로드는 일반적으로 관련된 데이터를 표시해야 할 때 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="8ab20-374">이 경우 강사를 위한 사무실 할당이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="8ab20-375">사용자가 강사(이전 이미지에서 Harui)를 선택하는 경우 관련된 `Course` 엔터티가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="8ab20-376">`Instructor` 및 `Course` 엔터티는 다대다 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8ab20-377">`Course` 엔터티 및 관련 `Department` 엔터티에 대해 즉시 로드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="8ab20-378">이 경우 선택한 강사에 대한 과정만 필요하므로 별도 쿼리가 더 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="8ab20-379">이 예제에서는 탐색 속성에 있는 엔터티에서 탐색 속성에 대한 즉시 로드를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="8ab20-380">사용자가 과정(이전 이미지에서 Chemistry)을 선택하면 `Enrollments` 엔터티의 관련된 데이터가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="8ab20-381">이전 이미지에서는 학생 이름 및 학점이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="8ab20-382">`Course` 및 `Enrollment` 엔터티는 일대다 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="8ab20-383">강사 인덱스 뷰에 대한 뷰 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="8ab20-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="8ab20-384">강사 페이지는 서로 다른 세 테이블의 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="8ab20-385">세 개 테이블을 나타내는 세 개 엔터티가 포함된 뷰 모델이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="8ab20-386">다음 코드로 *SchoolViewModels* 폴더에서 *InstructorIndexData.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="8ab20-387">강사 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="8ab20-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ab20-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ab20-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="8ab20-389">[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Instructor`를 모델 클래스로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ab20-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ab20-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="8ab20-391">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ab20-392">위의 명령은 `Instructor` 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="8ab20-393">앱을 실행하고 강사 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="8ab20-394">*Pages/Instructors/Index.cshtml.cs* 를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="8ab20-395">`OnGetAsync` 메서드는 선택한 강사의 ID에 대해 경로 데이터(선택 사항)를 받아들입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="8ab20-396">*Pages/Instructors/Index.cshtml.cs* 파일에서 쿼리를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="8ab20-397">쿼리에는 다음 두 include가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-397">The query has two includes:</span></span>

* <span data-ttu-id="8ab20-398">`OfficeAssignment`: [강사 뷰](#IP)에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="8ab20-399">`CourseAssignments`: 가르친 과정을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="8ab20-400">강사 인덱스 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="8ab20-400">Update the instructors Index page</span></span>

<span data-ttu-id="8ab20-401">*Pages/Instructors/Index.cshtml* 을 다음 표시로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="8ab20-402">위의 표시로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="8ab20-403">`page` 지시문을 `@page`에서 `@page "{id:int?}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="8ab20-404">`"{id:int?}"`는 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="8ab20-405">경로 템플릿은 데이터를 라우팅할 URL에서 정수 쿼리 문자열을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="8ab20-406">예를 들어, `@page` 지시문만 있는 강사에 대해 **Select** 링크를 클릭하면 다음과 같은 URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="8ab20-407">page 지시문이 `@page "{id:int?}"`이면, 이전 URL은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="8ab20-408">페이지 제목은 **Instructors(강사)** 입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="8ab20-409">`item.OfficeAssignment`가 Null이 아닌 경우에만 `item.OfficeAssignment.Location`을 표시하는 **Office** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8ab20-410">이는 일대영 또는 일 관계이기 때문에 관련된 OfficeAssignment 엔터티가 있을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="8ab20-411">각 강사가 가르치는 과정을 표시하는 **Courses** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8ab20-412">이 razor 구문에 대한 자세한 내용은 [명시적 줄 전환](xref:mvc/views/razor#explicit-line-transition)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab20-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="8ab20-413">선택된 강사의 `tr` 요소에 `class="success"`를 동적으로 추가하는 코드를 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="8ab20-414">부트스트랩 클래스를 사용하여 선택된 행에 대한 배경색을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="8ab20-415">**Select** 로 레이블 지정된 새 하이퍼링크를 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="8ab20-416">이 링크는 선택한 강사의 ID를 `Index` 메서드에 보내고 배경색을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="8ab20-417">앱을 실행하고 **강사** 탭을 선택합니다. 페이지에 관련된 `OfficeAssignment` 엔터티의 `Location`(사무실)이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="8ab20-418">OfficeAssignment가 Null이면 빈 테이블 셀이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="8ab20-419">**Select** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-419">Click on the **Select** link.</span></span> <span data-ttu-id="8ab20-420">행 스타일이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="8ab20-421">선택한 강사가 가르친 과정 추가</span><span class="sxs-lookup"><span data-stu-id="8ab20-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="8ab20-422">*Pages/Instructors/Index.cshtml.cs* 에서 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="8ab20-423">`public int CourseID { get; set; }` 추가</span><span class="sxs-lookup"><span data-stu-id="8ab20-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="8ab20-424">업데이트된 쿼리를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="8ab20-425">이전 쿼리는 `Department` 엔터티를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="8ab20-426">다음 코드는 강사가 선택되었을 때 실행됩니다(`id != null`).</span><span class="sxs-lookup"><span data-stu-id="8ab20-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="8ab20-427">선택한 강사는 뷰 모델의 강사 목록에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8ab20-428">뷰 모델의 `Courses` 속성은 해당 강사의 `CourseAssignments` 탐색 속성에서 `Course` 엔터티로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="8ab20-429">`Where` 메서드는 컬렉션도 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="8ab20-430">앞의 `Where` 메서드에서 단일 `Instructor` 엔터티만 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="8ab20-431">`Single` 메서드는 컬렉션을 단일 `Instructor` 엔터티로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="8ab20-432">`Instructor` 엔터티는 `CourseAssignments` 속성에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="8ab20-433">`CourseAssignments`는 관련 `Course` 엔터티에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![강사 및 과정 간 관계 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="8ab20-435">`Single` 메서드는 컬렉션에 한 개 항목만 있을 때 컬렉션에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="8ab20-436">`Single` 메서드는 컬렉션이 비어 있거나 둘 이상의 항목이 있는 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="8ab20-437">대안은 `SingleOrDefault`입니다. 컬렉션이 비어 있는 경우 기본값을 반환합니다(이 경우 Null).</span><span class="sxs-lookup"><span data-stu-id="8ab20-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="8ab20-438">비어 있는 컬렉션에 `SingleOrDefault` 사용</span><span class="sxs-lookup"><span data-stu-id="8ab20-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="8ab20-439">예외가 발생합니다(null 참조에서 `Courses` 속성을 찾으려고 시도하므로).</span><span class="sxs-lookup"><span data-stu-id="8ab20-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="8ab20-440">예외 메시지로는 문제의 원인을 명확하게 알기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="8ab20-441">다음 코드는 과정을 선택할 때 뷰 모델의 `Enrollments` 속성을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="8ab20-442">다음 태그를 *Pages/Instructors/Index.cshtml* Razor 페이지 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="8ab20-443">위의 표시는 강사가 선택된 경우 강사와 관련된 과정의 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="8ab20-444">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-444">Test the app.</span></span> <span data-ttu-id="8ab20-445">강사 페이지에서 **Select** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="8ab20-446">학생 데이터 표시</span><span class="sxs-lookup"><span data-stu-id="8ab20-446">Show student data</span></span>

<span data-ttu-id="8ab20-447">이 섹션에서는 선택한 과정에 대한 학생 데이터를 표시하도록 앱이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="8ab20-448">*Pages/Instructors/Index.cshtml.cs* 에서 `OnGetAsync` 메서드의 쿼리를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="8ab20-449">*Pages/Instructors/Index.cshtml* 을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="8ab20-450">다음 표시를 파일 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="8ab20-451">이전 표시는 선택한 과정을 등록한 학생의 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="8ab20-452">페이지를 새로 고치고 강사를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="8ab20-453">과정을 선택하여 등록된 학생 및 해당 등급의 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-453">Select a course to see the list of enrolled students and their grades.</span></span>

![강사 및 과정이 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="8ab20-455">Single 사용</span><span class="sxs-lookup"><span data-stu-id="8ab20-455">Using Single</span></span>

<span data-ttu-id="8ab20-456">`Single` 메서드는 `Where` 메서드를 별도로 호출하는 대신 `Where` 조건을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="8ab20-457">위의 `Single` 접근 방식은 `Where`를 사용하는 것보다 장점을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="8ab20-458">일부 개발자는 `Single` 방식을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="8ab20-459">명시적 로드</span><span class="sxs-lookup"><span data-stu-id="8ab20-459">Explicit loading</span></span>

<span data-ttu-id="8ab20-460">현재 코드는 `Enrollments` 및 `Students`에 대한 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="8ab20-461">사용자가 과정에 등록된 내용을 거의 보지 않는다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="8ab20-462">이 경우 최적화는 요청된 경우에만 등록 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="8ab20-463">이 섹션에서는 `Enrollments` 및 `Students`의 명시적 로드를 사용하도록 `OnGetAsync`가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="8ab20-464">`OnGetAsync`를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="8ab20-465">위의 코드는 등록 및 학생 데이터에 대한 *ThenInclude* 메서드 호출을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="8ab20-466">과정을 선택하면 강조 표시된 코드가 다음을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="8ab20-467">선택한 과정에 대한 `Enrollment` 엔터티</span><span class="sxs-lookup"><span data-stu-id="8ab20-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="8ab20-468">각 `Enrollment`에 대한 `Student` 엔터티</span><span class="sxs-lookup"><span data-stu-id="8ab20-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="8ab20-469">위의 코드에서는 `.AsNoTracking()`을 주석 처리했습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="8ab20-470">탐색 속성은 추적된 엔터티에 대해서만 명시적으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="8ab20-471">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-471">Test the app.</span></span> <span data-ttu-id="8ab20-472">사용자 관점에서 앱은 이전 버전과 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="8ab20-473">다음 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab20-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ab20-474">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8ab20-474">Additional resources</span></span>

* [<span data-ttu-id="8ab20-475">이 자습서의 YouTube 버전(1부)</span><span class="sxs-lookup"><span data-stu-id="8ab20-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="8ab20-476">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="8ab20-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="8ab20-477">[이전](xref:data/ef-rp/complex-data-model)
>[다음](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="8ab20-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
