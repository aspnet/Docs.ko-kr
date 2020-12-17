---
title: '4부: 데이터베이스 작업'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 09/26/2020
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 552a74016c281af248ce735c6ed6a5a55768a16a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486244"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="cd59e-103">Razor Pages에 대한 자습서 시리즈의 4부</span><span class="sxs-lookup"><span data-stu-id="cd59e-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="cd59e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="cd59e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cd59e-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cd59e-106">`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="cd59e-107">데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-109">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="cd59e-110">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString` 키를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="cd59e-111">로컬 개발의 경우 구성은 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cd59e-113">생성된 연결 문자열은 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-114">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="cd59e-115">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 테스트 또는 프로덕션 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="cd59e-116">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="cd59e-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="cd59e-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="cd59e-119">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="cd59e-120">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="cd59e-121">기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="cd59e-122">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![보기 메뉴](sql/_static/5/ssox.png)

1. <span data-ttu-id="cd59e-124">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/5/design.png)

   ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

   <span data-ttu-id="cd59e-127">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="cd59e-128">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="cd59e-129">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-131">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="cd59e-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="cd59e-132">SQLite</span></span>

<span data-ttu-id="cd59e-133">[SQLite](https://www.sqlite.org/) 웹 사이트 상태:</span><span class="sxs-lookup"><span data-stu-id="cd59e-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="cd59e-134">SQLite는 신뢰성 높고 통합 기능을 제공하는 자체 포함된 임베디드 공용 도메인 SQL 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="cd59e-135">SQLite는 전 세계에서 가장 널리 사용되는 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="cd59e-136">여러 타사 도구를 다운로드하여 SQLite 데이터베이스를 관리하고 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="cd59e-137">다음은 [DB Browser for SQLite](https://sqlitebrowser.org/)의 이미지입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="cd59e-138">다른 SQLite 도구를 사용할 경우 그에 관한 의견을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![영화 데이터베이스를 보여 주는 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="cd59e-140">이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="cd59e-141">마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="cd59e-142">그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="cd59e-143">예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="cd59e-144">열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="cd59e-145">이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="cd59e-146">대신 스키마가 변경되면 데이터베이스가 삭제되고 다시 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="cd59e-147">SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="cd59e-148">테이블 다시 빌드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="cd59e-149">새 테이블 만들기.</span><span class="sxs-lookup"><span data-stu-id="cd59e-149">Creating a new table.</span></span>
>* <span data-ttu-id="cd59e-150">이전 테이블에서 새 테이블로 데이터 복사.</span><span class="sxs-lookup"><span data-stu-id="cd59e-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="cd59e-151">이전 테이블 삭제.</span><span class="sxs-lookup"><span data-stu-id="cd59e-151">Dropping the old table.</span></span>
>* <span data-ttu-id="cd59e-152">새 테이블 이름 바꾸기.</span><span class="sxs-lookup"><span data-stu-id="cd59e-152">Renaming the new table.</span></span>
>
><span data-ttu-id="cd59e-153">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="cd59e-154">SQLite EF Core 데이터베이스 공급자 제한 사항</span><span class="sxs-lookup"><span data-stu-id="cd59e-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="cd59e-155">마이그레이션 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="cd59e-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="cd59e-156">데이터 시드</span><span class="sxs-lookup"><span data-stu-id="cd59e-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="cd59e-157">SQLite ALTER TABLE 문</span><span class="sxs-lookup"><span data-stu-id="cd59e-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="cd59e-158">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="cd59e-158">Seed the database</span></span>

<span data-ttu-id="cd59e-159">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="cd59e-160">데이터베이스에 영화가 있는 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="cd59e-161">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="cd59e-161">Add the seed initializer</span></span>

<span data-ttu-id="cd59e-162">*Program.cs* 의 내용을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="cd59e-163">위의 코드에서 다음을 수행하도록 `Main` 메서드가 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="cd59e-164">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="cd59e-165">`seedData.Initialize` 메서드를 호출하여 데이터베이스 컨텍스트 인스턴스에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="cd59e-166">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="cd59e-167">[using 문](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)은 컨텍스트가 삭제되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="cd59e-168">`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="cd59e-169">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cd59e-171">데이터베이스의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-171">Delete all the records in the database.</span></span> <span data-ttu-id="cd59e-172">브라우저에서 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="cd59e-173">`Startup` 클래스에서 메서드를 호출하여 앱을 강제로 초기화하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="cd59e-174">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="cd59e-175">다음 중 한 가지 방법을 사용하여 IIS를 중지했다가 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="cd59e-176">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

   1. <span data-ttu-id="cd59e-179">앱이 비디버그 모드에서 실행 중인 경우 <kbd>F5</kbd>를 눌러 디버그 모드에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="cd59e-180">앱이 디버그 모드인 경우 디버거를 중지하고 <kbd>F5</kbd>를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-181">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cd59e-182">데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cd59e-183">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="cd59e-184">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-184">The app shows the seeded data:</span></span>

