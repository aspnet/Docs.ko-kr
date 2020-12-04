---
title: '2부: 모델 추가'
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 2부입니다. 이 섹션에서는 모델 클래스를 추가합니다.
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: b2e840e20d034b42b2dc4a525b1dd76e44bbe3a8
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420060"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="168b5-104">2부. ASP.NET Core에서 Razor Pages 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="168b5-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="168b5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="168b5-106">이 섹션에서는 데이터베이스에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="168b5-107">이 앱의 모델 클래스는 [EF Core(Entity Framework Core)](/ef/core)를 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="168b5-108">EF Core는 데이터 액세스를 간소화하는 O/RM(개체-관계형 매퍼)입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="168b5-109">먼저 모델 클래스를 작성하면 EF Core가 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="168b5-110">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(**P** lain **O** ld **C** LR **O** bject) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="168b5-111">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="168b5-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="168b5-113">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="168b5-115">**솔루션 탐색기** 에서 *RazorPagesMovie* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="168b5-116">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="168b5-117">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="168b5-118">**추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="168b5-119">클래스의 이름을 *동영상* 으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="168b5-120">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-121">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-122">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-123">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-124">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-124">With this attribute:</span></span>

  * <span data-ttu-id="168b5-125">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-126">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="168b5-128">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="168b5-129">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="168b5-130">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-131">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-132">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-133">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-134">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-134">With this attribute:</span></span>

  * <span data-ttu-id="168b5-135">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-136">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="168b5-137">NuGet 패키지 및 EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="168b5-138">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-138">Add a database context class</span></span>

1. <span data-ttu-id="168b5-139">*RazorPagesMovie* 프로젝트에서 *Data* 라는 이름의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="168b5-140">*Data* 폴더에서 다음 코드를 사용하여 *RazorPagesMovieContext.cs* 라는 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="168b5-141">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="168b5-142">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="168b5-143">이후 단계에서 종속성이 추가될 때까지 코드는 컴파일되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="168b5-144">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-144">Add a database connection string</span></span>

<span data-ttu-id="168b5-145">강조 표시된 아래 코드와 같이 *appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="168b5-146">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="168b5-146">Register the database context</span></span>

1. <span data-ttu-id="168b5-147">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="168b5-148">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-149">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="168b5-150">**솔루션 도구 창** 에서 *RazorPagesMovie* 프로젝트를 Ctrl + 클릭한 다음 **추가** > **새 폴더...** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="168b5-151">*Models* 폴더를 Ctrl + 클릭하고 **추가** > **새 파일...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="168b5-152">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="168b5-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="168b5-153">왼쪽 창에서 **일반** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="168b5-154">가운데 창에서 **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="168b5-155">클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="168b5-156">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-157">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-158">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-159">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-160">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-160">With this attribute:</span></span>

  * <span data-ttu-id="168b5-161">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-162">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="168b5-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="168b5-164">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="168b5-165">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="168b5-165">Scaffold the movie model</span></span>

<span data-ttu-id="168b5-166">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="168b5-167">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="168b5-169">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="168b5-170">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="168b5-171">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="168b5-172">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![이전 지침의 이미지입니다.](model/_static/5/sca.png)

1. <span data-ttu-id="168b5-174">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

1. <span data-ttu-id="168b5-176">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="168b5-177">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="168b5-178">**데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="168b5-179">**데이터 컨텍스트 추가** 대화 상자에서 클래스 이름 *RazorPagesMovie.Data.RazorPagesMovieContext* 가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="168b5-180">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-180">Select **Add**.</span></span>

   ![이전 지침의 이미지입니다.](model/_static/3/arp.png)

<span data-ttu-id="168b5-182">*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="168b5-184">*Program.cs*, *Startup.cs*, *.csproj* 파일이 포함된 프로젝트 디렉터리에서 명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="168b5-185">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="168b5-186">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="168b5-187">다음 표에서는 ASP.NET Core 코드 생성기 옵션을 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="168b5-188">옵션</span><span class="sxs-lookup"><span data-stu-id="168b5-188">Option</span></span>               | <span data-ttu-id="168b5-189">Description</span><span class="sxs-lookup"><span data-stu-id="168b5-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="168b5-190">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="168b5-191">사용할 `DbContext` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="168b5-192">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="168b5-193">뷰를 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="168b5-194">Edit 및 Create 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="168b5-195">`-h` 옵션을 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="168b5-196">자세한 내용은 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="168b5-197">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="168b5-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="168b5-198">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="168b5-199">다음 코드는 `Startup`에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="168b5-200">앱이 개발에서 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 `IWebHostEnvironment`가 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-201">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="168b5-202">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="168b5-203">*Pages* 폴더를 Ctrl + 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="168b5-204">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="168b5-205">*Pages/Movies* 폴더를 Ctrl + 클릭하고 **추가** > **새 스캐폴딩...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![이전 지침의 이미지입니다.](model/_static/scaMac.png)

