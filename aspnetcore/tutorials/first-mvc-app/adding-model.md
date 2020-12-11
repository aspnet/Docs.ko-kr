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
ms.openlocfilehash: d99d5bfd84c8c7e7d0c479964211591be434330c
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855523"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="129b1-103">4부. ASP.NET Core MVC 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="129b1-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="129b1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="129b1-105">이 섹션에서는 데이터베이스에서 영화를 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="129b1-106">이러한 클래스는 **M** VC 앱의 "**M** odel" 부분이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="129b1-107">이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="129b1-108">EF Core는 작성해야 할 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="129b1-109">직접 만들게 될 모델 클래스는 EF Core에 대한 어떠한 종속성도 없으므로 POCO(**P** lain **O** ld **C** LR **O** bject) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="129b1-110">이 클래스는 데이터베이스에 저장될 데이터의 속성만 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="129b1-111">이 자습서에서는 먼저 모델 클래스를 작성하고 EF Core가 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="129b1-112">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-114">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="129b1-115">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="129b1-117">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="129b1-119">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="129b1-120">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="129b1-121">다음 코드로 *Movie.cs* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="129b1-122">`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="129b1-123">`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="129b1-124">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="129b1-124">With this attribute:</span></span>

* <span data-ttu-id="129b1-125">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="129b1-126">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="129b1-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="129b1-128">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-130">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="129b1-132">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="129b1-133">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="129b1-134">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="129b1-135">추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-137">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="129b1-138">**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="129b1-139">오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="129b1-140">일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="129b1-142">`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="129b1-143">**확인** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-143">Press the **Ok** button.</span></span>

<span data-ttu-id="129b1-144">**라이선스 승인** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="129b1-145">필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="129b1-146">위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="129b1-147">다음 .NET CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="129b1-148">앞의 명령은 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="129b1-149">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="129b1-149">Create a database context class</span></span>

<span data-ttu-id="129b1-150">`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="129b1-151">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="129b1-152">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-152">Create a *Data* folder.</span></span>

<span data-ttu-id="129b1-153">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="129b1-154">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="129b1-155">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="129b1-156">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="129b1-157">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="129b1-157">Register the database context</span></span>

<span data-ttu-id="129b1-158">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="129b1-159">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="129b1-160">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="129b1-161">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="129b1-162">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="129b1-163">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="129b1-164">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-166">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="129b1-167">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="129b1-168">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="129b1-169">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-169">Add a database connection string</span></span>

<span data-ttu-id="129b1-170">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-172">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="129b1-173">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="129b1-174">영화 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="129b1-174">Scaffold movie pages</span></span>

<span data-ttu-id="129b1-175">스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-177">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="129b1-179">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="129b1-181">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="129b1-182">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="129b1-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="129b1-183">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="129b1-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc5.png)

* <span data-ttu-id="129b1-185">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="129b1-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="129b1-186">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="129b1-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="129b1-187">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-187">Select **Add**</span></span>

<span data-ttu-id="129b1-188">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="129b1-188">Visual Studio creates:</span></span>

* <span data-ttu-id="129b1-189">영화 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="129b1-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="129b1-190">Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="129b1-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="129b1-191">이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="129b1-193">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="129b1-194">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="129b1-195">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-196">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="129b1-197">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="129b1-198">스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="129b1-199">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="129b1-200">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="129b1-201">앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="129b1-202">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="129b1-202">Initial migration</span></span>

<span data-ttu-id="129b1-203">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="129b1-204">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-206">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="129b1-207">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="129b1-208">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="129b1-209">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="129b1-210">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="129b1-211">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="129b1-212">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="129b1-213">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="129b1-214">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="129b1-215">데이터베이스 수정 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="129b1-216">No type was specified for the decimal column ‘Price’ on entity type ‘Movie’.</span><span class="sxs-lookup"><span data-stu-id="129b1-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="129b1-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="129b1-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="129b1-218">Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="129b1-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="129b1-219">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-220">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="129b1-221">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="129b1-222">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="129b1-223">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="129b1-224">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="129b1-225">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="129b1-226">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="129b1-227">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="129b1-228">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="129b1-229">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="129b1-229">The InitialCreate class</span></span>

<span data-ttu-id="129b1-230">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="129b1-231">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="129b1-232">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="129b1-233">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-233">Test the app</span></span>

* <span data-ttu-id="129b1-234">앱을 실행하고 **Movie App** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="129b1-235">다음 중 하나와 비슷한 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="129b1-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-237">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="129b1-238">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="129b1-239">**Create** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-239">Test the **Create** page.</span></span> <span data-ttu-id="129b1-240">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="129b1-241">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="129b1-242">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="129b1-243">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="129b1-244">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="129b1-245">컨트롤러에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="129b1-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-247">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="129b1-248">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="129b1-249">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-250">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="129b1-251">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="129b1-252">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="129b1-253">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="129b1-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="129b1-254">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="129b1-255">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="129b1-256">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="129b1-257">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="129b1-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="129b1-258">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="129b1-259">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="129b1-260">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="129b1-261">강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="129b1-262">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="129b1-263">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="129b1-264">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="129b1-265">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="129b1-266">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="129b1-267">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="129b1-268">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="129b1-269">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="129b1-270">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="129b1-271">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="129b1-272">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="129b1-273">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="129b1-274">보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="129b1-275">영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="129b1-276">`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="129b1-277">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="129b1-278">예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="129b1-279">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="129b1-280">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="129b1-281">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="129b1-282">이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="129b1-283">영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="129b1-284">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="129b1-285">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="129b1-286">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="129b1-287">즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="129b1-288">추가 자료</span><span class="sxs-lookup"><span data-stu-id="129b1-288">Additional resources</span></span>

