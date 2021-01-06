---
title: 4부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 마이그레이션
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 4부입니다.
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061108"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="b6585-103">4부. ASP.NET Core에서 EF Core 마이그레이션을 사용한 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b6585-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="b6585-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b6585-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6585-105">이 자습서에서는 데이터 모델 변경 관리를 위한 EF Core 마이그레이션 기능을 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="b6585-106">새 앱이 개발되면 데이터 모델이 자주 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="b6585-107">모델이 변경될 때마다 모델이 데이터베이스와 동기화 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="b6585-108">이 자습서 시리즈는 존재하지 않는 경우 데이터베이스를 만들도록 Entity Framework를 구성하여 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="b6585-109">데이터 모델이 변경될 때마다 데이터베이스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="b6585-110">다음에 앱이 실행되면 `EnsureCreated`가 호출되어 새 데이터 모델과 일치하도록 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="b6585-111">그런 다음, `DbInitializer` 클래스가 실행되어 새 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="b6585-112">데이터베이스를 데이터 모델과 동기화된 상태로 유지하는 이 접근 방식은 앱을 프로덕션 환경에 배포할 때까지 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="b6585-113">앱이 프로덕션 환경에서 실행 중인 경우 일반적으로 유지 관리해야 하는 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="b6585-114">앱은 변경(예: 새 열 추가)될 때마다 테스트 데이터베이스로 시작될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="b6585-115">EF Core 마이그레이션 기능은 EF Core가 새 데이터베이스를 만드는 대신 데이터베이스 스키마를 업데이트하도록 설정하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="b6585-116">데이터 모델이 변경될 때 데이터베이스를 삭제하고 다시 작성하는 대신 마이그레이션은 스키마를 업데이트하고 기존 데이터를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="b6585-117">데이터베이스 삭제</span><span class="sxs-lookup"><span data-stu-id="b6585-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6585-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6585-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6585-119">SSOX(**SQL Server 개체 탐색기**)를 사용하여 데이터베이스를 삭제하거나, PMC(**패키지 관리자 콘솔**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6585-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6585-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b6585-121">명령 프롬프트에서 다음 명령을 실행하여 EF CLI를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="b6585-122">명령 프롬프트에서 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="b6585-123">프로젝트 폴더에는 *ContosoUniversity.csproj* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="b6585-124">*CU.db* 파일을 삭제하거나 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="b6585-125">초기 마이그레이션 만들기</span><span class="sxs-lookup"><span data-stu-id="b6585-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6585-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6585-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6585-127">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6585-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6585-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6585-129">명령 프롬프트가 프로젝트 폴더에 있는지 확인하고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="b6585-130">Up 및 Down 메서드</span><span class="sxs-lookup"><span data-stu-id="b6585-130">Up and Down methods</span></span>

<span data-ttu-id="b6585-131">EF Core `migrations add` 명령은 데이터베이스를 만드는 코드를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="b6585-132">이 마이그레이션 코드는 *Migrations\<timestamp>_InitialCreate.cs* 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="b6585-133">`InitialCreate` 클래스의 `Up` 메서드는 데이터 모델 엔터티 집합에 해당하는 데이터베이스 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="b6585-134">`Down` 메서드는 다음 예제처럼 테이블을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="b6585-135">위의 코드는 초기 마이그레이션에 대한 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="b6585-136">코드:</span><span class="sxs-lookup"><span data-stu-id="b6585-136">The code:</span></span>

* <span data-ttu-id="b6585-137">`migrations add InitialCreate` 명령에서 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="b6585-138">`database update` 명령에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="b6585-139">데이터베이스 컨텍스트 클래스에 지정된 데이터 모델의 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="b6585-140">파일 이름에는 마이그레이션 이름 매개 변수(이 예제에서 "InitialCreate")가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="b6585-141">마이그레이션 이름은 임의의 유효한 파일 이름이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="b6585-142">마이그레이션에서 수행 중인 작업을 요약한 단어 또는 구를 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="b6585-143">예를 들어 부서 테이블을 추가한 마이그레이션은 "AddDepartmentTable"이라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="b6585-144">마이그레이션 기록 테이블</span><span class="sxs-lookup"><span data-stu-id="b6585-144">The migrations history table</span></span>