![영화 데이터를 표시하는 Movie 애플리케이션이 열린 브라우저](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="cd59e-186">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="cd59e-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cd59e-187">[이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="cd59e-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="cd59e-188">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cd59e-189">`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="cd59e-190">데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-192">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="cd59e-193">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString` 키를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="cd59e-194">로컬 개발의 경우 구성은 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cd59e-196">생성된 연결 문자열은 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-197">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="cd59e-198">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 테스트 또는 프로덕션 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="cd59e-199">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="cd59e-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="cd59e-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="cd59e-202">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="cd59e-203">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="cd59e-204">기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="cd59e-205">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="cd59e-207">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

<span data-ttu-id="cd59e-210">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="cd59e-211">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="cd59e-212">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-214">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="cd59e-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="cd59e-215">SQLite</span></span>

<span data-ttu-id="cd59e-216">[SQLite](https://www.sqlite.org/) 웹 사이트 상태:</span><span class="sxs-lookup"><span data-stu-id="cd59e-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="cd59e-217">SQLite는 신뢰성 높고 통합 기능을 제공하는 자체 포함된 임베디드 공용 도메인 SQL 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="cd59e-218">SQLite는 전 세계에서 가장 널리 사용되는 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="cd59e-219">여러 타사 도구를 다운로드하여 SQLite 데이터베이스를 관리하고 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="cd59e-220">다음은 [DB Browser for SQLite](https://sqlitebrowser.org/)의 이미지입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="cd59e-221">다른 SQLite 도구를 사용할 경우 그에 관한 의견을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![영화 데이터베이스를 보여 주는 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="cd59e-223">이 자습서에서는 가능한 경우 Entity Framework Core 마이그레이션 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="cd59e-224">마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="cd59e-225">그러나 마이그레이션은 EF Core 공급자가 지원하는 유형의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한적입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="cd59e-226">예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="cd59e-227">열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="cd59e-228">이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="cd59e-229">대신 스키마가 변경되면 데이터베이스가 삭제되고 다시 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="cd59e-230">SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경되면 테이블을 다시 빌드하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="cd59e-231">테이블 다시 빌드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="cd59e-232">새 테이블 만들기.</span><span class="sxs-lookup"><span data-stu-id="cd59e-232">Creating a new table.</span></span>
>* <span data-ttu-id="cd59e-233">이전 테이블에서 새 테이블로 데이터 복사.</span><span class="sxs-lookup"><span data-stu-id="cd59e-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="cd59e-234">이전 테이블 삭제.</span><span class="sxs-lookup"><span data-stu-id="cd59e-234">Dropping the old table.</span></span>
>* <span data-ttu-id="cd59e-235">새 테이블 이름 바꾸기.</span><span class="sxs-lookup"><span data-stu-id="cd59e-235">Renaming the new table.</span></span>
>
><span data-ttu-id="cd59e-236">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="cd59e-237">SQLite EF Core 데이터베이스 공급자 제한 사항</span><span class="sxs-lookup"><span data-stu-id="cd59e-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="cd59e-238">마이그레이션 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="cd59e-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="cd59e-239">데이터 시드</span><span class="sxs-lookup"><span data-stu-id="cd59e-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="cd59e-240">SQLite ALTER TABLE 문</span><span class="sxs-lookup"><span data-stu-id="cd59e-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="cd59e-241">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="cd59e-241">Seed the database</span></span>

<span data-ttu-id="cd59e-242">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="cd59e-243">데이터베이스에 영화가 있는 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="cd59e-244">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="cd59e-244">Add the seed initializer</span></span>

<span data-ttu-id="cd59e-245">*Program.cs* 의 내용을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="cd59e-246">위의 코드에서 다음을 수행하도록 `Main` 메서드가 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="cd59e-247">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="cd59e-248">`seedData.Initialize` 메서드를 호출하여 데이터베이스 컨텍스트 인스턴스에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="cd59e-249">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="cd59e-250">[using 문](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)은 컨텍스트가 삭제되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="cd59e-251">`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="cd59e-252">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cd59e-254">데이터베이스의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-254">Delete all the records in the database.</span></span> <span data-ttu-id="cd59e-255">브라우저에서 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="cd59e-256">`Startup` 클래스에서 메서드를 호출하여 앱을 강제로 초기화하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="cd59e-257">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="cd59e-258">다음 중 한 가지 방법을 사용하여 IIS를 중지했다가 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="cd59e-259">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * <span data-ttu-id="cd59e-262">앱이 비디버그 모드에서 실행 중인 경우 <kbd>F5</kbd>를 눌러 디버그 모드에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="cd59e-263">앱이 디버그 모드인 경우 디버거를 중지하고 <kbd>F5</kbd>를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-264">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cd59e-265">데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cd59e-266">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="cd59e-267">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-267">The app shows the seeded data:</span></span>

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="cd59e-269">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="cd59e-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cd59e-270">[이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="cd59e-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd59e-271">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cd59e-272">`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="cd59e-273">데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-275">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="cd59e-276">`ConfigureServices`에서 사용되는 메서드에 대한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="cd59e-277">`CookiePolicyOptions`에 대한 [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="cd59e-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="cd59e-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="cd59e-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="cd59e-279">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString` 키를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="cd59e-280">로컬 개발의 경우 구성은 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cd59e-282">생성된 연결 문자열은 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cd59e-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cd59e-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-284">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="cd59e-285">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 테스트 또는 프로덕션 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="cd59e-286">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cd59e-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="cd59e-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="cd59e-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="cd59e-289">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="cd59e-290">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="cd59e-291">기본적으로 LocalDB 데이터베이스는 `C:/Users/<user/>` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="cd59e-292">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="cd59e-294">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](sql/_static/design.png)

  ![디자이너에 열린 Movie 테이블](sql/_static/dv.png)

<span data-ttu-id="cd59e-297">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="cd59e-298">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="cd59e-299">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="cd59e-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cd59e-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-302">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="cd59e-303">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="cd59e-303">Seed the database</span></span>

<span data-ttu-id="cd59e-304">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="cd59e-305">데이터베이스에 영화가 있는 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="cd59e-306">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="cd59e-306">Add the seed initializer</span></span>

<span data-ttu-id="cd59e-307">*Program.cs* 의 내용을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="cd59e-308">위의 코드에서 다음을 수행하도록 `Main` 메서드가 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="cd59e-309">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="cd59e-310">`seedData.Initialize` 메서드를 호출하여 데이터베이스 컨텍스트 인스턴스에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="cd59e-311">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="cd59e-312">[using 문](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)은 컨텍스트가 삭제되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="cd59e-313">프로덕션 앱은 `Database.Migrate`를 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="cd59e-314">`Update-Database`가 실행되지 않는 경우 다음 예외를 방지하기 위해 위의 코드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="cd59e-315">SqlException: 로그인에서 요청한 “RazorPagesMovieContext-21” 데이터베이스를 열 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="cd59e-316">로그인에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-316">The login failed.</span></span>
<span data-ttu-id="cd59e-317">'user name' 사용자에 대한 로그인에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="cd59e-318">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cd59e-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cd59e-320">데이터베이스의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-320">Delete all the records in the database.</span></span> <span data-ttu-id="cd59e-321">브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="cd59e-322">`Startup` 클래스에서 메서드를 호출하여 앱을 강제로 초기화하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="cd59e-323">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="cd59e-324">다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="cd59e-325">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * <span data-ttu-id="cd59e-328">앱이 비디버그 모드에서 실행 중인 경우 <kbd>F5</kbd>를 눌러 디버그 모드에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="cd59e-329">앱이 디버그 모드인 경우 디버거를 중지하고 <kbd>F5</kbd>를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cd59e-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cd59e-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cd59e-331">데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cd59e-332">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cd59e-333">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd59e-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cd59e-334">데이터베이스의 모든 레코드를 삭제하면 시드 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="cd59e-335">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="cd59e-336">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-336">The app shows the seeded data:</span></span>

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55https.png)

<span data-ttu-id="cd59e-338">다음 자습서는 데이터의 표현을 정리합니다.</span><span class="sxs-lookup"><span data-stu-id="cd59e-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd59e-339">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cd59e-339">Additional resources</span></span>

* [<span data-ttu-id="cd59e-340">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="cd59e-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="cd59e-341">[이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="cd59e-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
