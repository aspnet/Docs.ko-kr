---
title: 4부. 데이터베이스 및 ASP.NET Core 작업
author: rick-anderson
description: ':::no-loc(Razor)::: Pages에 대한 자습서 시리즈의 4부입니다.'
ms.author: riande
ms.date: 7/22/2019
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: d592cf7d8a96a7e4ec2e53418843a186488951be
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058157"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="e69ff-103">4부. 데이터베이스 및 ASP.NET Core 작업</span><span class="sxs-lookup"><span data-stu-id="e69ff-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="e69ff-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="e69ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="e69ff-105">`:::no-loc(Razor):::PagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-105">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="e69ff-106">데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-108">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="e69ff-109">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="e69ff-110">로컬 개발의 경우 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-110">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e69ff-112">데이터베이스의 이름 값(`Database={Database name}`)은 생성된 코드에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="e69ff-113">이름 값은 임의적입니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-114">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="e69ff-115">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 실제 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="e69ff-116">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e69ff-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="e69ff-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e69ff-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e69ff-119">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="e69ff-120">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e69ff-121">기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="e69ff-122">**보기** 메뉴에서 SSOX( **SQL Server 개체 탐색기** )를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="e69ff-124">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-124">Right click on the `Movie` table and select **View Designer** :</span></span>

  ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

<span data-ttu-id="e69ff-127">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="e69ff-128">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="e69ff-129">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-129">Right click on the `Movie` table and select **View Data** :</span></span>

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-131">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="e69ff-132">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="e69ff-132">Seed the database</span></span>

<span data-ttu-id="e69ff-133">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="e69ff-134">DB에 영화가 존재할 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="e69ff-135">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="e69ff-135">Add the seed initializer</span></span>

<span data-ttu-id="e69ff-136">*Program.cs* 에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-136">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="e69ff-137">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e69ff-138">컨텍스트를 전달하는 시드 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e69ff-139">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="e69ff-140">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Program.cs)]

<span data-ttu-id="e69ff-141">`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="e69ff-142">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e69ff-144">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-144">Delete all the records in the DB.</span></span> <span data-ttu-id="e69ff-145">브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="e69ff-146">시드 메서드가 실행되도록 앱을 강제로 초기화합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="e69ff-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="e69ff-147">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="e69ff-148">다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="e69ff-149">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * <span data-ttu-id="e69ff-152">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="e69ff-153">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-154">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e69ff-155">DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록).</span><span class="sxs-lookup"><span data-stu-id="e69ff-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="e69ff-156">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="e69ff-157">앱이 시드된 데이터를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="e69ff-158">다음 자습서는 데이터의 표현을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e69ff-159">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e69ff-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e69ff-160">[이전: 스캐폴드된 :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="e69ff-160">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="e69ff-161">`:::no-loc(Razor):::PagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-161">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="e69ff-162">데이터베이스 컨텍스트는 *Startup.cs* 의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-164">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="e69ff-165">`ConfigureServices`에서 사용되는 메서드에 대한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e69ff-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="e69ff-166">`:::no-loc(Cookie):::PolicyOptions`에 대한 [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="e69ff-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `:::no-loc(Cookie):::PolicyOptions`.</span></span>
* [<span data-ttu-id="e69ff-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="e69ff-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="e69ff-168">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="e69ff-169">로컬 개발의 경우 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-169">For local development, it gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e69ff-171">데이터베이스의 이름 값(`Database={Database name}`)은 생성된 코드에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="e69ff-172">이름 값은 임의적입니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/:::no-loc(appsettings.json):::)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e69ff-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e69ff-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-174">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="e69ff-175">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 실제 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="e69ff-176">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e69ff-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="e69ff-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e69ff-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e69ff-179">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="e69ff-180">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e69ff-181">기본적으로 LocalDB 데이터베이스는 `C:/Users/<user/>` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="e69ff-182">**보기** 메뉴에서 SSOX( **SQL Server 개체 탐색기** )를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="e69ff-184">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-184">Right click on the `Movie` table and select **View Designer** :</span></span>

  ![Movie 테이블에서 열린 상황에 맞는 메뉴](sql/_static/design.png)

  ![디자이너에 열린 Movie 테이블](sql/_static/dv.png)

<span data-ttu-id="e69ff-187">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="e69ff-188">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="e69ff-189">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-189">Right click on the `Movie` table and select **View Data** :</span></span>

  ![테이블 데이터를 보여 주는 열린 Movie 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e69ff-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e69ff-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-192">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="e69ff-193">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="e69ff-193">Seed the database</span></span>

<span data-ttu-id="e69ff-194">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="e69ff-195">DB에 영화가 존재할 경우 시드 이니셜라이저가 반환되고 영화가 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="e69ff-196">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="e69ff-196">Add the seed initializer</span></span>

<span data-ttu-id="e69ff-197">*Program.cs* 에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-197">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="e69ff-198">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e69ff-199">컨텍스트를 전달하는 시드 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e69ff-200">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="e69ff-201">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Program.cs)]

<span data-ttu-id="e69ff-202">프로덕션 앱은 `Database.Migrate`를 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="e69ff-203">`Update-Database`가 실행되지 않는 경우 다음 예외를 방지하기 위해 위의 코드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="e69ff-204">SqlException: 로그인에서 요청한 “:::no-loc(Razor):::PagesMovieContext-21” 데이터베이스를 열 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-204">SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="e69ff-205">로그인에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-205">The login failed.</span></span>
<span data-ttu-id="e69ff-206">'user name' 사용자에 대한 로그인에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="e69ff-207">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e69ff-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e69ff-209">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-209">Delete all the records in the DB.</span></span> <span data-ttu-id="e69ff-210">브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="e69ff-211">시드 메서드가 실행되도록 앱을 강제로 초기화합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="e69ff-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="e69ff-212">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="e69ff-213">다음 중 한 가지 방법을 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="e69ff-214">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지** 를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * <span data-ttu-id="e69ff-217">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="e69ff-218">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e69ff-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e69ff-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e69ff-220">DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록).</span><span class="sxs-lookup"><span data-stu-id="e69ff-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="e69ff-221">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="e69ff-222">앱이 시드된 데이터를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e69ff-223">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e69ff-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e69ff-224">DB의 모든 레코드를 삭제합니다(시드 메서드가 실행되도록).</span><span class="sxs-lookup"><span data-stu-id="e69ff-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="e69ff-225">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="e69ff-226">앱이 시드된 데이터를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="e69ff-227">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-227">The app shows the seeded data:</span></span>

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55.png)

<span data-ttu-id="e69ff-229">다음 자습서는 데이터의 표현을 정리합니다.</span><span class="sxs-lookup"><span data-stu-id="e69ff-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e69ff-230">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e69ff-230">Additional resources</span></span>

* [<span data-ttu-id="e69ff-231">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="e69ff-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="e69ff-232">[이전: 스캐폴드된 :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="e69ff-232">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
