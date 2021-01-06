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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053984"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="92699-104">자습서: 정렬, 필터링 및 페이징 추가 - ASP.NET MVC 및 EF Core 사용</span><span class="sxs-lookup"><span data-stu-id="92699-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="92699-105">이전 자습서에서는 학생 엔터티에 대한 기본적인 CRUD 작업을 위한 일련의 웹 페이지를 구현했습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="92699-106">이 자습서에서는 학생 인덱스 페이지에 정렬, 필터링 및 페이징 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="92699-107">단순 그룹화를 수행하는 페이지도 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92699-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="92699-108">다음 그림에서는 작업이 완료되었을 때 페이지 모양을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="92699-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="92699-109">열 제목은 해당 열로 정렬하기 위해 사용자가 클릭할 수 있는 링크입니다.</span><span class="sxs-lookup"><span data-stu-id="92699-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="92699-110">열 제목을 반복해서 클릭하면 오름차순 및 내림차순으로 정렬 순서가 토글됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![학생 인덱스 페이지](sort-filter-page/_static/paging.png)

<span data-ttu-id="92699-112">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92699-113">열 정렬 링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-113">Add column sort links</span></span>
> * <span data-ttu-id="92699-114">검색 상자 추가</span><span class="sxs-lookup"><span data-stu-id="92699-114">Add a Search box</span></span>
> * <span data-ttu-id="92699-115">학생 인덱스에 페이징 추가</span><span class="sxs-lookup"><span data-stu-id="92699-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="92699-116">Index 메서드에 페이징 추가</span><span class="sxs-lookup"><span data-stu-id="92699-116">Add paging to Index method</span></span>
> * <span data-ttu-id="92699-117">페이징 링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-117">Add paging links</span></span>
> * <span data-ttu-id="92699-118">정보 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="92699-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="92699-119">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="92699-119">Prerequisites</span></span>

