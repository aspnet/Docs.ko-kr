---
title: 8부. ASP.NET Core MVC 앱에 새 필드 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 8부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: d2b3b22a94e3119712e331565cc74ffa60ada726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050708"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="39956-103">8부. ASP.NET Core MVC 앱에 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="39956-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="39956-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="39956-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="39956-105">이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="39956-106">모델에 새 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-106">Add a new field to the model.</span></span>
* <span data-ttu-id="39956-107">새 필드를 데이터베이스로 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="39956-108">EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="39956-109">데이터베이스 스키마를 추적하기 위해 데이터베이스에 테이블을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="39956-110">데이터베이스가 생성된 모델 클래스와 동기화되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="39956-111">동기화되어 있지 않다면 EF에서 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="39956-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="39956-112">이렇게 하면 더 쉽게 일관성이 없는 데이터베이스/코드 문제를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="39956-113">영화 모델에 Rating 속성 추가</span><span class="sxs-lookup"><span data-stu-id="39956-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="39956-114">*Models/Movie.cs* 에 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="39956-115">앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="39956-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39956-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="39956-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="39956-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="39956-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39956-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="39956-119">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39956-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="39956-120">명령 ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="39956-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="39956-121">`Movie` 클래스에 새 필드를 추가했으므로 이 새 속성이 포함되도록 속성 바인딩 목록을 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="39956-122">*MoviesController.cs* 에서 `Rating` 속성을 포함하도록 `Create` 및 `Edit` 작업 메서드에 대한 `[Bind]` 특성을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="39956-123">브라우저 보기에서 새 `Rating` 속성을 표시, 작성 및 편집하기 위해 보기 템플릿을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="39956-124">*/Views/Movies/Index.cshtml* 파일을 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="39956-125">`Rating` 필드를 사용하여 */Views/Movies/Create.cshtml* 을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="39956-126">Visual Studio / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="39956-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="39956-127">이전 "양식 그룹"을 복사/붙여넣기하고 intelliSense에서 필드를 업데이트하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="39956-128">IntelliSense는 [태그 도우미](xref:mvc/views/tag-helpers/intro)와 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![개발자가 보기의 두 번째 레이블 요소에서 asp-for의 특성 값에 대해 문자 R을 입력했습니다.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="39956-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="39956-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="39956-133">나머지 템플릿을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-133">Update the remaining templates.</span></span>

<span data-ttu-id="39956-134">새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="39956-135">아래에서 변경 예제를 볼 수 있지만 각 `new Movie`마다 이 변경을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="39956-136">새 필드를 포함하도록 DB가 수정될 때까지 앱은 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="39956-137">지금 앱을 실행하면 `SqlException`이 던져집니다.</span><span class="sxs-lookup"><span data-stu-id="39956-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="39956-138">이 오류는 수정된 Movie 모델 클래스가 기존 데이터베이스의 Movie 테이블 스키마와 다르기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="39956-139">(데이터베이스 테이블에 `Rating` 열이 없습니다.)</span><span class="sxs-lookup"><span data-stu-id="39956-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="39956-140">이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="39956-141">Entity Framework에서 새 모델 클래스 스키마에 따라 데이터베이스를 자동으로 삭제하고 다시 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="39956-142">이 방법은 테스트 데이터베이스에서 활발한 개발을 수행할 때 개발 주기의 초기 단계에서 매우 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="39956-143">그러나 단점은 데이터베이스에서 기존 데이터를 손실한다는 것입니다. 따라서 프로덕션 데이터베이스에서 이 방법을 사용하지 않으려 합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="39956-144">테스트 데이터로 데이터베이스를 자동으로 시드하는 데 이니셜라이저를 사용하는 것은 종종 애플리케이션을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="39956-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="39956-145">이는 초기 개발과 SQLite를 사용할 때 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="39956-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="39956-146">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="39956-147">이 방법의 장점은 데이터가 유지된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="39956-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="39956-148">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="39956-149">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="39956-150">이 자습서에서는 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39956-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39956-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39956-152">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC 메뉴](adding-model/_static/pmc.png)

<span data-ttu-id="39956-154">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="39956-155">`Add-Migration` 명령은 마이그레이션 프레임워크에서 현재 `Movie` DB 스키마로 현재 `Movie` 모델을 검사하고 DB를 새 모델로 마이그레이션하는 데 필요한 코드를 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="39956-156">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="39956-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="39956-157">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39956-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="39956-158">DB의 모든 레코드가 삭제되면 이니셜라이즈 메서드가 DB를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="39956-159">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39956-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="39956-160">데이터베이스와 이전 마이그레이션을 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39956-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="39956-161">`dotnet ef migrations remove`는 마지막 마이그레이션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="39956-162">둘 이상의 마이그레이션이 있는 경우 마이그레이션 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="39956-163">앱을 실행하고 `Rating` 필드를 사용하여 영화를 생성, 편집, 표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39956-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="39956-164">[이전](search.md)
> [다음](validation.md)</span><span class="sxs-lookup"><span data-stu-id="39956-164">[Previous](search.md)
[Next](validation.md)</span></span>
