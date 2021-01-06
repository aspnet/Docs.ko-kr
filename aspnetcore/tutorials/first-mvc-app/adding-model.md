---
title: 4부. ASP.NET Core MVC 앱에 모델 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: bfda45afeea67a11ad775996d94a06125df08bc6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854589"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="7679e-103">4부. ASP.NET Core MVC 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="7679e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7679e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="7679e-105">이 섹션에서는 데이터베이스에서 영화를 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="7679e-106">이러한 클래스는 **M** VC 앱의 "**M** odel" 부분이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="7679e-107">이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="7679e-108">EF Core는 작성해야 할 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="7679e-109">직접 만들게 될 모델 클래스는 EF Core에 대한 어떠한 종속성도 없으므로 POCO(**P** lain **O** ld **C** LR **O** bject) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7679e-110">이 클래스는 데이터베이스에 저장될 데이터의 속성만 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="7679e-111">이 자습서에서는 먼저 모델 클래스를 작성하고 EF Core가 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="7679e-112">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-114">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="7679e-115">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7679e-117">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7679e-119">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="7679e-120">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="7679e-121">다음 코드로 *Movie.cs* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="7679e-122">`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="7679e-123">`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7679e-124">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="7679e-124">With this attribute:</span></span>

* <span data-ttu-id="7679e-125">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="7679e-126">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7679e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="7679e-128">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-130">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7679e-132">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="7679e-133">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="7679e-134">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="7679e-135">추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-137">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7679e-138">**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="7679e-139">오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="7679e-140">일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="7679e-142">`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="7679e-143">**확인** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-143">Press the **Ok** button.</span></span>

<span data-ttu-id="7679e-144">**라이선스 승인** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="7679e-145">필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="7679e-146">위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="7679e-147">다음 .NET CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="7679e-148">앞의 명령은 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="7679e-149">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="7679e-149">Create a database context class</span></span>

<span data-ttu-id="7679e-150">`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="7679e-151">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="7679e-152">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-152">Create a *Data* folder.</span></span>

<span data-ttu-id="7679e-153">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="7679e-154">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7679e-155">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7679e-156">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="7679e-157">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="7679e-157">Register the database context</span></span>

<span data-ttu-id="7679e-158">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7679e-159">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="7679e-160">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7679e-161">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="7679e-162">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="7679e-163">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="7679e-164">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-166">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="7679e-167">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7679e-168">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="7679e-169">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-169">Add a database connection string</span></span>

<span data-ttu-id="7679e-170">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-172">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="7679e-173">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="7679e-174">영화 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="7679e-174">Scaffold movie pages</span></span>

<span data-ttu-id="7679e-175">스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-177">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="7679e-179">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="7679e-181">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="7679e-182">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="7679e-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="7679e-183">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="7679e-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc5.png)

* <span data-ttu-id="7679e-185">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="7679e-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="7679e-186">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="7679e-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="7679e-187">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-187">Select **Add**</span></span>

<span data-ttu-id="7679e-188">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="7679e-188">Visual Studio creates:</span></span>

* <span data-ttu-id="7679e-189">영화 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="7679e-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="7679e-190">Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="7679e-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="7679e-191">이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="7679e-193">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7679e-194">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="7679e-195">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-196">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7679e-197">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7679e-198">스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="7679e-199">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="7679e-200">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="7679e-201">앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="7679e-202">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="7679e-202">Initial migration</span></span>

<span data-ttu-id="7679e-203">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="7679e-204">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-206">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="7679e-207">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="7679e-208">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="7679e-209">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="7679e-210">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="7679e-211">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="7679e-212">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="7679e-213">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="7679e-214">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="7679e-215">데이터베이스 수정 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="7679e-216">No type was specified for the decimal column ‘Price’ on entity type ‘Movie’.</span><span class="sxs-lookup"><span data-stu-id="7679e-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7679e-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="7679e-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7679e-218">Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="7679e-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="7679e-219">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-220">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="7679e-221">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="7679e-222">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="7679e-223">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="7679e-224">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="7679e-225">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="7679e-226">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="7679e-227">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="7679e-228">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="7679e-229">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="7679e-229">The InitialCreate class</span></span>

<span data-ttu-id="7679e-230">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="7679e-231">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="7679e-232">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="7679e-233">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-233">Test the app</span></span>