* <span data-ttu-id="b6585-145">SSOX 또는 SQLite 도구를 사용하여 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="b6585-146">`__EFMigrationsHistory` 테이블이 추가된 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="b6585-147">`__EFMigrationsHistory` 테이블은 데이터베이스에 적용된 마이그레이션을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="b6585-148">`__EFMigrationsHistory` 테이블의 데이터를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="b6585-149">첫 번째 마이그레이션에 대해 한 행을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="b6585-150">데이터 모델 스냅샷</span><span class="sxs-lookup"><span data-stu-id="b6585-150">The data model snapshot</span></span>

<span data-ttu-id="b6585-151">마이그레이션에서는 현재 데이터 모델의 ‘스냅샷’을 *Migrations/SchoolContextModelSnapshot.cs* 에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="b6585-152">마이그레이션을 추가하면 EF가 현재 데이터 모델을 스냅샷 파일과 비교하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="b6585-153">스냅샷 파일이 데이터 모델의 상태를 추적하므로 `<timestamp>_<migrationname>.cs` 파일을 삭제하여 마이그레이션을 삭제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="b6585-154">가장 최근 마이그레이션을 백업하려면 `migrations remove` 명령을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="b6585-155">이 명령은 마이그레이션을 삭제하고 스냅샷이 올바르게 다시 설정되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="b6585-156">자세한 내용은 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6585-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="b6585-157">EnsureCreated 제거</span><span class="sxs-lookup"><span data-stu-id="b6585-157">Remove EnsureCreated</span></span>

<span data-ttu-id="b6585-158">이 자습서 시리즈는 `EnsureCreated`를 사용하여 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="b6585-159">`EnsureCreated`는 마이그레이션 기록 테이블을 만들지 않으므로 마이그레이션과 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="b6585-160">데이터베이스를 삭제하고 자주 다시 생성하는 테스트 또는 신속한 프로토타입 만들기를 위해 디자인되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="b6585-161">이 지점부터 자습서에서는 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="b6585-162">*Data/DBInitializer.cs* 에서 다음 줄을 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="b6585-163">앱을 실행하고 데이터베이스가 시드되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="b6585-164">프로덕션 환경에서 마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="b6585-164">Applying migrations in production</span></span>

<span data-ttu-id="b6585-165">프로덕션 앱은 애플리케이션 시작 시 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)를 호출하지 **않는 것이** 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="b6585-166">`Migrate`는 서버 팜에 배포된 앱에서 호출하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="b6585-167">앱이 여러 서버 인스턴스로 확장되는 경우 데이터베이스 스키마 업데이트가 여러 서버에서 발생하거나 읽기/쓰기 권한과 충돌하지 않도록 하는 것이 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="b6585-168">데이터베이스 마이그레이션은 배포의 일부로 제어된 방식으로 수행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="b6585-169">프로덕션 데이터베이스 마이그레이션 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="b6585-170">마이그레이션을 사용하여 SQL 스크립트를 작성하고 배포 시 SQL 스크립트 사용</span><span class="sxs-lookup"><span data-stu-id="b6585-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="b6585-171">제어된 환경에서 `dotnet ef database update` 실행</span><span class="sxs-lookup"><span data-stu-id="b6585-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b6585-172">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b6585-172">Troubleshooting</span></span>

<span data-ttu-id="b6585-173">앱에서 SQL Server LocalDB를 사용하고 다음 예외를 표시하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b6585-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="b6585-174">솔루션은 명령 프롬프트에서 `dotnet ef database update`를 실행해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="b6585-175">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b6585-175">Additional resources</span></span>