1. <span data-ttu-id="168b5-207">**새 스캐폴딩** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="168b5-209">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="168b5-210">**DbContext Class to use:** 열에서 클래스 이름을 *RazorPagesMovie.Data.RazorPagesMovieContext* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="168b5-211">**마침** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-211">Select **Finish**.</span></span>

   ![이전 지침의 이미지입니다.](model/_static/5/arpMac.png)

<span data-ttu-id="168b5-213">*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="168b5-214">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="168b5-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="168b5-215">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="168b5-216">다음 코드는 `Startup`에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="168b5-217">앱이 개발에서 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 `IWebHostEnvironment`가 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="168b5-218">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="168b5-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-220">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="168b5-221">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="168b5-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="168b5-223">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="168b5-223">Updated</span></span>

* <span data-ttu-id="168b5-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-224">*Startup.cs*</span></span>

<span data-ttu-id="168b5-225">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="168b5-227">스캐폴드 프로세스는 다음 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="168b5-228">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="168b5-229">생성된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-230">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="168b5-231">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="168b5-232">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="168b5-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="168b5-234">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="168b5-234">Updated</span></span>

* <span data-ttu-id="168b5-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-235">*Startup.cs*</span></span>

<span data-ttu-id="168b5-236">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="168b5-237">EF의 마이그레이션 기능을 사용하여 초기 데이터베이스 스키마 만들기</span><span class="sxs-lookup"><span data-stu-id="168b5-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="168b5-238">Entity Framework Core의 마이그레이션 기능은 다음을 수행하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="168b5-239">초기 데이터베이스 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-239">Create the initial database schema.</span></span>
* <span data-ttu-id="168b5-240">데이터베이스 스키마를 증분 방식으로 업데이트하여 애플리케이션의 데이터 모델과 동기화된 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="168b5-241">데이터베이스의 기존 데이터는 그대로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-243">이 섹션에서는 **패키지 관리자 콘솔**(PMC) 창을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="168b5-244">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-244">Add an initial migration.</span></span>
* <span data-ttu-id="168b5-245">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="168b5-246">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 메뉴](model/_static/5/pmc.png)

1. <span data-ttu-id="168b5-248">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168b5-249">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="168b5-250">다음 .NET CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="168b5-251">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="168b5-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="168b5-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="168b5-253">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="168b5-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="168b5-254">경고는 이후 단계에서 처리되므로 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="168b5-255">`migrations` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="168b5-256">이 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="168b5-257">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="168b5-258">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="168b5-259">`update` 명령은 적용되지 않은 마이그레이션에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="168b5-260">이 경우 `update`는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="168b5-262">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="168b5-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="168b5-263">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="168b5-264">EF Core 데이터베이스 컨텍스트와 같은 서비스는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="168b5-265">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="168b5-266">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="168b5-267">스캐폴딩 도구는 자동으로 데이터베이스 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="168b5-268">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="168b5-269">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="168b5-270">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="168b5-271">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="168b5-272">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="168b5-273">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="168b5-274">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="168b5-275">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="168b5-276">연결 문자열 이름은 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="168b5-277">로컬 개발의 경우 [구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168b5-278">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="168b5-279">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="168b5-280">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-280">Test the app</span></span>