* [<span data-ttu-id="92699-120">CRUD 기능 구현</span><span class="sxs-lookup"><span data-stu-id="92699-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="92699-121">열 정렬 링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-121">Add column sort links</span></span>

<span data-ttu-id="92699-122">Student 인덱스 페이지에 정렬을 추가하려면 Students 컨트롤러의 `Index` 메서드를 변경하고 Student 인덱스 뷰에 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="92699-123">Index 메서드에 정렬 기능 추가</span><span class="sxs-lookup"><span data-stu-id="92699-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="92699-124">*StudentsController.cs* 에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="92699-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="92699-125">이 코드는 URL의 쿼리 문자열에서 `sortOrder` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="92699-126">쿼리 문자열 값은 매개 변수로 ASP.NET Core MVC에 의해 작업 메서드에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="92699-127">매개 변수는 "Name" 또는 "Date" 문자열이며 필요에 따라 밑줄과 내림차순을 지정하는 문자열 "desc"가 옵니다.</span><span class="sxs-lookup"><span data-stu-id="92699-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="92699-128">기본 정렬 순서는 오름차순입니다.</span><span class="sxs-lookup"><span data-stu-id="92699-128">The default sort order is ascending.</span></span>

<span data-ttu-id="92699-129">인덱스 페이지를 처음 요청하면 쿼리 문자열은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="92699-130">학생은 성을 기준으로 오름차순으로 표시되며 이것은 `switch` 문의 제어 이동 사례로 설정된 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="92699-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="92699-131">사용자가 열 제목 하이퍼링크를 클릭하면 쿼리 문자열에 해당 `sortOrder` 값이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="92699-132">열 제목 하이퍼링크를 적절한 쿼리 문자열 값으로 구성하기 위해 뷰에 두 `ViewData` 요소(NameSortParm 및 DateSortParm)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="92699-133">이것은 3개로 구성된 문입니다.</span><span class="sxs-lookup"><span data-stu-id="92699-133">These are ternary statements.</span></span> <span data-ttu-id="92699-134">첫 번째 문은 `sortOrder` 매개 변수는 Null인지, 비어 있는지 여부를 지정하고 Null이면 NameSortParm을 "name_desc"로 설정하고, 그렇지 않으면 빈 문자열로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="92699-135">이러한 두 문을 사용하면 뷰에서 다음과 같이 열 제목 하이퍼링크를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="92699-136">현재 정렬 순서</span><span class="sxs-lookup"><span data-stu-id="92699-136">Current sort order</span></span>  | <span data-ttu-id="92699-137">성 하이퍼링크</span><span class="sxs-lookup"><span data-stu-id="92699-137">Last Name Hyperlink</span></span> | <span data-ttu-id="92699-138">날짜 하이퍼링크</span><span class="sxs-lookup"><span data-stu-id="92699-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="92699-139">성 오름차순</span><span class="sxs-lookup"><span data-stu-id="92699-139">Last Name ascending</span></span>  | <span data-ttu-id="92699-140">descending</span><span class="sxs-lookup"><span data-stu-id="92699-140">descending</span></span>          | <span data-ttu-id="92699-141">ascending</span><span class="sxs-lookup"><span data-stu-id="92699-141">ascending</span></span>      |
| <span data-ttu-id="92699-142">성 내림차순</span><span class="sxs-lookup"><span data-stu-id="92699-142">Last Name descending</span></span> | <span data-ttu-id="92699-143">ascending</span><span class="sxs-lookup"><span data-stu-id="92699-143">ascending</span></span>           | <span data-ttu-id="92699-144">ascending</span><span class="sxs-lookup"><span data-stu-id="92699-144">ascending</span></span>      |
| <span data-ttu-id="92699-145">날짜 오름차순</span><span class="sxs-lookup"><span data-stu-id="92699-145">Date ascending</span></span>       | <span data-ttu-id="92699-146">ascending</span><span class="sxs-lookup"><span data-stu-id="92699-146">ascending</span></span>           | <span data-ttu-id="92699-147">descending</span><span class="sxs-lookup"><span data-stu-id="92699-147">descending</span></span>     |
| <span data-ttu-id="92699-148">날짜 내림차순</span><span class="sxs-lookup"><span data-stu-id="92699-148">Date descending</span></span>      | <span data-ttu-id="92699-149">ascending</span><span class="sxs-lookup"><span data-stu-id="92699-149">ascending</span></span>           | <span data-ttu-id="92699-150">ascending</span><span class="sxs-lookup"><span data-stu-id="92699-150">ascending</span></span>      |

<span data-ttu-id="92699-151">메서드는 LINQ to Entities를 사용하여 정렬할 기준이 되는 열을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="92699-152">이 코드는 switch 문 앞에 `IQueryable` 변수를 만들고 switch 문에서 변수를 수정한 다음, `switch` 문 다음에 `ToListAsync` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="92699-153">`IQueryable` 변수를 작성하고 수정하면 데이터베이스에 쿼리가 보내지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="92699-154">`IQueryable` 개체를 `ToListAsync`와 같은 메서드를 호출하여 컬렉션으로 변환할 때까지 쿼리가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="92699-155">따라서 이 코드는 `return View` 문까지 실행되지 않는 단일 쿼리가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="92699-156">많은 수의 열로 이 코드가 길어질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="92699-157">[이 시리즈의 마지막 자습서](advanced.md#dynamic-linq)에서는 문자열 변수에 `OrderBy` 열의 이름을 전달할 수 있는 코드를 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="92699-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="92699-158">Student 인덱스 뷰에 열 제목 하이퍼링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="92699-159">*Views/Students/Index.cshtml* 에 있는 코드를 다음 코드로 바꾸어 열 제목 하이퍼링크를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="92699-160">변경된 선이 강조 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-160">The changed lines are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="92699-161">이 코드는 `ViewData` 속성의 정보를 사용하여 하이퍼링크를 적절한 쿼리 문자열 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="92699-162">앱을 실행하여 **Students(학생)** 탭을 선택하고 **Last Name(성)** 및 **Enrollment Date(등록 날짜)** 열 머리글을 클릭하여 제대로 정렬되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![이름 순서로 된 학생 인덱스 페이지](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="92699-164">검색 상자 추가</span><span class="sxs-lookup"><span data-stu-id="92699-164">Add a Search box</span></span>

<span data-ttu-id="92699-165">학생 인덱스 페이지에 필터링을 추가하려면 뷰에 텍스트 상자와 [제출] 단추를 추가하고 `Index` 메서드에 해당 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="92699-166">텍스트 상자를 통해 이름 및 성 필드에서 검색할 문자열을 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="92699-167">Index 메서드에 필터링 기능 추가</span><span class="sxs-lookup"><span data-stu-id="92699-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="92699-168">*StudentsController.cs* 에서 `Index` 메서드를 다음 코드로 바꿉니다(변경 내용이 강조 표시됨).</span><span class="sxs-lookup"><span data-stu-id="92699-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="92699-169">`searchString` 매개 변수를 `Index` 메서드에 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="92699-170">검색 문자열 값은 Index 뷰에 추가할 텍스트 상자에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="92699-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="92699-171">또한 성과 이름에 검색 문자열이 포함된 학생만 선택하는 where 절이 LINQ 문에 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="92699-172">where 절을 추가하는 문은 검색할 값이 있는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="92699-173">여기에서는 `IQueryable` 개체에서 `Where` 메서드를 호출하고 있으며 필터는 서버에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="92699-174">일부 시나리오에서는 메모리 내 컬렉션에서 확장 메서드로 `Where` 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="92699-175">(예를 들어, EF `DbSet` 대신에 `IEnumerable` 컬렉션을 반환하는 리포지토리 메서드를 참조하도록 `_context.Students`로 참조를 변경한다고 가정해 보겠습니다.) 결과는 일반적으로 동일하지만 경우에 따라 다를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="92699-176">예를 들어 `Contains` 메서드의 .NET Framework 구현은 기본적으로 대/소문자 구분 비교를 수행하지만 SQL Server에서는 SQL Server 인스턴스의 데이터 정렬 설정에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="92699-177">이 설정은 기본적으로 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="92699-178">`ToUpper` 메서드를 호출하여 테스트가 명시적으로 대/소문자를 구분하지 않도록 설정할 수 있습니다.  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* .</span><span class="sxs-lookup"><span data-stu-id="92699-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="92699-179">이렇게 하면 `IQueryable` 개체 대신 `IEnumerable` 컬렉션을 반환하는 리포지토리를 사용하도록 코드를 나중에 변경할 경우 결과가 동일하게 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="92699-180">(`IEnumerable` 컬렉션에서 `Contains` 메서드를 호출하면 .NET Framework 구현을 가져오고 `IQueryable` 개체에서 호출하면 데이터베이스 공급자 구현을 가져옵니다.) 그러나 이 솔루션에서는 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="92699-181">`ToUpper` 코드는 TSQL SELECT 문의 WHERE 절에 함수를 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="92699-182">그러면 최적화 프로그램이 인덱스를 사용할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="92699-183">SQL은 대부분 대/소문자를 구분하지 않도록 설치된다는 것을 고려하면 대/소문자 구분 데이터 저장소로 마이그레이션할 때까지 `ToUpper` 코드를 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="92699-184">Students 인덱스 뷰에 검색 상자 추가</span><span class="sxs-lookup"><span data-stu-id="92699-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="92699-185">*Views/Student/Index.cshtml* 에서 캡션, 텍스트 상자 및 **검색** 단추를 만들기 위해 여는 테이블 태그 바로 앞에 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="92699-186">이 코드는 `<form>` [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하여 검색 텍스트 상자 및 단추를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="92699-187">기본적으로는 `<form>` 태그 도우미는 폼 데이터를 POST로 제출합니다. 즉, 매개 변수를 URL에 쿼리 문자열로 전달하지 않고 HTTP 메시지 본문으로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="92699-188">HTTP GET을 지정하면 폼 데이터가 URL에 쿼리 문자열로 전달되고 이를 통해 사용자는 URL을 책갈피로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="92699-189">W3C 지침에 따라 작업이 업데이트되지 않을 때 GET을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="92699-190">앱을 실행하고 **Students(학생)** 탭을 선택하며 검색 문자열을 입력한 후 [검색]을 클릭하여 필터링이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![필터링이 있는 학생 인덱스 페이지](sort-filter-page/_static/filtering.png)

<span data-ttu-id="92699-192">URL에 검색 문자열이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="92699-193">이 페이지를 책갈피로 지정하면 책갈피를 사용할 때 필터링된 목록을 얻게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="92699-194">`method="get"`을 `form` 태그에 추가하면 쿼리 문자열이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="92699-195">이 단계에서 열 제목 정렬 링크를 클릭하면 **검색** 상자에 입력한 필터 값이 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="92699-196">다음 섹션에서 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="92699-197">학생 인덱스에 페이징 추가</span><span class="sxs-lookup"><span data-stu-id="92699-197">Add paging to Students Index</span></span>

<span data-ttu-id="92699-198">학생 인덱스 페이지에 페이징을 추가하려면 `Skip` 및 `Take` 문을 사용하는 `PaginatedList` 클래스를 만들어 항상 테이블의 모든 행을 검색하는 대신, 서버에서 데이터를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="92699-199">그런 다음, `Index` 메서드를 추가로 변경하고 `Index` 뷰에 페이징 단추를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="92699-200">다음 그림에는 페이징 단추가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-200">The following illustration shows the paging buttons.</span></span>

![페이징 링크가 있는 학생 인덱스 페이지](sort-filter-page/_static/paging.png)

<span data-ttu-id="92699-202">프로젝트 폴더에서 `PaginatedList.cs`를 만든 후 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="92699-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="92699-203">이 코드에서 `CreateAsync` 메서드는 페이지 크기 및 페이지 번호를 사용하고 적절한 `Skip` 및 `Take` 문을 `IQueryable`에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="92699-204">`IQueryable`에서 `ToListAsync`를 호출하면 요청된 페이지만 포함하는 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="92699-205">**이전** 및 **다음** 페이징 단추를 사용 또는 사용하지 않도록 하는 데 `HasPreviousPage` 및 `HasNextPage` 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="92699-206">생성자가 비동기 코드를 실행할 수 없으므로 `CreateAsync` 메서드가 생성자 대신 `PaginatedList<T>` 개체를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="92699-207">Index 메서드에 페이징 추가</span><span class="sxs-lookup"><span data-stu-id="92699-207">Add paging to Index method</span></span>

<span data-ttu-id="92699-208">*StudentsController.cs* 에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="92699-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="92699-209">이 코드는 페이지 번호 매개 변수, 현재 정렬 순서 매개 변수 및 현재 필터 매개 변수를 메서드 시그니처에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="92699-210">페이지가 처음 표시되거나 사용자가 페이징 또는 정렬 링크를 클릭하지 않으면 모든 매개 변수가 Null이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="92699-211">페이징 링크를 클릭하면 페이지 변수에 표시할 페이지 번호가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="92699-212">페이징 동안 정렬 순서를 동일하게 유지하기 위해 페이징 링크에 포함되어야 하므로 CurrentSort라는 `ViewData` 요소는 현재 정렬 순서의 뷰를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="92699-213">CurrentFilter라는 `ViewData` 요소는 현재 필터 문자열이 포함된 뷰를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="92699-214">이 값은 페이징 중 필터 설정을 유지하기 위해 페이징 링크에 포함되어야 하며 페이지를 다시 표시할 때 텍스트 상자에 복원되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="92699-215">페이징 중에 검색 문자열이 변경되면 새 필터로 인해 다른 데이터가 표시될 수 있으므로 페이지는 1로 재설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="92699-216">텍스트 상자에 값을 입력하고 [제출] 단추를 누르면 검색 문자열이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="92699-217">이 경우 `searchString` 매개 변수는 Null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="92699-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="92699-218">`Index` 메서드의 끝에서 `PaginatedList.CreateAsync` 메서드는 학생 쿼리를 페이징을 지원하는 컬렉션 형식의 단일 학생 페이지로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="92699-219">그러면 단일 학생 페이지가 뷰에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="92699-220">`PaginatedList.CreateAsync` 메서드는 페이지 번호를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="92699-221">두 개의 물음표는 Null 병합 연산자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="92699-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="92699-222">Null 병합 연산자는 nullable 형식의 기본값을 정의합니다. `(pageNumber ?? 1)` 식은 값이 있는 경우 `pageNumber` 값을 반환하고 `pageNumber`가 Null이면 1일 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="92699-223">페이징 링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-223">Add paging links</span></span>

<span data-ttu-id="92699-224">*Views/Students/Index.cshtml* 에서 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="92699-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="92699-225">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-225">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="92699-226">페이지 맨 위에 `@model` 문은 뷰가 `List<T>` 개체 대신 `PaginatedList<T>` 개체를 가져오는 것을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="92699-227">열 머리글 링크는 쿼리 문자열을 사용하여 현재 검색 문자열을 컨트롤러에 전달하므로 사용자가 필터 결과 내에서 정렬할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="92699-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="92699-228">태그 도우미에 페이징 단추가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="92699-229">앱을 실행하고 [학생] 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-229">Run the app and go to the Students page.</span></span>

![페이징 링크가 있는 학생 인덱스 페이지](sort-filter-page/_static/paging.png)

<span data-ttu-id="92699-231">다른 정렬 순서의 페이징 링크를 클릭하여 페이징이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="92699-232">그런 다음, 검색 문자열을 입력하고 페이징을 다시 시도하여 정렬 및 필터링을 통해 페이징이 제대로 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="92699-233">정보 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="92699-233">Create an About page</span></span>

<span data-ttu-id="92699-234">Contoso University 웹 사이트의 **정보** 페이지에는 각 등록 날짜에 등록된 학생 수가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="92699-235">여기에는 그룹화와 그룹에 대한 간단한 계산이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="92699-236">이 작업을 수행하기 위해 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="92699-237">뷰에 전달해야 하는 데이터에 대해 뷰 모델 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92699-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="92699-238">홈 컨트롤러에서 About 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92699-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="92699-239">정보 뷰를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92699-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="92699-240">뷰 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="92699-240">Create the view model</span></span>

<span data-ttu-id="92699-241">*Models* 폴더에 *SchoolViewModels* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="92699-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="92699-242">새 폴더에서 *EnrollmentDateGroup.cs* 클래스 파일을 추가하고 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="92699-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="92699-243">홈 컨트롤러 수정</span><span class="sxs-lookup"><span data-stu-id="92699-243">Modify the Home Controller</span></span>

<span data-ttu-id="92699-244">*HomeController.cs* 에서 파일 맨 위에 다음 using 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="92699-245">클래스의 여는 중괄호 바로 뒤에 데이터베이스 컨텍스트에 대한 클래스 변수를 추가하고 ASP.NET Core DI에서 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="92699-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="92699-246">다음 코드를 통해 `About` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="92699-247">LINQ 문은 등록 날짜별로 학생 엔터티를 그룹화하고 각 그룹의 엔터티 수를 계산하며 결과를 `EnrollmentDateGroup` 뷰 모델 개체의 컬렉션에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="92699-248">정보 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="92699-248">Create the About View</span></span>

<span data-ttu-id="92699-249">다음 코드를 사용하여 *Views/Home/About.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="92699-250">앱을 실행하고 [정보] 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-250">Run the app and go to the About page.</span></span> <span data-ttu-id="92699-251">각 등록 날짜에 대한 학생 수가 테이블에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="92699-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="92699-252">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="92699-252">Get the code</span></span>

[<span data-ttu-id="92699-253">완성된 애플리케이션을 다운로드하거나 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="92699-254">다음 단계</span><span class="sxs-lookup"><span data-stu-id="92699-254">Next steps</span></span>

<span data-ttu-id="92699-255">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92699-256">열 정렬 링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-256">Added column sort links</span></span>
> * <span data-ttu-id="92699-257">검색 상자 추가</span><span class="sxs-lookup"><span data-stu-id="92699-257">Added a Search box</span></span>
> * <span data-ttu-id="92699-258">학생 인덱스에 페이징 추가</span><span class="sxs-lookup"><span data-stu-id="92699-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="92699-259">Index 메서드에 페이징 추가</span><span class="sxs-lookup"><span data-stu-id="92699-259">Added paging to Index method</span></span>
> * <span data-ttu-id="92699-260">페이징 링크 추가</span><span class="sxs-lookup"><span data-stu-id="92699-260">Added paging links</span></span>
> * <span data-ttu-id="92699-261">정보 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="92699-261">Created an About page</span></span>

<span data-ttu-id="92699-262">마이그레이션을 사용하여 데이터 모델 변경 내용을 처리하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="92699-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="92699-263">다음: 데이터 모델 변경 처리</span><span class="sxs-lookup"><span data-stu-id="92699-263">Next: Handle data model changes</span></span>](migrations.md)