* <span data-ttu-id="b6585-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="b6585-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="b6585-177">패키지 관리자 콘솔(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="b6585-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="b6585-178">다음 단계</span><span class="sxs-lookup"><span data-stu-id="b6585-178">Next steps</span></span>

<span data-ttu-id="b6585-179">다음 자습서에서는 데이터 모델을 빌드하여 엔터티 속성 및 새 엔터티를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b6585-180">[이전 자습서](xref:data/ef-rp/sort-filter-page)
> [다음 자습서](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="b6585-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b6585-181">이 자습서에서는 데이터 모델 변경 관리를 위한 EF Core 마이그레이션 기능이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="b6585-182">해결할 수 없는 문제가 발생한 경우 [완성된 앱](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="b6585-183">새 앱이 개발되면 데이터 모델이 자주 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="b6585-184">모델이 변경될 때마다 모델이 데이터베이스와 동기화 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="b6585-185">이 자습서는 존재하지 않는 경우 데이터베이스를 만들도록 Entity Framework를 구성하여 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="b6585-186">데이터 모델이 변경될 때마다 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-186">Each time the data model changes:</span></span>

* <span data-ttu-id="b6585-187">DB가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-187">The DB is dropped.</span></span>
* <span data-ttu-id="b6585-188">EF는 모델과 일치하는 새 항목을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="b6585-189">앱은 테스트 데이터로 DB를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="b6585-190">DB를 데이터 모델과 동기화된 상태로 유지하는 이 접근 방식은 앱을 프로덕션 환경에 배포할 때까지 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="b6585-191">앱이 프로덕션 환경에서 실행 중인 경우 일반적으로 유지 관리해야 하는 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="b6585-192">앱은 변경(예: 새 열 추가)될 때마다 테스트 DB로 시작될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="b6585-193">EF Core 마이그레이션 기능은 EF Core에서 새 DB를 만드는 대신 DB 스키마를 업데이트하도록 하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="b6585-194">데이터 모델이 변경될 때 DB를 삭제하고 다시 작성하는 대신 마이그레이션은 스키마를 업데이트하고 기존 데이터를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="b6585-195">데이터베이스 삭제</span><span class="sxs-lookup"><span data-stu-id="b6585-195">Drop the database</span></span>

<span data-ttu-id="b6585-196">SSOX(**SQL Server 개체 탐색기**) 또는 `database drop` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6585-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6585-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6585-198">PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="b6585-199">PMC에서 `Get-Help about_EntityFrameworkCore`를 실행하여 도움말 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6585-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6585-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b6585-201">명령 창을 열고 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="b6585-202">프로젝트 폴더에는 *Startup.cs* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="b6585-203">명령 창에서 다음을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="b6585-204">초기 마이그레이션 만들기 및 DB 업데이트</span><span class="sxs-lookup"><span data-stu-id="b6585-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="b6585-205">프로젝트를 빌드하고 첫 번째 마이그레이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6585-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6585-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6585-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6585-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="b6585-208">Up 및 Down 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="b6585-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="b6585-209">EF Core `migrations add` 명령은 DB를 생성하는 코드를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="b6585-210">이 마이그레이션 코드는 *Migrations\<timestamp>_InitialCreate.cs* 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="b6585-211">`InitialCreate` 클래스dml `Up` 메서드는 데이터 모델 엔터티 집합에 해당하는 DB 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="b6585-212">`Down` 메서드는 다음 예제처럼 테이블을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="b6585-213">마이그레이션에서는 마이그레이션을 위한 데이터 모델 변경을 구현하기 위해 `Up` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="b6585-214">업데이트를 롤백하는 명령을 입력하면 마이그레이션에서 `Down` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="b6585-215">위의 코드는 초기 마이그레이션에 대한 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="b6585-216">이 코드는 `migrations add InitialCreate` 명령을 실행할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="b6585-217">파일 이름에는 마이그레이션 이름 매개 변수(이 예제에서 "InitialCreate")가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="b6585-218">마이그레이션 이름은 임의의 유효한 파일 이름이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="b6585-219">마이그레이션에서 수행 중인 작업을 요약한 단어 또는 구를 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="b6585-220">예를 들어 부서 테이블을 추가한 마이그레이션은 "AddDepartmentTable"이라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="b6585-221">초기 마이그레이션이 생성되었고 DB가 존재하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b6585-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="b6585-222">DB 만들기 코드가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="b6585-223">DB는 데이터 모델과 이미 일치하므로 DB 만들기 코드를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="b6585-224">DB 만들기 코드가 실행되면 DB가 데이터 모델과 이미 일치하므로 아무것도 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="b6585-225">앱이 새 환경에 배포되면 DB 만들기 코드를 실행하여 DB를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="b6585-226">이전에 DB는 삭제되었거나 존재하지 않으므로 마이그레이션에서 새 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="b6585-227">데이터 모델 스냅샷</span><span class="sxs-lookup"><span data-stu-id="b6585-227">The data model snapshot</span></span>

<span data-ttu-id="b6585-228">마이그레이션은 *Migrations/SchoolContextModelSnapshot.cs* 에 현재 데이터베이스 스키마의 *스냅숏* 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="b6585-229">마이그레이션을 추가하면 EF가 데이터 모델을 스냅샷 파일과 비교하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="b6585-230">마이그레이션을 삭제하려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6585-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6585-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6585-232">마이그레이션 제거</span><span class="sxs-lookup"><span data-stu-id="b6585-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b6585-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b6585-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="b6585-234">자세한 내용은 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6585-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="b6585-235">마이그레이션 제거 명령은 마이그레이션을 삭제하고 스냅샷이 올바르게 다시 설정되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="b6585-236">EnsureCreated 제거 및 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="b6585-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="b6585-237">초기 개발에는 `EnsureCreated`가 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="b6585-238">이 자습서에서는 마이그레이션이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="b6585-239">`EnsureCreated`에는 다음과 같은 제한 사항이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="b6585-240">마이그레이션을 무시하고 DB 및 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="b6585-241">마이그레이션 테이블을 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="b6585-242">마이그레이션에 사용할 수 *없습니다*.</span><span class="sxs-lookup"><span data-stu-id="b6585-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="b6585-243">DB를 삭제하고 자주 다시 생성하는 테스트 또는 신속한 프로토타입 만들기를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="b6585-244">`EnsureCreated`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="b6585-245">앱을 실행하고 DB이 시드되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="b6585-246">데이터베이스 검사</span><span class="sxs-lookup"><span data-stu-id="b6585-246">Inspect the database</span></span>

<span data-ttu-id="b6585-247">**SQL Server 개체 탐색기** 를 사용하여 DB를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="b6585-248">`__EFMigrationsHistory` 테이블이 추가된 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="b6585-249">`__EFMigrationsHistory` 테이블은 DB에 적용된 마이그레이션을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="b6585-250">`__EFMigrationsHistory` 테이블의 데이터를 보면 첫 번째 마이그레이션에 대해 한 행이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="b6585-251">앞의 CLI 출력 예제의 마지막 로그는 이 행을 만드는 INSERT 문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="b6585-252">앱을 실행하고 모든 항목이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="b6585-253">프로덕션 환경에서 마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="b6585-253">Applying migrations in production</span></span>

<span data-ttu-id="b6585-254">프로덕션 앱은 애플리케이션 시작 시 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)를 호출하지 **않는 것이** 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="b6585-255">서버 팜의 앱에서 `Migrate`를 호출하면 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="b6585-256">예를 들어, 앱이 스케일 아웃으로 클라우드에 배포된 경우(앱의 여러 인스턴스가 실행 중임)</span><span class="sxs-lookup"><span data-stu-id="b6585-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="b6585-257">데이터베이스 마이그레이션은 배포의 일부로 제어된 방식으로 수행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="b6585-258">프로덕션 데이터베이스 마이그레이션 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="b6585-259">마이그레이션을 사용하여 SQL 스크립트를 작성하고 배포 시 SQL 스크립트 사용</span><span class="sxs-lookup"><span data-stu-id="b6585-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="b6585-260">제어된 환경에서 `dotnet ef database update` 실행</span><span class="sxs-lookup"><span data-stu-id="b6585-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="b6585-261">EF Core는 `__MigrationsHistory` 테이블을 사용하여 마이그레이션을 실행해야 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="b6585-262">DB가 최신 상태이면 마이그레이션이 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b6585-263">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b6585-263">Troubleshooting</span></span>

<span data-ttu-id="b6585-264">[완료된 앱](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="b6585-265">앱에서는 다음과 같은 예외가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="b6585-266">해결책: `dotnet ef database update`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6585-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="b6585-267">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b6585-267">Additional resources</span></span>

* [<span data-ttu-id="b6585-268">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="b6585-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="b6585-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)</span><span class="sxs-lookup"><span data-stu-id="b6585-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="b6585-270">패키지 관리자 콘솔(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="b6585-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="b6585-271">[이전](xref:data/ef-rp/sort-filter-page)
> [다음](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="b6585-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