1. <span data-ttu-id="168b5-281">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="168b5-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="168b5-282">다음과 같은 오류가 표시되는 경우</span><span class="sxs-lookup"><span data-stu-id="168b5-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="168b5-283">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="168b5-284">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-284">Test the **Create** link.</span></span>

   ![페이지 만들기](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="168b5-286">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="168b5-287">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="168b5-288">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="168b5-289">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="168b5-290">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="168b5-291">추가 자료</span><span class="sxs-lookup"><span data-stu-id="168b5-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="168b5-292">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="168b5-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="168b5-293">이 섹션에서는 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="168b5-294">이 앱의 모델 클래스는 [EF Core(Entity Framework Core)](/ef/core)를 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="168b5-295">EF Core는 데이터 액세스를 간소화하는 O/RM(개체-관계형 매퍼)입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="168b5-296">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="168b5-297">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="168b5-298">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="168b5-299">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-301">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="168b5-302">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-302">Name the folder *Models*.</span></span>

<span data-ttu-id="168b5-303">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="168b5-304">**추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="168b5-305">클래스의 이름을 **동영상** 으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-305">Name the class **Movie**.</span></span>

<span data-ttu-id="168b5-306">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-307">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-308">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-309">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-310">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-310">With this attribute:</span></span>

  * <span data-ttu-id="168b5-311">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-312">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="168b5-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="168b5-315">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="168b5-316">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="168b5-317">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-318">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-319">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-320">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-321">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-321">With this attribute:</span></span>

  * <span data-ttu-id="168b5-322">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-323">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="168b5-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="168b5-325">NuGet 패키지 및 EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="168b5-326">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-326">Add a database context class</span></span>

* <span data-ttu-id="168b5-327">*RazorPagesMovie* 프로젝트에서 *Data* 라는 이름의 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="168b5-328">다음 `RazorPagesMovieContext` 클래스를 *Data* 폴더에 추가:</span><span class="sxs-lookup"><span data-stu-id="168b5-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="168b5-329">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="168b5-330">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="168b5-331">이후 단계에서 종속성이 추가될 때까지 코드는 컴파일되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="168b5-332">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-332">Add a database connection string</span></span>

<span data-ttu-id="168b5-333">강조 표시된 아래 코드와 같이 *appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="168b5-334">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="168b5-334">Register the database context</span></span>

<span data-ttu-id="168b5-335">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="168b5-336">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-337">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="168b5-338">**솔루션 도구 창** 에서 **RazorPagesMovie** 프로젝트를 Ctrl + 클릭한 다음 **추가** > **새 폴더...** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="168b5-339">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="168b5-340">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="168b5-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="168b5-341">왼쪽 창에서 **일반** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="168b5-342">가운데 창에서 **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="168b5-343">클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="168b5-344">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-345">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-346">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-347">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-348">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-348">With this attribute:</span></span>

  * <span data-ttu-id="168b5-349">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-350">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="168b5-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="168b5-352">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="168b5-353">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="168b5-353">Scaffold the movie model</span></span>

<span data-ttu-id="168b5-354">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="168b5-355">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-357">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="168b5-358">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="168b5-359">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="168b5-360">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="168b5-362">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="168b5-364">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="168b5-365">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="168b5-366">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름을 RazorPagesMovie.**Models**.RazorPagesMovieContext에서 RazorPagesMovie.**Data**.RazorPagesMovieContext로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="168b5-367">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="168b5-368">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="168b5-369">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-369">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/3/arp.png)

<span data-ttu-id="168b5-371">*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="168b5-373">*Program.cs*, *Startup.cs*, *.csproj* 파일이 포함된 프로젝트 디렉터리에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="168b5-374">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="168b5-375">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="168b5-376">다음 표에서는 ASP.NET Core 코드 생성기 옵션을 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="168b5-377">옵션</span><span class="sxs-lookup"><span data-stu-id="168b5-377">Option</span></span>               | <span data-ttu-id="168b5-378">Description</span><span class="sxs-lookup"><span data-stu-id="168b5-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="168b5-379">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="168b5-380">사용할 `DbContext` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="168b5-381">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="168b5-382">뷰를 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="168b5-383">Edit 및 Create 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="168b5-384">`-h` 옵션을 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="168b5-385">자세한 내용은 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="168b5-386">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="168b5-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="168b5-387">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="168b5-388">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="168b5-389">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-390">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="168b5-391">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="168b5-392">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="168b5-393">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="168b5-394">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 스캐폴딩...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

<span data-ttu-id="168b5-396">**새 스캐폴딩** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="168b5-398">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="168b5-399">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택하거나 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="168b5-400">**데이터 컨텍스트 클래스** 행에 새 클래스의 이름 RazorPagesMovie.**Data**.RazorPagesMovieContext를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="168b5-401">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="168b5-402">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="168b5-403">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-403">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

<span data-ttu-id="168b5-405">*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="168b5-406">EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-406">Add EF tools</span></span>

