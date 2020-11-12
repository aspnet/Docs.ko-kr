---
title: 4부. ASP.NET Core MVC 앱에 모델 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422724"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="66891-103">4부. ASP.NET Core MVC 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="66891-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="66891-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="66891-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="66891-105">이 섹션에서는 데이터베이스에서 영화를 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="66891-106">이러한 클래스는 **M** VC 앱의 " **M** odel" 부분이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="66891-107">이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="66891-108">EF Core는 작성해야 할 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="66891-109">직접 만들게 될 모델 클래스는 EF Core에 대한 어떠한 종속성도 없으므로 POCO( **P** lain **O** ld **C** LR **O** bject) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="66891-110">이 클래스는 데이터베이스에 저장될 데이터의 속성만 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="66891-111">이 자습서에서는 먼저 모델 클래스를 작성하고 EF Core가 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="66891-112">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="66891-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-114">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="66891-115">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66891-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66891-117">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66891-119">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="66891-120">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="66891-121">다음 코드로 *Movie.cs* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="66891-122">`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="66891-123">`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="66891-124">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="66891-124">With this attribute:</span></span>

* <span data-ttu-id="66891-125">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="66891-126">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="66891-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="66891-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="66891-128">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="66891-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-130">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="66891-132">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="66891-133">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="66891-134">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="66891-135">추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66891-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-137">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66891-138">**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="66891-139">오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="66891-140">일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="66891-142">`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="66891-143">**확인** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-143">Press the **Ok** button.</span></span>

<span data-ttu-id="66891-144">**라이선스 승인** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="66891-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="66891-145">필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="66891-146">위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="66891-147">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="66891-147">Create a database context class</span></span>

<span data-ttu-id="66891-148">`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="66891-149">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="66891-150">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-150">Create a *Data* folder.</span></span>

<span data-ttu-id="66891-151">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="66891-152">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="66891-153">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="66891-154">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="66891-155">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="66891-155">Register the database context</span></span>

<span data-ttu-id="66891-156">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="66891-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="66891-157">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="66891-158">이러한 서비스(예: :::no-loc(Razor)::: Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-158">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="66891-159">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="66891-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="66891-160">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="66891-161">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="66891-162">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-164">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="66891-165">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="66891-166">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="66891-167">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="66891-167">Add a database connection string</span></span>

<span data-ttu-id="66891-168">*:::no-loc(appsettings.json):::* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-168">Add a connection string to the *:::no-loc(appsettings.json):::* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-170">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="66891-171">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="66891-172">영화 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="66891-172">Scaffold movie pages</span></span>

<span data-ttu-id="66891-173">스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-175">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="66891-177">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="66891-179">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="66891-180">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="66891-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="66891-181">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="66891-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc5.png)

* <span data-ttu-id="66891-183">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="66891-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="66891-184">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="66891-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="66891-185">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-185">Select **Add**</span></span>

<span data-ttu-id="66891-186">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="66891-186">Visual Studio creates:</span></span>

