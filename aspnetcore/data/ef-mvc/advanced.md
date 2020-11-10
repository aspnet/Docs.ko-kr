---
title: '자습서: 고급 시나리오에 대해 알아보기 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 Entity Framework Core를 사용하는 ASP.NET Core 웹앱 개발의 기본 개념을 넘어 유용한 토픽을 소개합니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054387"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="2b48f-103">자습서: 고급 시나리오에 대해 알아보기 - ASP.NET MVC 및 EF Core 사용</span><span class="sxs-lookup"><span data-stu-id="2b48f-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="2b48f-104">이전 자습서에서는 계층당 하나의 테이블 상속을 구현했습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="2b48f-105">이 자습서에서는 Entity Framework Core를 사용하는 ASP.NET Core 웹 애플리케이션을 개발하는 기본 개념을 넘어 알아 두면 유용한 여러 가지 항목을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="2b48f-106">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2b48f-107">원시 SQL 쿼리 수행</span><span class="sxs-lookup"><span data-stu-id="2b48f-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="2b48f-108">엔터티를 반환하는 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-108">Call a query to return entities</span></span>
> * <span data-ttu-id="2b48f-109">기타 형식을 반환하는 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-109">Call a query to return other types</span></span>
> * <span data-ttu-id="2b48f-110">업데이트 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-110">Call an update query</span></span>
> * <span data-ttu-id="2b48f-111">SQL 쿼리 검사</span><span class="sxs-lookup"><span data-stu-id="2b48f-111">Examine SQL queries</span></span>
> * <span data-ttu-id="2b48f-112">추상화 계층 만들기</span><span class="sxs-lookup"><span data-stu-id="2b48f-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="2b48f-113">자동 변경 검색에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="2b48f-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="2b48f-114">EF Core 소스 코드 및 개발 계획에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="2b48f-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="2b48f-115">동적 LINQ를 사용하여 코드를 단순화하는 방법 알아보기</span><span class="sxs-lookup"><span data-stu-id="2b48f-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2b48f-116">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="2b48f-116">Prerequisites</span></span>