<span data-ttu-id="168b5-407">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="168b5-408">위의 명령은 .NET Core CLI에 대한 Entity Framework Core 도구를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="168b5-409">자세한 내용은 [Entity Framework Core 도구 참조 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="168b5-410">개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용</span><span class="sxs-lookup"><span data-stu-id="168b5-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="168b5-411">SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="168b5-412">다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="168b5-413">`IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="168b5-414">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="168b5-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-416">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="168b5-417">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="168b5-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="168b5-419">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="168b5-419">Updated</span></span>

* <span data-ttu-id="168b5-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-420">*Startup.cs*</span></span>

<span data-ttu-id="168b5-421">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-422">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="168b5-423">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="168b5-424">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="168b5-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="168b5-426">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="168b5-426">Updated</span></span>

* <span data-ttu-id="168b5-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-427">*Startup.cs*</span></span>

<span data-ttu-id="168b5-428">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="168b5-430">스캐폴드 프로세스는 다음 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="168b5-431">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="168b5-432">생성된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="168b5-433">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="168b5-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-435">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="168b5-436">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-436">Add an initial migration.</span></span>
* <span data-ttu-id="168b5-437">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="168b5-438">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="168b5-440">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168b5-441">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="168b5-442">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="168b5-443">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="168b5-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="168b5-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="168b5-445">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="168b5-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="168b5-446">경고는 이후 단계에서 처리되므로 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="168b5-447">migrations 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="168b5-448">이 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="168b5-449">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="168b5-450">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="168b5-451">`update` 명령은 적용되지 않은 마이그레이션에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="168b5-452">이 경우 `update`는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="168b5-454">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="168b5-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="168b5-455">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="168b5-456">EF Core 데이터베이스 컨텍스트와 같은 서비스는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="168b5-457">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="168b5-458">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="168b5-459">스캐폴딩 도구는 자동으로 데이터베이스 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="168b5-460">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="168b5-461">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="168b5-462">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="168b5-463">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="168b5-464">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="168b5-465">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="168b5-466">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="168b5-467">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="168b5-468">연결 문자열 이름은 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="168b5-469">로컬 개발의 경우 [구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168b5-470">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="168b5-471">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="168b5-472">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-472">Test the app</span></span>

* <span data-ttu-id="168b5-473">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="168b5-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="168b5-474">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="168b5-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="168b5-475">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="168b5-476">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-476">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="168b5-478">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="168b5-479">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="168b5-480">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="168b5-481">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="168b5-482">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="168b5-483">추가 자료</span><span class="sxs-lookup"><span data-stu-id="168b5-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="168b5-484">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="168b5-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="168b5-485">이 섹션에서는 플랫폼 간 [SQLite 데이터베이스](https://www.sqlite.org/index.html)에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="168b5-486">ASP.NET Core 템플릿으로 만든 앱이 SQLite 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="168b5-487">앱의 모델 클래스를 [EF Core(Entity Framework Core)](/ef/core)([SQLite EF Core 데이터베이스 공급자](/ef/core/providers/sqlite))와 함께 사용하여 데이터베이스를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="168b5-488">EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="168b5-489">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="168b5-490">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="168b5-491">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="168b5-492">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-494">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="168b5-495">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-495">Name the folder *Models*.</span></span>

<span data-ttu-id="168b5-496">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="168b5-497">**추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="168b5-498">클래스의 이름을 **동영상** 으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-498">Name the class **Movie**.</span></span>

<span data-ttu-id="168b5-499">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-500">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-501">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-502">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-503">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-503">With this attribute:</span></span>

  * <span data-ttu-id="168b5-504">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-505">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="168b5-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="168b5-508">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="168b5-509">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="168b5-510">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-511">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-512">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-513">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-514">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-514">With this attribute:</span></span>

  * <span data-ttu-id="168b5-515">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-516">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="168b5-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="168b5-518">NuGet 패키지 및 EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="168b5-519">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-519">Add a database context class</span></span>

<span data-ttu-id="168b5-520">RazorPagesMovie 프로젝트에서 *Data* 라는 이름의 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="168b5-521">다음 `RazorPagesMovieContext` 클래스를 *Data* 폴더에 추가:</span><span class="sxs-lookup"><span data-stu-id="168b5-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="168b5-522">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="168b5-523">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="168b5-524">이후 단계에서 종속성이 추가될 때까지 코드는 컴파일되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="168b5-525">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-525">Add a database connection string</span></span>

<span data-ttu-id="168b5-526">강조 표시된 아래 코드와 같이 *appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="168b5-527">필요한 NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="168b5-527">Add required NuGet packages</span></span>

<span data-ttu-id="168b5-528">다음 .NET Core CLI 명령을 실행하여 SQLite 및 CodeGeneration.Design을 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="168b5-529">스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="168b5-530">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="168b5-530">Register the database context</span></span>

<span data-ttu-id="168b5-531">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="168b5-532">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="168b5-533">오류에 대한 검사 방법으로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-534">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="168b5-535">**솔루션 도구 창** 에서 *RazorPagesMovie* 프로젝트를 Ctrl + 클릭한 다음 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="168b5-536">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="168b5-537">*Models* 폴더를 Ctrl + 클릭하고 **추가** > **새 파일** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="168b5-538">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="168b5-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="168b5-539">왼쪽 창에서 **일반** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="168b5-540">가운데 창에서 **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="168b5-541">클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="168b5-542">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="168b5-543">`Movie` 클래스에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="168b5-544">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="168b5-545">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="168b5-546">이 특성을 사용하면:</span><span class="sxs-lookup"><span data-stu-id="168b5-546">With this attribute:</span></span>

  * <span data-ttu-id="168b5-547">사용자가 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="168b5-548">시간 정보 없이 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="168b5-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="168b5-550">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="168b5-551">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="168b5-551">Scaffold the movie model</span></span>

<span data-ttu-id="168b5-552">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="168b5-553">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-555">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="168b5-556">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="168b5-557">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="168b5-558">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="168b5-560">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="168b5-562">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="168b5-563">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="168b5-564">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름인 **RazorPagesMovie.Models.RazorPagesMovieContext** 를 수용합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="168b5-565">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-565">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arp.png)