* <span data-ttu-id="7679e-234">앱을 실행하고 **Movie App** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="7679e-235">다음 중 하나와 비슷한 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="7679e-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-237">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="7679e-238">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="7679e-239">**Create** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-239">Test the **Create** page.</span></span> <span data-ttu-id="7679e-240">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7679e-241">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7679e-242">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7679e-243">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7679e-244">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="7679e-245">컨트롤러에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="7679e-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-247">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="7679e-248">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="7679e-249">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-250">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="7679e-251">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="7679e-252">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="7679e-253">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="7679e-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="7679e-254">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="7679e-255">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="7679e-256">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="7679e-257">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="7679e-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="7679e-258">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="7679e-259">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="7679e-260">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="7679e-261">강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="7679e-262">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="7679e-263">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="7679e-264">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="7679e-265">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="7679e-266">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="7679e-267">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="7679e-268">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="7679e-269">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="7679e-270">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="7679e-271">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="7679e-272">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="7679e-273">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="7679e-274">보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="7679e-275">영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="7679e-276">`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="7679e-277">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="7679e-278">예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="7679e-279">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="7679e-280">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="7679e-281">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="7679e-282">이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="7679e-283">영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="7679e-284">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="7679e-285">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="7679e-286">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="7679e-287">즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7679e-288">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7679e-288">Additional resources</span></span>

* [<span data-ttu-id="7679e-289">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7679e-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="7679e-290">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="7679e-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="7679e-291">[이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="7679e-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="7679e-292">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-294">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="7679e-295">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7679e-297">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-298">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7679e-299">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="7679e-300">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="7679e-301">다음 코드로 *Movie.cs* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="7679e-302">`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="7679e-303">`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7679e-304">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="7679e-304">With this attribute:</span></span>

* <span data-ttu-id="7679e-305">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="7679e-306">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7679e-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="7679e-308">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-310">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7679e-312">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="7679e-313">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="7679e-314">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="7679e-315">추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-317">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7679e-318">**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="7679e-319">오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="7679e-320">일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="7679e-322">`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="7679e-323">**확인** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-323">Press the **Ok** button.</span></span>

<span data-ttu-id="7679e-324">**라이선스 승인** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="7679e-325">필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="7679e-326">위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="7679e-327">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="7679e-327">Create a database context class</span></span>

<span data-ttu-id="7679e-328">`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="7679e-329">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="7679e-330">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-330">Create a *Data* folder.</span></span>

<span data-ttu-id="7679e-331">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="7679e-332">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7679e-333">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7679e-334">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="7679e-335">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="7679e-335">Register the database context</span></span>

<span data-ttu-id="7679e-336">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7679e-337">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="7679e-338">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7679e-339">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="7679e-340">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="7679e-341">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="7679e-342">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-344">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="7679e-345">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7679e-346">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="7679e-347">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-347">Add a database connection string</span></span>

<span data-ttu-id="7679e-348">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-350">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="7679e-351">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="7679e-352">영화 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="7679e-352">Scaffold movie pages</span></span>

<span data-ttu-id="7679e-353">스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-355">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="7679e-357">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="7679e-359">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="7679e-360">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="7679e-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="7679e-361">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="7679e-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc3.png)

* <span data-ttu-id="7679e-363">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="7679e-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="7679e-364">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="7679e-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="7679e-365">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-365">Select **Add**</span></span>

<span data-ttu-id="7679e-366">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="7679e-366">Visual Studio creates:</span></span>

* <span data-ttu-id="7679e-367">영화 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="7679e-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="7679e-368">Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="7679e-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="7679e-369">이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="7679e-371">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7679e-372">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="7679e-373">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-374">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7679e-375">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7679e-376">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="7679e-377">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="7679e-378">앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="7679e-379">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="7679e-379">Initial migration</span></span>

<span data-ttu-id="7679e-380">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="7679e-381">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-383">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="7679e-384">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="7679e-385">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="7679e-386">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="7679e-387">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="7679e-388">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="7679e-389">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="7679e-390">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="7679e-391">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="7679e-392">데이터베이스 수정 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="7679e-393">No type was specified for the decimal column ‘Price’ on entity type ‘Movie’.</span><span class="sxs-lookup"><span data-stu-id="7679e-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7679e-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="7679e-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7679e-395">Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="7679e-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="7679e-396">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-397">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="7679e-398">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="7679e-399">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="7679e-400">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="7679e-401">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="7679e-402">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="7679e-403">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="7679e-404">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="7679e-405">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="7679e-406">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="7679e-406">The InitialCreate class</span></span>

<span data-ttu-id="7679e-407">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="7679e-408">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="7679e-409">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="7679e-410">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-410">Test the app</span></span>

* <span data-ttu-id="7679e-411">앱을 실행하고 **Movie App** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="7679e-412">다음 중 하나와 비슷한 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="7679e-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-414">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="7679e-415">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="7679e-416">**Create** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-416">Test the **Create** page.</span></span> <span data-ttu-id="7679e-417">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7679e-418">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7679e-419">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7679e-420">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7679e-421">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="7679e-422">컨트롤러에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="7679e-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-424">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="7679e-425">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="7679e-426">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-427">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="7679e-428">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="7679e-429">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="7679e-430">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="7679e-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="7679e-431">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="7679e-432">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="7679e-433">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="7679e-434">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="7679e-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="7679e-435">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="7679e-436">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="7679e-437">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="7679e-438">강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="7679e-439">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="7679e-440">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="7679e-441">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="7679e-442">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="7679e-443">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="7679e-444">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="7679e-445">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="7679e-446">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="7679e-447">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="7679e-448">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="7679e-449">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="7679e-450">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="7679e-451">보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="7679e-452">영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="7679e-453">`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="7679e-454">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="7679e-455">예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="7679e-456">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="7679e-457">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="7679e-458">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="7679e-459">이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="7679e-460">영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="7679e-461">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="7679e-462">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="7679e-463">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="7679e-464">즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7679e-465">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7679e-465">Additional resources</span></span>