* <span data-ttu-id="66891-187">영화 컨트롤러( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="66891-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="66891-188">Create, Delete, Details, Edit, 및 Index 페이지에 대한 :::no-loc(Razor)::: 뷰 파일( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="66891-188">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="66891-189">이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="66891-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="66891-191">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="66891-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="66891-192">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="66891-193">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-194">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66891-195">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="66891-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="66891-196">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="66891-197">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="66891-198">앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="66891-199">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="66891-199">Initial migration</span></span>

<span data-ttu-id="66891-200">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="66891-201">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-203">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="66891-204">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="66891-205">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="66891-206">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="66891-207">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="66891-208">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="66891-209">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="66891-210">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="66891-211">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="66891-212">데이터베이스 수정 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="66891-213">No type was specified for the decimal column ‘Price’ on entity type ‘Movie’.</span><span class="sxs-lookup"><span data-stu-id="66891-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="66891-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="66891-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="66891-215">Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="66891-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="66891-216">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-217">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="66891-218">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="66891-219">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="66891-220">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="66891-221">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="66891-222">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="66891-223">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="66891-224">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="66891-225">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="66891-226">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="66891-226">The InitialCreate class</span></span>

<span data-ttu-id="66891-227">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="66891-228">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="66891-229">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="66891-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="66891-230">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-230">Test the app</span></span>

* <span data-ttu-id="66891-231">앱을 실행하고 **Movie App** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="66891-232">다음 중 하나와 비슷한 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="66891-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-234">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="66891-235">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="66891-236">**Create** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-236">Test the **Create** page.</span></span> <span data-ttu-id="66891-237">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="66891-238">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="66891-239">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="66891-240">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="66891-241">**Edit** , **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="66891-242">컨트롤러에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="66891-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-244">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="66891-245">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="66891-246">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-247">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="66891-248">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="66891-249">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="66891-250">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="66891-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="66891-251">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="66891-252">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="66891-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="66891-253">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="66891-254">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="66891-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="66891-255">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="66891-256">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="66891-257">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="66891-258">강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="66891-259">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="66891-260">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="66891-261">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="66891-262">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="66891-263">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="66891-264">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="66891-265">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="66891-266">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="66891-267">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="66891-268">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="66891-269">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="66891-270">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="66891-271">보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="66891-272">영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="66891-273">`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="66891-274">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="66891-275">예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="66891-276">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="66891-277">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="66891-278">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="66891-279">이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="66891-280">영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="66891-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="66891-281">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="66891-282">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="66891-283">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="66891-284">즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66891-285">추가 자료</span><span class="sxs-lookup"><span data-stu-id="66891-285">Additional resources</span></span>

* [<span data-ttu-id="66891-286">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="66891-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="66891-287">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="66891-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="66891-288">[이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="66891-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="66891-289">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="66891-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-291">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="66891-292">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66891-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66891-294">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-295">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66891-296">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 클래스** > **빈 클래스** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="66891-297">파일 이름을 *Movie.cs* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="66891-298">다음 코드로 *Movie.cs* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="66891-299">`Movie` 클래스에는 데이터베이스에서 기본 키에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="66891-300">`ReleaseDate`의 <xref:System.ComponentModel.DataAnnotations.DataType> 특성은 날짜 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="66891-301">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="66891-301">With this attribute:</span></span>

* <span data-ttu-id="66891-302">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="66891-303">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="66891-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="66891-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="66891-305">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="66891-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-307">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="66891-309">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="66891-310">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="66891-311">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="66891-312">추가적인 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66891-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-314">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66891-315">**프로젝트** 메뉴에서 **NuGet 패키지 관리** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="66891-316">오른쪽 위의 **검색** 필드에 `Microsoft.EntityFrameworkCore.SQLite`를 입력하고 **반환** 키를 눌러 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="66891-317">일치하는 NuGet 패키지를 선택하고 **패키지 추가** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet 패키지 추가](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="66891-319">`MvcMovie` 프로젝트가 선택된 **프로젝트 선택** 대화 상자가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="66891-320">**확인** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-320">Press the **Ok** button.</span></span>

<span data-ttu-id="66891-321">**라이선스 승인** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="66891-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="66891-322">필요에 따라 라이선스를 검토한 다음 **수락** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="66891-323">위의 단계를 반복하여 다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="66891-324">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="66891-324">Create a database context class</span></span>

<span data-ttu-id="66891-325">`Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="66891-326">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="66891-327">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-327">Create a *Data* folder.</span></span>

<span data-ttu-id="66891-328">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="66891-329">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="66891-330">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="66891-331">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="66891-332">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="66891-332">Register the database context</span></span>

<span data-ttu-id="66891-333">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="66891-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="66891-334">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="66891-335">이러한 서비스(예: :::no-loc(Razor)::: Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-335">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="66891-336">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="66891-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="66891-337">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="66891-338">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="66891-339">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-341">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="66891-342">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="66891-343">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="66891-344">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="66891-344">Add a database connection string</span></span>

<span data-ttu-id="66891-345">*:::no-loc(appsettings.json):::* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-345">Add a connection string to the *:::no-loc(appsettings.json):::* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-347">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="66891-348">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="66891-349">영화 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="66891-349">Scaffold movie pages</span></span>

<span data-ttu-id="66891-350">스캐폴딩 도구를 사용하여 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-352">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="66891-354">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="66891-356">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="66891-357">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="66891-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="66891-358">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="66891-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc3.png)

* <span data-ttu-id="66891-360">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="66891-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="66891-361">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="66891-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="66891-362">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-362">Select **Add**</span></span>

<span data-ttu-id="66891-363">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="66891-363">Visual Studio creates:</span></span>

* <span data-ttu-id="66891-364">영화 컨트롤러( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="66891-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="66891-365">Create, Delete, Details, Edit, 및 Index 페이지에 대한 :::no-loc(Razor)::: 뷰 파일( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="66891-365">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="66891-366">이러한 파일의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="66891-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="66891-368">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="66891-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="66891-369">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="66891-370">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-371">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66891-372">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="66891-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="66891-373">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="66891-374">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="66891-375">앱을 실행하고 **Movie App** 링크를 클릭하면 *Cannot open database* 또는 *no such table: Movie 오류 메시지가 표시됩니다.* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="66891-376">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="66891-376">Initial migration</span></span>

<span data-ttu-id="66891-377">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="66891-378">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 수정할 수 있는 도구 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-380">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="66891-381">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="66891-382">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="66891-383">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="66891-384">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="66891-385">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="66891-386">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="66891-387">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="66891-388">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="66891-389">데이터베이스 수정 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="66891-390">No type was specified for the decimal column ‘Price’ on entity type ‘Movie’.</span><span class="sxs-lookup"><span data-stu-id="66891-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="66891-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="66891-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="66891-392">Explicitly specify the SQL server column type that can accommodate all the values using ‘HasColumnType()’.</span><span class="sxs-lookup"><span data-stu-id="66891-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="66891-393">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-394">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="66891-395">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="66891-396">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="66891-397">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="66891-398">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="66891-399">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="66891-400">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="66891-401">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="66891-402">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="66891-403">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="66891-403">The InitialCreate class</span></span>

<span data-ttu-id="66891-404">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="66891-405">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="66891-406">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="66891-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="66891-407">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-407">Test the app</span></span>

* <span data-ttu-id="66891-408">앱을 실행하고 **Movie App** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="66891-409">다음 중 하나와 비슷한 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="66891-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-411">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="66891-412">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="66891-413">**Create** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-413">Test the **Create** page.</span></span> <span data-ttu-id="66891-414">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="66891-415">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="66891-416">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="66891-417">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="66891-418">**Edit** , **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="66891-419">컨트롤러에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="66891-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-421">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="66891-422">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="66891-423">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-424">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="66891-425">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="66891-426">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="66891-427">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="66891-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="66891-428">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="66891-429">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="66891-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="66891-430">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="66891-431">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="66891-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="66891-432">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="66891-433">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="66891-434">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="66891-435">강력한 형식의 방법을 사용하면 컴파일 시에 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="66891-436">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 보기에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="66891-437">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="66891-438">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="66891-439">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="66891-440">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="66891-441">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="66891-442">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="66891-443">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="66891-444">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="66891-445">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="66891-446">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="66891-447">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="66891-448">보기 파일 맨 위에 있는 `@model` 문은 보기에 필요한 개체 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="66891-449">영화 컨트롤러가 만들어질 때 다음 `@model` 문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="66891-450">`@model` 지시문을 사용하면 컨트롤러가 보기에 전달한 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="66891-451">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="66891-452">예를 들어 *Details.cshtml* 보기의 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="66891-453">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="66891-454">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="66891-455">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="66891-456">이 코드는 `Index` 작업 메서드에서 보기로 `Movies` 목록을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="66891-457">영화 컨트롤러가 만들어질 때 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="66891-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="66891-458">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="66891-459">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="66891-460">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="66891-461">즉, 여러 가지 이점 중에서도 코드의 컴파일 시 검사를 수행할 수 있다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66891-462">추가 자료</span><span class="sxs-lookup"><span data-stu-id="66891-462">Additional resources</span></span>

* [<span data-ttu-id="66891-463">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="66891-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="66891-464">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="66891-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="66891-465">[이전 보기 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="66891-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="66891-466">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="66891-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-468">*Models* 폴더> **추가** > **클래스** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="66891-469">클래스의 이름을 **Movie** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-470">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="66891-471">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="66891-472">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="66891-472">Scaffold the movie model</span></span>

<span data-ttu-id="66891-473">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="66891-474">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-476">**솔루션 탐색기** 에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위 단계의 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="66891-478">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="66891-480">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="66891-481">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="66891-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="66891-482">**데이터 컨텍스트 클래스:** **+** 아이콘을 선택하고 기본값 **MvcMovie.Models.MvcMovieContext** 를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc.png)

* <span data-ttu-id="66891-484">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="66891-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="66891-485">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="66891-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="66891-486">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-486">Select **Add**</span></span>

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

<span data-ttu-id="66891-488">Visual Studio가 다음을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="66891-488">Visual Studio creates:</span></span>

* <span data-ttu-id="66891-489">Entity Framework Core [데이터베이스 컨텍스트 클래스](xref:data/ef-mvc/intro#create-the-database-context)( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="66891-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="66891-490">영화 컨트롤러( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="66891-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="66891-491">Create, Delete, Details, Edit, 및 Index 페이지에 대한 :::no-loc(Razor)::: 뷰 파일( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="66891-491">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="66891-492">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(생성, 읽기, 수정 및 삭제) 작업 메서드와 보기 및 데이터베이스 컨텍스트의 자동 생성을 *스캐폴딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66891-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66891-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="66891-494">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="66891-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="66891-495">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="66891-496">Linux에서는 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="66891-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="66891-497">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66891-498">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66891-499">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="66891-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="66891-500">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="66891-501">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="66891-502">응용 프로그램을 실행하고 **Mvc Movie** 링크를 클릭하면 다음과 유사한 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPool:::no-loc(Identity)::: identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-504">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="66891-505">데이터베이스를 만들어야 하는데 이를 수행하기 위해 EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="66891-506">마이그레이션을 통해 데이터 모델과 일치하는 데이터베이스를 만들고 데이터 모델 변경 시 데이터베이스 스키마를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="66891-507">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="66891-507">Initial migration</span></span>

<span data-ttu-id="66891-508">이 섹션에서는 다음 작업을 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="66891-509">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-509">Add an initial migration.</span></span>
* <span data-ttu-id="66891-510">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="66891-512">**도구** 메뉴에서 **NuGet 패키지 관리자** > **PMC(패키지 관리자 콘솔)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="66891-514">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="66891-515">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="66891-516">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="66891-517">`Initial` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="66891-518">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="66891-519">자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="66891-520">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-521">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="66891-522">`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="66891-523">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일의 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="66891-524">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="66891-525">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="66891-526">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="66891-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="66891-527">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="66891-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="66891-528">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="66891-529">이러한 서비스(예: :::no-loc(Razor)::: Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-529">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="66891-530">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="66891-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66891-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66891-532">스캐폴딩 도구는 자동으로 DB 컨텍스트를 만들고 DI 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="66891-533">다음 `Startup.ConfigureServices` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="66891-534">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="66891-535">`MvcMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="66891-536">데이터 컨텍스트(`MvcMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="66891-537">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="66891-538">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66891-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="66891-539">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="66891-540">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="66891-541">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="66891-542">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="66891-543">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66891-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="66891-544">DB 컨텍스트를 만들고 DI 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="66891-545">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-545">Test the app</span></span>

* <span data-ttu-id="66891-546">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="66891-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="66891-547">다음과 비슷한 데이터베이스 예외가 발생할 경우:</span><span class="sxs-lookup"><span data-stu-id="66891-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="66891-548">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="66891-549">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-549">Test the **Create** link.</span></span> <span data-ttu-id="66891-550">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="66891-551">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="66891-552">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="66891-553">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="66891-554">**Edit** , **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="66891-555">`Startup` 클래스를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="66891-556">앞에서 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="66891-557">`services.AddDbContext<MvcMovieContext>(options =>`는 사용할 데이터베이스 및 연결 문자열을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="66891-558">`=>`는 [람다 연산자](/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="66891-559">*Controllers/MoviesController.cs* 파일을 열고 생성자를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="66891-560">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="66891-561">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="66891-562">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="66891-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="66891-563">이 자습서의 앞부분에서는 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 보기에 전달하는 방법을 살펴봤습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="66891-564">`ViewData` 사전은 정보를 보기에 전달하기 위한 편리한 런타임 바인딩 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="66891-565">또한 MVC는 보기에 강력한 형식의 모델 개체를 전달하는 기능도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="66891-566">이렇게 강력하게 형식화된 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="66891-567">스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="66891-568">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="66891-569">일반적으로 `id` 매개 변수는 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="66891-570">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="66891-571">컨트롤러를 `movies` 컨트롤러로 설정(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="66891-572">작업을 `details`로 설정(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="66891-573">ID를 1로 설정(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="66891-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="66891-574">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="66891-575">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="66891-576">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="66891-577">영화를 찾으면 `Movie` 모델의 인스턴스를 `Details` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="66891-578">*Views/Movies/Details.cshtml* 파일의 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="66891-579">보기 파일 맨 위에 있는 `@model` 문을 포함하여 보기에서 필요로 하는 개체 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="66891-580">영화 컨트롤러를 만들 때 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문이 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66891-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="66891-581">이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="66891-582">예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="66891-583">또한 `Create` 및 `Edit` 메서드와 보기도 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="66891-584">*Index.cshtml* 보기 및 영화 컨트롤러의 `Index` 메서드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="66891-585">코드가 `View` 메서드를 호출할 때 `List` 개체를 생성하는 방법에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="66891-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="66891-586">이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="66891-587">영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="66891-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="66891-588">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 보기에 전달한 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="66891-589">예를 들어 *Index.cshtml* 보기에서 코드는 강력한 형식의 `Model` 개체에 대해 `foreach` 문을 사용하여 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="66891-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="66891-590">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="66891-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="66891-591">즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66891-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66891-592">추가 자료</span><span class="sxs-lookup"><span data-stu-id="66891-592">Additional resources</span></span>

* [<span data-ttu-id="66891-593">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="66891-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="66891-594">세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="66891-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="66891-595">[이전 보기 추가](adding-view.md)
> [다음 데이터베이스 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="66891-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