* [<span data-ttu-id="129b1-289">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="129b1-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="129b1-290">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="129b1-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="129b1-291">[이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="129b1-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="129b1-292">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-294">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="129b1-295">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="129b1-297">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-298">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="129b1-299">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="129b1-300">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="129b1-301">다음 코드로 *Movie.cs* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="129b1-302">`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="129b1-303">`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="129b1-304">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="129b1-304">With this attribute:</span></span>

* <span data-ttu-id="129b1-305">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="129b1-306">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="129b1-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="129b1-308">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-310">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="129b1-312">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="129b1-313">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="129b1-314">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="129b1-315">추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-317">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="129b1-318">**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="129b1-319">오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="129b1-320">일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="129b1-322">`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="129b1-323">**확인** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-323">Press the **Ok** button.</span></span>

<span data-ttu-id="129b1-324">**라이선스 승인** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="129b1-325">필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="129b1-326">위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="129b1-327">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="129b1-327">Create a database context class</span></span>

<span data-ttu-id="129b1-328">`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="129b1-329">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="129b1-330">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-330">Create a *Data* folder.</span></span>

<span data-ttu-id="129b1-331">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="129b1-332">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="129b1-333">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="129b1-334">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="129b1-335">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="129b1-335">Register the database context</span></span>

<span data-ttu-id="129b1-336">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="129b1-337">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="129b1-338">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="129b1-339">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="129b1-340">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="129b1-341">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="129b1-342">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-344">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="129b1-345">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="129b1-346">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="129b1-347">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-347">Add a database connection string</span></span>

<span data-ttu-id="129b1-348">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-350">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="129b1-351">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="129b1-352">영화 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="129b1-352">Scaffold movie pages</span></span>

<span data-ttu-id="129b1-353">스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-355">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="129b1-357">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="129b1-359">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="129b1-360">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="129b1-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="129b1-361">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="129b1-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc3.png)

* <span data-ttu-id="129b1-363">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="129b1-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="129b1-364">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="129b1-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="129b1-365">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-365">Select **Add**</span></span>

<span data-ttu-id="129b1-366">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="129b1-366">Visual Studio creates:</span></span>

* <span data-ttu-id="129b1-367">영화 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="129b1-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="129b1-368">Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="129b1-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="129b1-369">이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="129b1-371">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="129b1-372">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="129b1-373">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-374">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="129b1-375">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="129b1-376">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="129b1-377">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="129b1-378">앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="129b1-379">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="129b1-379">Initial migration</span></span>

<span data-ttu-id="129b1-380">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="129b1-381">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-383">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="129b1-384">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="129b1-385">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="129b1-386">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="129b1-387">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="129b1-388">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="129b1-389">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="129b1-390">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="129b1-391">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="129b1-392">데이터베이스 수정 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="129b1-393">No type was specified for the decimal column ‘Price’ on entity type ‘Movie’.</span><span class="sxs-lookup"><span data-stu-id="129b1-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="129b1-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="129b1-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="129b1-395">Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="129b1-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="129b1-396">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-397">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="129b1-398">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="129b1-399">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="129b1-400">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="129b1-401">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="129b1-402">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="129b1-403">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="129b1-404">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="129b1-405">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="129b1-406">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="129b1-406">The InitialCreate class</span></span>

<span data-ttu-id="129b1-407">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="129b1-408">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="129b1-409">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="129b1-410">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-410">Test the app</span></span>

* <span data-ttu-id="129b1-411">앱을 실행하고 **Movie App** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="129b1-412">다음 중 하나와 비슷한 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="129b1-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-414">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="129b1-415">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="129b1-416">**Create** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-416">Test the **Create** page.</span></span> <span data-ttu-id="129b1-417">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="129b1-418">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="129b1-419">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="129b1-420">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="129b1-421">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="129b1-422">컨트롤러에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="129b1-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-424">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="129b1-425">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="129b1-426">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-427">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="129b1-428">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="129b1-429">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="129b1-430">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="129b1-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="129b1-431">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="129b1-432">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="129b1-433">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="129b1-434">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="129b1-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="129b1-435">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="129b1-436">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="129b1-437">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="129b1-438">강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="129b1-439">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="129b1-440">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="129b1-441">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="129b1-442">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="129b1-443">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="129b1-444">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="129b1-445">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="129b1-446">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="129b1-447">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="129b1-448">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="129b1-449">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="129b1-450">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="129b1-451">보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="129b1-452">영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="129b1-453">`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="129b1-454">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="129b1-455">예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="129b1-456">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="129b1-457">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="129b1-458">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="129b1-459">이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="129b1-460">영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="129b1-461">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="129b1-462">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="129b1-463">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="129b1-464">즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="129b1-465">추가 자료</span><span class="sxs-lookup"><span data-stu-id="129b1-465">Additional resources</span></span>

* [<span data-ttu-id="129b1-466">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="129b1-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="129b1-467">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="129b1-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="129b1-468">[이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="129b1-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="129b1-469">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="129b1-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-471">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="129b1-472">클래스의 이름을 **Movie** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-473">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="129b1-474">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="129b1-475">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="129b1-475">Scaffold the movie model</span></span>

<span data-ttu-id="129b1-476">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="129b1-477">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-479">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="129b1-481">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="129b1-483">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="129b1-484">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="129b1-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="129b1-485">**데이터 컨텍스트 클래스:** **+** 아이콘을 선택하고 기본값 **MvcMovie.Models.MvcMovieContext** 를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc.png)

* <span data-ttu-id="129b1-487">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="129b1-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="129b1-488">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="129b1-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="129b1-489">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-489">Select **Add**</span></span>

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

<span data-ttu-id="129b1-491">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="129b1-491">Visual Studio creates:</span></span>

* <span data-ttu-id="129b1-492">Entity Framework Core [데이터베이스 컨텍스트 클래스](xref:data/ef-mvc/intro#create-the-database-context)(*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="129b1-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="129b1-493">영화 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="129b1-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="129b1-494">Create, Delete, Details, Edit, 및 Index 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="129b1-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="129b1-495">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(생성, 읽기, 수정 및 삭제) 작업 메서드와 보기 및 데이터베이스 컨텍스트의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="129b1-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="129b1-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="129b1-497">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="129b1-498">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="129b1-499">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="129b1-500">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="129b1-501">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="129b1-502">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="129b1-503">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="129b1-504">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="129b1-505">응용 프로그램을 실행하고 **Mvc Movie** 링크를 클릭하면 다음과 유사한 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-507">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="129b1-508">데이터베이스를 만들어야 하는데 이를 수행하기 위해 EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="129b1-509">마이그레이션을 통해 데이터 모델과 일치하는 데이터베이스를 만들고 데이터 모델 변경 시 데이터베이스 스키마를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="129b1-510">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="129b1-510">Initial migration</span></span>

<span data-ttu-id="129b1-511">이 섹션에서는 다음 작업을 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="129b1-512">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-512">Add an initial migration.</span></span>
* <span data-ttu-id="129b1-513">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="129b1-515">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="129b1-517">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="129b1-518">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="129b1-519">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="129b1-520">`Initial` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="129b1-521">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="129b1-522">자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="129b1-523">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-524">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="129b1-525">`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="129b1-526">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="129b1-527">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="129b1-528">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="129b1-529">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="129b1-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="129b1-530">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="129b1-531">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="129b1-532">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="129b1-533">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="129b1-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="129b1-535">스캐폴딩 도구는 자동으로 DB 컨텍스트를 만들고 DI 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="129b1-536">다음 `Startup.ConfigureServices` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="129b1-537">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="129b1-538">`MvcMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="129b1-539">데이터 컨텍스트(`MvcMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="129b1-540">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="129b1-541">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="129b1-542">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="129b1-543">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="129b1-544">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="129b1-545">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="129b1-546">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="129b1-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="129b1-547">DB 컨텍스트를 만들고 DI 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="129b1-548">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-548">Test the app</span></span>

* <span data-ttu-id="129b1-549">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="129b1-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="129b1-550">다음과 비슷한 데이터베이스 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="129b1-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="129b1-551">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="129b1-552">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-552">Test the **Create** link.</span></span> <span data-ttu-id="129b1-553">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="129b1-554">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="129b1-555">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="129b1-556">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="129b1-557">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="129b1-558">`Startup` 클래스를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="129b1-559">앞에서 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="129b1-560">`services.AddDbContext<MvcMovieContext>(options =>`는 사용할 데이터베이스 및 연결 문자열을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="129b1-561">`=>`는 [람다 연산자](/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="129b1-562">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="129b1-563">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="129b1-564">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="129b1-565">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="129b1-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="129b1-566">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="129b1-567">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="129b1-568">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="129b1-569">이렇게 강력하게 형식화된 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="129b1-570">스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="129b1-571">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="129b1-572">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="129b1-573">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="129b1-574">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="129b1-575">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="129b1-576">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="129b1-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="129b1-577">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="129b1-578">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="129b1-579">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="129b1-580">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="129b1-581">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="129b1-582">보기 파일 맨 위에 있는 `@model` 문을 포함하여 보기에서 필요로 하는 개체 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="129b1-583">영화 컨트롤러를 만들 때 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문이 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="129b1-584">이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="129b1-585">예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="129b1-586">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="129b1-587">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="129b1-588">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="129b1-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="129b1-589">이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="129b1-590">영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="129b1-591">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="129b1-592">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="129b1-593">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="129b1-594">즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="129b1-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="129b1-595">추가 자료</span><span class="sxs-lookup"><span data-stu-id="129b1-595">Additional resources</span></span>

* [<span data-ttu-id="129b1-596">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="129b1-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="129b1-597">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="129b1-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="129b1-598">[이전 보기 추가](adding-view.md)
> [다음 데이터베이스 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="129b1-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