* [<span data-ttu-id="7679e-466">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7679e-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="7679e-467">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="7679e-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="7679e-468">[이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="7679e-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="7679e-469">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="7679e-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-471">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="7679e-472">클래스의 이름을 **Movie** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-473">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7679e-474">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7679e-475">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="7679e-475">Scaffold the movie model</span></span>

<span data-ttu-id="7679e-476">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7679e-477">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-479">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="7679e-481">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="7679e-483">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="7679e-484">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="7679e-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="7679e-485">**데이터 컨텍스트 클래스:** **+** 아이콘을 선택하고 기본값 **MvcMovie.Models.MvcMovieContext** 를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc.png)

* <span data-ttu-id="7679e-487">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="7679e-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="7679e-488">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="7679e-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="7679e-489">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-489">Select **Add**</span></span>

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

<span data-ttu-id="7679e-491">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="7679e-491">Visual Studio creates:</span></span>

* <span data-ttu-id="7679e-492">Entity Framework Core [데이터베이스 컨텍스트 클래스](xref:data/ef-mvc/intro#create-the-database-context)(*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="7679e-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="7679e-493">영화 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="7679e-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="7679e-494">Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="7679e-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="7679e-495">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(생성, 읽기, 수정 및 삭제) 작업 메서드와 보기 및 데이터베이스 컨텍스트의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7679e-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7679e-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7679e-497">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7679e-498">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="7679e-499">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="7679e-500">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7679e-501">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7679e-502">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7679e-503">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="7679e-504">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="7679e-505">응용 프로그램을 실행하고 **Mvc Movie** 링크를 클릭하면 다음과 유사한 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-507">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="7679e-508">데이터베이스를 만들어야 하는데 이를 수행하기 위해 EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="7679e-509">마이그레이션을 통해 데이터 모델과 일치하는 데이터베이스를 만들고 데이터 모델 변경 시 데이터베이스 스키마를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7679e-510">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="7679e-510">Initial migration</span></span>

<span data-ttu-id="7679e-511">이 섹션에서는 다음 작업을 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="7679e-512">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-512">Add an initial migration.</span></span>
* <span data-ttu-id="7679e-513">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7679e-515">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="7679e-517">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="7679e-518">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="7679e-519">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="7679e-520">`Initial` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="7679e-521">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="7679e-522">자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="7679e-523">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-524">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="7679e-525">`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="7679e-526">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="7679e-527">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="7679e-528">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7679e-529">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="7679e-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7679e-530">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7679e-531">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="7679e-532">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7679e-533">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7679e-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7679e-535">스캐폴딩 도구는 자동으로 DB 컨텍스트를 만들고 DI 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="7679e-536">다음 `Startup.ConfigureServices` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7679e-537">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="7679e-538">`MvcMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7679e-539">데이터 컨텍스트(`MvcMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7679e-540">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="7679e-541">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7679e-542">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7679e-543">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7679e-544">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7679e-545">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7679e-546">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7679e-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7679e-547">DB 컨텍스트를 만들고 DI 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7679e-548">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-548">Test the app</span></span>

* <span data-ttu-id="7679e-549">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="7679e-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7679e-550">다음과 비슷한 데이터베이스 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="7679e-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7679e-551">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7679e-552">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-552">Test the **Create** link.</span></span> <span data-ttu-id="7679e-553">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7679e-554">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7679e-555">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7679e-556">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7679e-557">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7679e-558">`Startup` 클래스를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="7679e-559">앞에서 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="7679e-560">`services.AddDbContext<MvcMovieContext>(options =>`는 사용할 데이터베이스 및 연결 문자열을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="7679e-561">`=>`는 [람다 연산자](/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="7679e-562">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="7679e-563">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="7679e-564">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="7679e-565">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="7679e-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="7679e-566">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="7679e-567">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="7679e-568">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="7679e-569">이렇게 강력하게 형식화된 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="7679e-570">스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="7679e-571">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="7679e-572">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="7679e-573">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="7679e-574">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="7679e-575">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="7679e-576">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="7679e-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="7679e-577">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="7679e-578">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="7679e-579">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="7679e-580">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="7679e-581">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="7679e-582">보기 파일 맨 위에 있는 `@model` 문을 포함하여 보기에서 필요로 하는 개체 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="7679e-583">영화 컨트롤러를 만들 때 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문이 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="7679e-584">이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="7679e-585">예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="7679e-586">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="7679e-587">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="7679e-588">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="7679e-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="7679e-589">이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="7679e-590">영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="7679e-591">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="7679e-592">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="7679e-593">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="7679e-594">즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7679e-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7679e-595">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7679e-595">Additional resources</span></span>

* [<span data-ttu-id="7679e-596">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="7679e-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="7679e-597">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="7679e-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="7679e-598">[이전 보기 추가](adding-view.md)
> [다음 데이터베이스 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="7679e-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