<span data-ttu-id="168b5-567">*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="168b5-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="168b5-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="168b5-569">*Program.cs*, *Startup.cs*, *.csproj* 파일이 포함된 프로젝트 디렉터리에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="168b5-570">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="168b5-571">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="168b5-572">다음 표에서는 ASP.NET Core 코드 생성기 옵션을 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="168b5-573">옵션</span><span class="sxs-lookup"><span data-stu-id="168b5-573">Option</span></span>               | <span data-ttu-id="168b5-574">Description</span><span class="sxs-lookup"><span data-stu-id="168b5-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="168b5-575">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="168b5-576">사용할 `DbContext` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="168b5-577">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="168b5-578">뷰를 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="168b5-579">Edit 및 Create 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="168b5-580">`-h` 옵션을 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="168b5-581">자세한 내용은 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="168b5-582">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="168b5-583">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="168b5-584">*Pages* 폴더를 Ctrl + 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="168b5-585">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="168b5-586">*Pages/Movies* 폴더를 Ctrl + 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

<span data-ttu-id="168b5-588">**새 스캐폴딩 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="168b5-590">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="168b5-591">**모델 클래스** 드롭다운에서 **동영상** 을 선택하거나 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="168b5-592">**데이터 컨텍스트 클래스** 행에 **RazorPagesMovieContext** 를 입력하거나 선택합니다. 그러면 올바른 네임스페이스의 새 데이터베이스 컨텍스트 클래스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="168b5-593">이 경우 클래스는 **RazorPagesMovie.Models.RazorPagesMovieContext** 입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="168b5-594">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-594">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

<span data-ttu-id="168b5-596">*appsettings.json* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="168b5-597">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="168b5-598">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="168b5-598">Files created</span></span>

* <span data-ttu-id="168b5-599">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 Index입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="168b5-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="168b5-601">파일 업데이트됨</span><span class="sxs-lookup"><span data-stu-id="168b5-601">File updated</span></span>

* <span data-ttu-id="168b5-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="168b5-602">*Startup.cs*</span></span>

<span data-ttu-id="168b5-603">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="168b5-604">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="168b5-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="168b5-606">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="168b5-607">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-607">Add an initial migration.</span></span>
* <span data-ttu-id="168b5-608">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="168b5-609">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="168b5-611">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="168b5-612">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="168b5-613">스키마는 *RazorPagesMovieContext.cs* 파일에서 `DbContext`에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="168b5-614">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="168b5-615">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="168b5-616">자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="168b5-617">`Update-Database` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="168b5-618">`Up` 메서드는 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168b5-619">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="168b5-620">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="168b5-621">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="168b5-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="168b5-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="168b5-623">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="168b5-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="168b5-624">경고는 이후 단계에서 처리되므로 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="168b5-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="168b5-626">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="168b5-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="168b5-627">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="168b5-628">EF Core 데이터베이스 컨텍스트와 같은 서비스는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="168b5-629">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="168b5-630">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="168b5-631">스캐폴딩 도구는 자동으로 데이터베이스 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="168b5-632">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="168b5-633">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="168b5-634">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="168b5-635">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="168b5-636">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="168b5-637">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="168b5-638">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="168b5-639">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="168b5-640">연결 문자열 이름은 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="168b5-641">로컬 개발의 경우 [구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="168b5-642">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="168b5-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="168b5-643">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="168b5-644">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-644">Test the app</span></span>

* <span data-ttu-id="168b5-645">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="168b5-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="168b5-646">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="168b5-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="168b5-647">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="168b5-648">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-648">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="168b5-650">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="168b5-651">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="168b5-652">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="168b5-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="168b5-653">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="168b5-654">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="168b5-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="168b5-655">추가 자료</span><span class="sxs-lookup"><span data-stu-id="168b5-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="168b5-656">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="168b5-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
