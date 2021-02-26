---
title: '7부: 새 필드 추가'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 7부입니다.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101148"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="4a6de-103">7부. ASP.NET Core의 Razor Page에 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="4a6de-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="4a6de-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4a6de-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4a6de-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4a6de-106">이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4a6de-107">모델에 새 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-107">Add a new field to the model.</span></span>
* <span data-ttu-id="4a6de-108">데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4a6de-109">EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는</span><span class="sxs-lookup"><span data-stu-id="4a6de-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4a6de-110">데이터베이스에 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4a6de-111">모델 클래스가 데이터베이스와 동기화되지 않으면 EF는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="4a6de-112">스키마와 모델의 동기화를 자동 확인하면 일관성이 없는 데이터베이스 코드 문제를 쉽게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4a6de-113">영화 모델에 등급 속성 추가</span><span class="sxs-lookup"><span data-stu-id="4a6de-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="4a6de-114">*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="4a6de-115">앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-115">Build the app.</span></span>

1. <span data-ttu-id="4a6de-116">*Pages/Movies/Index.cshtml* 을 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="4a6de-117">다음 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-117">Update the following pages:</span></span>
   1. <span data-ttu-id="4a6de-118">삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="4a6de-119">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="4a6de-120">편집 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4a6de-121">새 필드를 포함하도록 데이터베이스가 업데이트될 때까지 앱은 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="4a6de-122">데이터베이스를 업데이트하지 않고 앱을 실행하면 `SqlException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4a6de-123">`SqlException` 예외는 업데이트된 영화 모델 클래스가 데이터베이스의 영화 테이블의 스키마와 다르면 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4a6de-124">데이터베이스 테이블에 `Rating` 열이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="4a6de-125">이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4a6de-126">새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4a6de-127">이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4a6de-128">단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4a6de-129">프로덕션 데이터베이스에서 이 방법을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="4a6de-130">테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마 변경 시 데이터베이스를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4a6de-131">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4a6de-132">이 방법의 장점은 데이터가 유지된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="4a6de-133">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4a6de-134">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4a6de-135">이 자습서의 경우 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4a6de-136">새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4a6de-137">아래에 샘플 변경이 나와 있지만 `new Movie` 블록마다 이 변경을 수행하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4a6de-138">[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4a6de-139">솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a6de-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a6de-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4a6de-141">등급 필드에 대한 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="4a6de-142">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="4a6de-143">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="4a6de-144">`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4a6de-145">`Movie` 모델을 `Movie` 데이터베이스 스키마와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="4a6de-146">데이터베이스 스키마를 새 모델로 마이그레이션하도록 코드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="4a6de-147">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4a6de-148">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4a6de-149">`Update-Database` 명령은 프레임워크에게 스키마 변경 내용을 데이터베이스에 적용하고 기존 데이터를 보존하라고 명령합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4a6de-150">데이터베이스의 모든 레코드를 삭제하면 이니셜라이저가 데이터베이스를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="4a6de-151">브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4a6de-152">다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4a6de-153">SSOX에서 데이터베이스를 삭제하려면:</span><span class="sxs-lookup"><span data-stu-id="4a6de-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="4a6de-154">SSOX에서 데이터베이스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="4a6de-155">데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="4a6de-156">**기존 연결 닫기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="4a6de-157">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-157">Select **OK**.</span></span>
1. <span data-ttu-id="4a6de-158">[PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a6de-159">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a6de-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4a6de-160">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="4a6de-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="4a6de-161">이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4a6de-162">마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4a6de-163">그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4a6de-164">예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4a6de-165">열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4a6de-166">이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4a6de-167">대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4a6de-168">SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4a6de-169">테이블 다시 빌드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4a6de-170">새 테이블 만들기.</span><span class="sxs-lookup"><span data-stu-id="4a6de-170">Creating a new table.</span></span>
>* <span data-ttu-id="4a6de-171">이전 테이블에서 새 테이블로 데이터 복사.</span><span class="sxs-lookup"><span data-stu-id="4a6de-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4a6de-172">이전 테이블 삭제.</span><span class="sxs-lookup"><span data-stu-id="4a6de-172">Dropping the old table.</span></span>
>* <span data-ttu-id="4a6de-173">새 테이블 이름 바꾸기.</span><span class="sxs-lookup"><span data-stu-id="4a6de-173">Renaming the new table.</span></span>
>
><span data-ttu-id="4a6de-174">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4a6de-175">SQLite EF Core 데이터베이스 공급자 제한 사항</span><span class="sxs-lookup"><span data-stu-id="4a6de-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4a6de-176">마이그레이션 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4a6de-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4a6de-177">데이터 시드</span><span class="sxs-lookup"><span data-stu-id="4a6de-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="4a6de-178">SQLite ALTER TABLE 문</span><span class="sxs-lookup"><span data-stu-id="4a6de-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="4a6de-179">마이그레이션 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="4a6de-180">다음 명령을 사용하여 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="4a6de-181">앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4a6de-182">데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a6de-183">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4a6de-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4a6de-184">[이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4a6de-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="4a6de-185">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4a6de-186">이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4a6de-187">모델에 새 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-187">Add a new field to the model.</span></span>
* <span data-ttu-id="4a6de-188">데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4a6de-189">EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는</span><span class="sxs-lookup"><span data-stu-id="4a6de-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4a6de-190">데이터베이스에 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4a6de-191">모델 클래스가 데이터베이스와 동기화되지 않으면 EF는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="4a6de-192">스키마와 모델의 동기화를 자동 확인하면 일관성이 없는 데이터베이스 코드 문제를 쉽게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4a6de-193">영화 모델에 등급 속성 추가</span><span class="sxs-lookup"><span data-stu-id="4a6de-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="4a6de-194">*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="4a6de-195">앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-195">Build the app.</span></span>

1. <span data-ttu-id="4a6de-196">*Pages/Movies/Index.cshtml* 을 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="4a6de-197">다음 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-197">Update the following pages:</span></span>
   1. <span data-ttu-id="4a6de-198">삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="4a6de-199">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="4a6de-200">편집 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4a6de-201">새 필드를 포함하도록 데이터베이스가 업데이트될 때까지 앱은 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="4a6de-202">데이터베이스를 업데이트하지 않고 앱을 실행하면 `SqlException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4a6de-203">`SqlException` 예외는 업데이트된 영화 모델 클래스가 데이터베이스의 영화 테이블의 스키마와 다르면 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4a6de-204">데이터베이스 테이블에 `Rating` 열이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="4a6de-205">이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4a6de-206">새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4a6de-207">이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4a6de-208">단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4a6de-209">프로덕션 데이터베이스에서 이 방법을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="4a6de-210">테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마 변경 시 데이터베이스를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4a6de-211">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4a6de-212">이 방법의 장점은 데이터가 유지된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="4a6de-213">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4a6de-214">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4a6de-215">이 자습서의 경우 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4a6de-216">새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4a6de-217">아래에 샘플 변경이 나와 있지만 `new Movie` 블록마다 이 변경을 수행하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4a6de-218">[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4a6de-219">솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a6de-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a6de-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4a6de-221">등급 필드에 대한 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="4a6de-222">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="4a6de-223">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="4a6de-224">`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4a6de-225">`Movie` 모델을 `Movie` 데이터베이스 스키마와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="4a6de-226">데이터베이스 스키마를 새 모델로 마이그레이션하도록 코드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="4a6de-227">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4a6de-228">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4a6de-229">`Update-Database` 명령은 프레임워크에게 스키마 변경 내용을 데이터베이스에 적용하고 기존 데이터를 보존하라고 명령합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4a6de-230">데이터베이스의 모든 레코드를 삭제하면 이니셜라이저가 데이터베이스를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="4a6de-231">브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4a6de-232">다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4a6de-233">SSOX에서 데이터베이스를 삭제하려면:</span><span class="sxs-lookup"><span data-stu-id="4a6de-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="4a6de-234">SSOX에서 데이터베이스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="4a6de-235">데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="4a6de-236">**기존 연결 닫기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="4a6de-237">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-237">Select **OK**.</span></span>
* <span data-ttu-id="4a6de-238">[PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a6de-239">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a6de-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4a6de-240">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="4a6de-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="4a6de-241">이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4a6de-242">마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4a6de-243">그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4a6de-244">예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4a6de-245">열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4a6de-246">이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4a6de-247">대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4a6de-248">SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4a6de-249">테이블 다시 빌드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4a6de-250">새 테이블 만들기.</span><span class="sxs-lookup"><span data-stu-id="4a6de-250">Creating a new table.</span></span>
>* <span data-ttu-id="4a6de-251">이전 테이블에서 새 테이블로 데이터 복사.</span><span class="sxs-lookup"><span data-stu-id="4a6de-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4a6de-252">이전 테이블 삭제.</span><span class="sxs-lookup"><span data-stu-id="4a6de-252">Dropping the old table.</span></span>
>* <span data-ttu-id="4a6de-253">새 테이블 이름 바꾸기.</span><span class="sxs-lookup"><span data-stu-id="4a6de-253">Renaming the new table.</span></span>
>
><span data-ttu-id="4a6de-254">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4a6de-255">SQLite EF Core 데이터베이스 공급자 제한 사항</span><span class="sxs-lookup"><span data-stu-id="4a6de-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4a6de-256">마이그레이션 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4a6de-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4a6de-257">데이터 시드</span><span class="sxs-lookup"><span data-stu-id="4a6de-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="4a6de-258">SQLite ALTER TABLE 문</span><span class="sxs-lookup"><span data-stu-id="4a6de-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="4a6de-259">마이그레이션 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="4a6de-260">다음 명령을 사용하여 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="4a6de-261">앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4a6de-262">데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a6de-263">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4a6de-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4a6de-264">[이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4a6de-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4a6de-265">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4a6de-266">이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4a6de-267">모델에 새 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-267">Add a new field to the model.</span></span>
* <span data-ttu-id="4a6de-268">데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4a6de-269">EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는</span><span class="sxs-lookup"><span data-stu-id="4a6de-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4a6de-270">데이터베이스에 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4a6de-271">모델 클래스가 데이터베이스와 동기화되지 않으면 EF는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="4a6de-272">스키마와 모델의 동기화를 자동 확인하면 일관성이 없는 데이터베이스 코드 문제를 쉽게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4a6de-273">영화 모델에 등급 속성 추가</span><span class="sxs-lookup"><span data-stu-id="4a6de-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4a6de-274">*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="4a6de-275">앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-275">Build the app.</span></span>

<span data-ttu-id="4a6de-276">*Pages/Movies/Index.cshtml* 을 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="4a6de-277">다음 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-277">Update the following pages:</span></span>

* <span data-ttu-id="4a6de-278">삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="4a6de-279">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="4a6de-280">편집 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4a6de-281">새 필드를 포함하도록 데이터베이스가 업데이트될 때까지 앱은 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="4a6de-282">앱을 지금 실행하면 앱은 `SqlException`을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4a6de-283">이 오류는 데이터베이스의 동영상 테이블의 스키마와 다른 업데이트된 동영상 모델 클래스로 인해 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4a6de-284">데이터베이스 테이블에 `Rating` 열이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="4a6de-285">이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4a6de-286">새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4a6de-287">이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4a6de-288">단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4a6de-289">프로덕션 데이터베이스에서 이 방법을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="4a6de-290">테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마 변경 시 데이터베이스를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4a6de-291">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4a6de-292">이 방법의 장점은 데이터가 유지된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="4a6de-293">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4a6de-294">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4a6de-295">이 자습서의 경우 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4a6de-296">새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4a6de-297">아래에 샘플 변경이 나와 있지만 `new Movie` 블록마다 이 변경을 수행하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4a6de-298">[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4a6de-299">솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4a6de-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a6de-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4a6de-301">등급 필드에 대한 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-301">Add a migration for the rating field</span></span>

<span data-ttu-id="4a6de-302">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="4a6de-303">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4a6de-304">`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4a6de-305">`Movie` 모델을 `Movie` 데이터베이스 스키마와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="4a6de-306">데이터베이스 스키마를 새 모델로 마이그레이션하도록 코드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="4a6de-307">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4a6de-308">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4a6de-309">`Update-Database` 명령은 스키마 변경 내용을 데이터베이스에 적용하기 위한 프레임워크를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4a6de-310">데이터베이스의 모든 레코드를 삭제하면 이니셜라이저가 데이터베이스를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="4a6de-311">브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4a6de-312">다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4a6de-313">SSOX에서 데이터베이스를 삭제하려면:</span><span class="sxs-lookup"><span data-stu-id="4a6de-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="4a6de-314">SSOX에서 데이터베이스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="4a6de-315">데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="4a6de-316">**기존 연결 닫기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="4a6de-317">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-317">Select **OK**.</span></span>
* <span data-ttu-id="4a6de-318">[PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4a6de-319">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a6de-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4a6de-320">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="4a6de-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="4a6de-321">이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="4a6de-322">마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="4a6de-323">그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="4a6de-324">예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="4a6de-325">열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="4a6de-326">이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="4a6de-327">대신 스키마가 변경되면 데이터베이스를 삭제하고 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="4a6de-328">SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="4a6de-329">테이블 다시 빌드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="4a6de-330">새 테이블 만들기.</span><span class="sxs-lookup"><span data-stu-id="4a6de-330">Creating a new table.</span></span>
>* <span data-ttu-id="4a6de-331">이전 테이블에서 새 테이블로 데이터 복사.</span><span class="sxs-lookup"><span data-stu-id="4a6de-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="4a6de-332">이전 테이블 삭제.</span><span class="sxs-lookup"><span data-stu-id="4a6de-332">Dropping the old table.</span></span>
>* <span data-ttu-id="4a6de-333">새 테이블 이름 바꾸기.</span><span class="sxs-lookup"><span data-stu-id="4a6de-333">Renaming the new table.</span></span>
>
><span data-ttu-id="4a6de-334">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a6de-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="4a6de-335">SQLite EF Core 데이터베이스 공급자 제한 사항</span><span class="sxs-lookup"><span data-stu-id="4a6de-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="4a6de-336">마이그레이션 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4a6de-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="4a6de-337">데이터 시드</span><span class="sxs-lookup"><span data-stu-id="4a6de-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="4a6de-338">SQLite ALTER TABLE 문</span><span class="sxs-lookup"><span data-stu-id="4a6de-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="4a6de-339">데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4a6de-340">데이터베이스를 삭제하려면 데이터베이스 파일(*MvcMovie.db*)을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="4a6de-341">그런 다음, `ef database update` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="4a6de-342">앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4a6de-343">데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4a6de-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a6de-344">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4a6de-344">Additional resources</span></span>

* [<span data-ttu-id="4a6de-345">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="4a6de-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="4a6de-346">[이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4a6de-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end