* [<span data-ttu-id="2b48f-117">상속 구현</span><span class="sxs-lookup"><span data-stu-id="2b48f-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="2b48f-118">원시 SQL 쿼리 수행</span><span class="sxs-lookup"><span data-stu-id="2b48f-118">Perform raw SQL queries</span></span>

<span data-ttu-id="2b48f-119">Entity Framework를 사용할 때 장점 중 하나는 코드가 데이터를 저장하는 특정 메서드에 너무 얽매이지 않아도 된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="2b48f-120">이것은 사용자를 위한 SQL 쿼리와 명령이 생성되므로 가능하며 사용자는 코드를 직접 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="2b48f-121">하지만 사용자가 직접 만든 특정 SQL 쿼리를 실행해야 할 때는 예외적인 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="2b48f-122">이러한 시나리오에서는 Entity Framework Code First API에 SQL 명령을 데이터베이스에 직접 전달할 수 있는 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="2b48f-123">EF Core 1.0에서 다음과 같은 옵션을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="2b48f-124">엔터티 형식을 반환하는 쿼리에 `DbSet.FromSql` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="2b48f-125">반환된 개체는 `DbSet` 개체에 예상되는 형식이어야 하며 [추적을 해제](crud.md#no-tracking-queries)하지 않는 한 데이터베이스 컨텍스트에 의해 자동으로 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="2b48f-126">쿼리가 아닌 명령에는 `Database.ExecuteSqlCommand`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="2b48f-127">엔터티가 아닌 형식을 반환하는 쿼리를 실행해야 하는 경우 EF에서 제공하는 데이터베이스 연결로 ADO.NET을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="2b48f-128">이 메서드를 사용하여 엔터티 형식을 검색하더라도 반환된 데이터는 데이터베이스 컨텍스트에 의해 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="2b48f-129">웹 애플리케이션에서 SQL 명령을 실행할 때 항상 그렇듯이 SQL 삽입 공격으로부터 사이트를 보호하기 위한 예방 조치를 취해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="2b48f-130">이를 수행하는 한 가지 방법은 매개 변수가 있는 쿼리를 사용하여 웹 페이지에서 제출한 문자열을 SQL 명령으로 해석할 수 없도록 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="2b48f-131">이 자습서에서는 사용자 입력을 쿼리에 통합할 때 매개 변수가 있는 쿼리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="2b48f-132">엔터티를 반환하는 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-132">Call a query to return entities</span></span>

<span data-ttu-id="2b48f-133">`DbSet<TEntity>` 클래스는 `TEntity` 형식의 엔터티를 반환하는 쿼리를 실행하는 데 사용할 수 있는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="2b48f-134">어떻게 작동하는지 보기 위해 Department 컨트롤러의 `Details` 메서드에서 코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="2b48f-135">다음 강조 표시된 코드에서처럼 *DepartmentsController.cs* 의 `Details` 메서드에서 `FromSql` 메서드 호출로 부서를 검색하는 코드를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-135">In *DepartmentsController.cs* , in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="2b48f-136">새 코드가 올바르게 작동하는지 확인하려면 **부서** 탭을 선택한 후 부서 중 하나에 대해 **세부 정보** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![부서 세부 정보](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="2b48f-138">기타 형식을 반환하는 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-138">Call a query to return other types</span></span>

<span data-ttu-id="2b48f-139">이전에 등록 날짜별로 학생 수를 보여주는 [정보] 페이지의 학생 통계 표를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="2b48f-140">학생 엔터티 집합(`_context.Students`)에서 데이터를 가져와 LINQ를 사용하여 결과를 `EnrollmentDateGroup` 뷰 모델 개체 목록에 프로젝션했습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="2b48f-141">LINQ를 사용하지 않고 SQL을 직접 작성한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="2b48f-142">이를 수행하려면 엔터티 개체가 아닌 다른 것을 리턴하는 SQL 쿼리를 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="2b48f-143">EF Core 1.0에서 이를 수행하는 한 가지 방법은 ADO.NET 코드를 작성하고 EF에서 데이터베이스 연결을 얻는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="2b48f-144">*HomeController.cs* 에서 `About` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-144">In *HomeController.cs* , replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="2b48f-145">using 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="2b48f-146">앱을 실행하고 [정보] 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-146">Run the app and go to the About page.</span></span> <span data-ttu-id="2b48f-147">이전과 동일한 데이터가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-147">It displays the same data it did before.</span></span>

![정보 페이지](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="2b48f-149">업데이트 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-149">Call an update query</span></span>

<span data-ttu-id="2b48f-150">Contoso University 관리자가 모든 과정의 학점 수를 변경하는 등 데이터베이스의 전체적인 변경을 수행하려고 한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="2b48f-151">대학에 과목이 많은 경우 엔터티로 모두 검색하여 개별적으로 변경하는 것은 비효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="2b48f-152">이 섹션에서는 사용자가 모든 과정에 대한 학점 수를 변경하는 요소를 지정할 수 있는 웹 페이지를 구현하고 SQL UPDATE 문을 실행하여 학점 수를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="2b48f-153">그러면 웹 페이지가 다음 그림과 같이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-153">The web page will look like the following illustration:</span></span>

![[Course Credits(과정 학점)] 페이지 업데이트](advanced/_static/update-credits.png)

<span data-ttu-id="2b48f-155">*CoursesController.cs* 에서 HttpGet 및 HttpPost의 UpdateCourseCredits 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-155">In *CoursesController.cs* , add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="2b48f-156">컨트롤러가 HttpGet 요청을 처리할 때 `ViewData["RowsAffected"]`에는 아무 것도 반환되지 않으며 앞의 그림과 같이 뷰에 빈 텍스트 상자와 제출 단추가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="2b48f-157">**업데이트** 단추를 클릭하면 HttpPost 메서드가 호출되고 승수는 텍스트 상자에 입력된 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="2b48f-158">그런 다음, 코드는 과정을 업데이트하는 SQL을 실행하고 영향을 받은 행 수를 `ViewData`의 뷰에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="2b48f-159">뷰에 `RowsAffected` 값이 있으면 업데이트된 행 수가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="2b48f-160">**솔루션 탐색기** 에서 *Views/Courses* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 항목** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-160">In **Solution Explorer** , right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="2b48f-161">**새 항목 추가** 대화 상자에서 왼쪽 창의 **설치됨** 아래에서 **ASP.NET** 을 클릭하고, **:::no-loc(Razor)::: 뷰** 를 클릭한 후, 새 뷰의 이름을 *UpdateCourseCredits.cshtml* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **:::no-loc(Razor)::: View** , and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="2b48f-162">*Views/Courses/UpdateCourseCredits.cshtml* 에서 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-162">In *Views/Courses/UpdateCourseCredits.cshtml* , replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="2b48f-163">**Courses(과정)** 탭을 선택하여 `UpdateCourseCredits` 메서드를 실행한 후 브라우저의 주소 표시줄에서 URL 끝에 "/UpdateCourseCredits"를 추가합니다(예: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="2b48f-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="2b48f-164">텍스트 상자에 숫자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-164">Enter a number in the text box:</span></span>

![[Course Credits(과정 학점)] 페이지 업데이트](advanced/_static/update-credits.png)

<span data-ttu-id="2b48f-166">**업데이트** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-166">Click **Update**.</span></span> <span data-ttu-id="2b48f-167">영향을 받은 행 수가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-167">You see the number of rows affected:</span></span>

![영향을 받는 [Course Credits(과정 학점)] 페이지 행 업데이트](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="2b48f-169">학점 수가 수정된 과정 목록을 보려면 **목록으로 돌아가기** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="2b48f-170">프로덕션 코드는 항상 업데이트로 인해 항상 유효한 데이터가 생기도록 보장해 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="2b48f-171">여기에 표시된 간소화된 코드에서는 5보다 큰 숫자가 생길 만큼 충분한 학점 수를 곱할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="2b48f-172">(`Credits` 속성에는 `[Range(0, 5)]` 특성이 포함됩니다.) 업데이트 쿼리가 작동하지만 잘못된 데이터로 인해 학점이 5 이하라고 가정하는 시스템의 다른 부분에 예기치 않은 결과가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="2b48f-173">원시 SQL 쿼리에 대한 자세한 내용은 [원시 SQL 쿼리](/ef/core/querying/raw-sql)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="2b48f-174">SQL 쿼리 검사</span><span class="sxs-lookup"><span data-stu-id="2b48f-174">Examine SQL queries</span></span>

<span data-ttu-id="2b48f-175">때로는 데이터베이스로 전송된 실제 SQL 쿼리를 볼 수 있는 것이 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="2b48f-176">ASP.NET Core의 기본 로깅 기능은 EF Core에서 쿼리 및 업데이트용 SQL이 포함된 로그를 작성하기 위해 EF Core에 의해 자동으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="2b48f-177">이 섹션에서는 SQL 로깅의 몇 가지 예를 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="2b48f-178">*StudentsController.cs* 를 열고 `Details` 메서드에서 `if (student == null)` 문에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="2b48f-179">디버그 모드에서 앱을 실행하고 학생에 대한 [세부 정보] 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="2b48f-180">디버그 출력을 보여 주는 **출력** 창으로 이동하면 쿼리를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="2b48f-181">여기서 놀라운 사실을 알 수 있는데, SQL이 Person 테이블에서 최대 2개 행을 선택한다는 것입니다(`TOP(2)`).</span><span class="sxs-lookup"><span data-stu-id="2b48f-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="2b48f-182">`SingleOrDefaultAsync` 메서드는 서버의 1개 행으로 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="2b48f-183">이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-183">Here's why:</span></span>

* <span data-ttu-id="2b48f-184">쿼리에서 여러 행을 반환하는 경우 메서드는 Null을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="2b48f-185">쿼리에서 여러 행을 반환하는지 여부를 확인하려면 EF에서 2 이상을 반환하는지 확인해야 합니다</span><span class="sxs-lookup"><span data-stu-id="2b48f-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="2b48f-186">디버그 모드를 사용하지 않고 **출력** 창에서 로깅 출력을 얻기 위해 중단점에서 중단할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="2b48f-187">출력을 확인하려는 지점에서 로깅을 중지하는 편리한 방법일 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="2b48f-188">그렇게 하지 않으면 로깅이 계속되고 관심있는 부분을 찾기 위해 뒤로 스크롤해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="2b48f-189">추상화 계층 만들기</span><span class="sxs-lookup"><span data-stu-id="2b48f-189">Create an abstraction layer</span></span>

<span data-ttu-id="2b48f-190">대부분의 개발자는 리포지토리 및 작업 패턴 단위를 구현하기 위한 코드를 Entity Framework에서 작동하는 코드를 둘러싼 래퍼로 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="2b48f-191">이러한 패턴은 애플리케이션의 데이터 액세스 계층 및 비즈니스 논리 계층 간에 추상화 계층을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="2b48f-192">이러한 패턴을 구현하면 데이터 저장소의 변경 내용으로부터 애플리케이션을 격리할 수 있으며 자동화된 단위 테스트 또는 TDD(테스트 중심 개발)를 용이하게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="2b48f-193">그러나 EF를 사용하는 애플리케이션에 대해 이러한 패턴을 구현하는 추가 코드를 작성하는 것이 항상 좋은 것만은 아닙니다. 다음과 같은 몇 가지 이유 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="2b48f-194">EF 컨텍스트 클래스 자체에서 데이터 저장소별 코드로부터 사용자 코드를 격리합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="2b48f-195">EF 컨텍스트 클래스는 EF를 사용하여 수행하는 데이터베이스 업데이트에 대해 작업 단위 클래스로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="2b48f-196">EF에는 리포지토리 코드를 작성하지 않고도 TDD를 구현하는 기능이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="2b48f-197">리포지토리 및 작업 패턴 단위를 구현하는 방법에 대한 자세한 내용은 [이 자습서 시리즈의 Entity Framework 5 버전](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="2b48f-198">Entity Framework Core는 테스트에 사용할 수 있는 메모리 내 데이터베이스 공급자를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="2b48f-199">자세한 내용은 [InMemory를 사용한 테스트](/ef/core/miscellaneous/testing/in-memory)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="2b48f-200">자동 변경 내용 검색</span><span class="sxs-lookup"><span data-stu-id="2b48f-200">Automatic change detection</span></span>

<span data-ttu-id="2b48f-201">Entity Framework는 엔터티의 현재 값을 원래 값과 비교하여 엔터티가 변경된 방법(즉, 데이터베이스에 전송해야 하는 업데이트)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="2b48f-202">원래 값은 엔터티가 쿼리 또는 연결될 때 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="2b48f-203">자동 변경 내용 검색을 발생시키는 일부 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="2b48f-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="2b48f-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="2b48f-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="2b48f-205">DbContext.Entry</span></span>

* <span data-ttu-id="2b48f-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="2b48f-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="2b48f-207">대량의 엔터티를 추적하고 루프에서 이러한 메서드 중 하나를 여러 번 호출하는 경우 `ChangeTracker.AutoDetectChangesEnabled` 속성을 사용하여 자동 변경 내용 검색을 일시적으로 해제하면 성능이 크게 향상될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="2b48f-208">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2b48f-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="2b48f-209">EF Core 소스 코드 및 개발 계획</span><span class="sxs-lookup"><span data-stu-id="2b48f-209">EF Core source code and development plans</span></span>

<span data-ttu-id="2b48f-210">Entity Framework Core 소스는 [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="2b48f-211">EF Core 리포지토리에는 야간 빌드, 문제 추적, 기능 사양, 디자인 회의 노트 및 [향후 개발을 위한 로드맵](https://github.com/dotnet/efcore/wiki/Roadmap)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="2b48f-212">버그를 보고하거나 찾고 참가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="2b48f-213">소스 코드가 오픈 소스이기는 하지만 Entity Framework Core는 완전히 Microsoft 제품으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="2b48f-214">Microsoft Entity Framework 팀이 어떤 참가자를 수락할지 관리하고 각 릴리스의 품질을 보장하기 위해 모든 코드 변경 내용을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="2b48f-215">기존 데이터베이스에서 리버스 엔지니어링</span><span class="sxs-lookup"><span data-stu-id="2b48f-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="2b48f-216">기존 데이터베이스에서 엔터티 클래스를 포함한 데이터 모델을 리버스 엔지니어링하려면 [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="2b48f-217">[시작 자습서](/ef/core/get-started/aspnetcore/existing-db)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="2b48f-218">동적 LINQ를 사용하여 코드 단순화</span><span class="sxs-lookup"><span data-stu-id="2b48f-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="2b48f-219">[이 시리즈의 세 번째 자습서](sort-filter-page.md)에서는 `switch` 문에 열 이름을 하드 코딩하여 LINQ 코드를 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="2b48f-220">선택할 수 있는 열이 두 개인 경우 잘 작동하지만 열 수가 많은 경우 코드가 길어질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="2b48f-221">이러한 문제를 해결하기 위해 `EF.Property` 메서드를 사용하여 속성 이름을 문자열로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="2b48f-222">이 방법을 사용해 보려면 `StudentsController`에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="2b48f-223">감사의 글</span><span class="sxs-lookup"><span data-stu-id="2b48f-223">Acknowledgments</span></span>

<span data-ttu-id="2b48f-224">Tom Dykstra 및 Rick Anderson(twitter @RickAndMSFT)이 본 자습서를 작성했습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="2b48f-225">Rowan Miller, Diego Vega 및 기타 Entity Framework 팀원은 코드를 검토해 주었고 자습서에 사용할 코드를 작성하는 동안 발생하는 디버그 문제를 도와주었습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="2b48f-226">John Parente 및 Paul Goldman은 ASP.NET Core 2.2 자습서를 업데이트하는 작업을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="2b48f-227">일반적인 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="2b48f-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="2b48f-228">다른 프로세스에 ContosoUniversity.dll 사용됨</span><span class="sxs-lookup"><span data-stu-id="2b48f-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="2b48f-229">오류 메시지:</span><span class="sxs-lookup"><span data-stu-id="2b48f-229">Error message:</span></span>

> <span data-ttu-id="2b48f-230">'...bin\Debug\netcoreapp1.0\ContosoUniversity.dll'을 쓰기용으로 열 수 없습니다 -- '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' 파일은 다른 프로세스에서 사용 중이므로 프로세스에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="2b48f-231">해결책:</span><span class="sxs-lookup"><span data-stu-id="2b48f-231">Solution:</span></span>

<span data-ttu-id="2b48f-232">IIS Express에서 사이트를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="2b48f-233">Windows 시스템 트레이로 이동하고 IIS Express를 찾아 해당 아이콘을 마우스 오른쪽 단추로 클릭하고 Contoso University 사이트를 선택한 다음, **사이트 중지** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="2b48f-234">Up 및 Down 메서드에서 코드 없이 스캐폴드된 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2b48f-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="2b48f-235">가능한 원인:</span><span class="sxs-lookup"><span data-stu-id="2b48f-235">Possible cause:</span></span>

<span data-ttu-id="2b48f-236">EF CLI 명령은 코드 파일을 자동으로 닫고 저장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="2b48f-237">`migrations add` 명령을 실행할 때 저장되지 않은 변경 내용이 있는 경우 EF가 변경 내용을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="2b48f-238">해결책:</span><span class="sxs-lookup"><span data-stu-id="2b48f-238">Solution:</span></span>

<span data-ttu-id="2b48f-239">`migrations remove` 명령을 실행하고 코드 변경 내용을 저장한 후 `migrations add` 명령을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="2b48f-240">데이터베이스 업데이트를 실행하는 동안 오류 발생</span><span class="sxs-lookup"><span data-stu-id="2b48f-240">Errors while running database update</span></span>

<span data-ttu-id="2b48f-241">기존 데이터가 있는 데이터베이스에서 스키마를 변경할 때 다른 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="2b48f-242">해결할 수 없는 마이그레이션 오류가 발생하면 연결 문자열에서 데이터베이스 이름을 변경하거나 데이터베이스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="2b48f-243">새 데이터베이스에는 마이그레이션할 데이터가 없으므로 update-database 명령은 오류없이 완료될 가능성이 훨씬 큽니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="2b48f-244">가장 간단한 방법은 *:::no-loc(appsettings.json):::* 에서 데이터베이스 이름을 바꾸는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-244">The simplest approach is to rename the database in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="2b48f-245">다음에 `database update`를 실행할 때 새 데이터베이스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="2b48f-246">SSOX에서 데이터베이스를 삭제하려면 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 클릭한 후 **데이터베이스 삭제** 대화 상자에서 **기존 연결 닫기** 를 선택하고 **확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-246">To delete a database in SSOX, right-click the database, click **Delete** , and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="2b48f-247">CLI를 사용하여 데이터베이스를 삭제하려면 `database drop` CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="2b48f-248">SQL Server 인스턴스 찾기 오류</span><span class="sxs-lookup"><span data-stu-id="2b48f-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="2b48f-249">오류 메시지:</span><span class="sxs-lookup"><span data-stu-id="2b48f-249">Error Message:</span></span>

> <span data-ttu-id="2b48f-250">SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="2b48f-251">서버를 찾을 수 없거나 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="2b48f-252">인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="2b48f-253">(공급자: SQL 네트워크 인터페이스, 오류: 26 - 지정된 서버/인스턴스 찾기 오류)</span><span class="sxs-lookup"><span data-stu-id="2b48f-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="2b48f-254">해결책:</span><span class="sxs-lookup"><span data-stu-id="2b48f-254">Solution:</span></span>

<span data-ttu-id="2b48f-255">연결 문자열을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-255">Check the connection string.</span></span> <span data-ttu-id="2b48f-256">데이터베이스 파일을 수동으로 삭제한 경우 새 데이터베이스로 시작하도록 생성 문자열에서 데이터베이스 이름을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="2b48f-257">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="2b48f-257">Get the code</span></span>

[<span data-ttu-id="2b48f-258">완성된 애플리케이션을 다운로드하거나 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="2b48f-259">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2b48f-259">Additional resources</span></span>

<span data-ttu-id="2b48f-260">EF Core에 대한 자세한 내용은 [Entity Framework Core 설명서](/ef/core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="2b48f-261">책에도 사용할 수 있습니다. [작업 중인 Entity Framework Core](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="2b48f-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="2b48f-262">웹앱을 배포하는 방법에 대한 정보는 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2b48f-263">인증 및 권한 부여와 같은 ASP.NET Core MVC와 관련된 다른 항목에 대한 정보는 <xref:index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b48f-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2b48f-264">다음 단계</span><span class="sxs-lookup"><span data-stu-id="2b48f-264">Next steps</span></span>

<span data-ttu-id="2b48f-265">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2b48f-266">원시 SQL 쿼리 수행</span><span class="sxs-lookup"><span data-stu-id="2b48f-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="2b48f-267">엔터티를 반환하는 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-267">Called a query to return entities</span></span>
> * <span data-ttu-id="2b48f-268">기타 형식을 반환하는 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-268">Called a query to return other types</span></span>
> * <span data-ttu-id="2b48f-269">업데이트 쿼리 호출</span><span class="sxs-lookup"><span data-stu-id="2b48f-269">Called an update query</span></span>
> * <span data-ttu-id="2b48f-270">SQL 쿼리 검사</span><span class="sxs-lookup"><span data-stu-id="2b48f-270">Examined SQL queries</span></span>
> * <span data-ttu-id="2b48f-271">추상화 계층 만들기</span><span class="sxs-lookup"><span data-stu-id="2b48f-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="2b48f-272">자동 변경 검색에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="2b48f-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="2b48f-273">EF Core 소스 코드 및 개발 계획에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="2b48f-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="2b48f-274">동적 LINQ를 사용하여 코드를 단순화하는 방법 알아보기</span><span class="sxs-lookup"><span data-stu-id="2b48f-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="2b48f-275">이것으로 ASP.NET Core MVC 애플리케이션에서 Entity Framework Core 사용에 대한 자습서 시리즈를 마칩니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="2b48f-276">이 시리즈는 새 데이터베이스로 작업했습니다. 대안은 기존 데이터베이스에서 모델을 리버스 엔지니어링하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2b48f-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2b48f-277">자습서: MVC가 있는 EF Core, 기존 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="2b48f-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
