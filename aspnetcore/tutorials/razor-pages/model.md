---
title: '2부. ASP.NET Core에서 :::no-loc(Razor)::: Pages 앱에 모델 추가'
author: rick-anderson
description: ':::no-loc(Razor)::: Pages에 대한 자습서 시리즈의 2부입니다.'
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050929"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="bd855-103">2부. ASP.NET Core에서 :::no-loc(Razor)::: Pages 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bd855-103">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="bd855-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd855-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="bd855-105">이 섹션에서는 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="bd855-106">이 앱의 모델 클래스는 [EF Core(Entity Framework Core)](/ef/core)를 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="bd855-107">EF Core는 데이터 액세스를 간소화하는 O/RM(개체-관계형 매퍼)입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="bd855-108">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="bd855-109">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="bd855-110">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bd855-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-112">**:::no-loc(Razor):::PagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-112">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="bd855-113">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-113">Name the folder *Models*.</span></span>

<span data-ttu-id="bd855-114">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-114">Right click the *Models* folder.</span></span> <span data-ttu-id="bd855-115">**추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="bd855-116">클래스의 이름을 **동영상** 으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bd855-118">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="bd855-119">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-120">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bd855-121">Solution Pad에서 **:::no-loc(Razor):::PagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더...** 를 선택합니다. 폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-121">In Solution Pad, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="bd855-122">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="bd855-123">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="bd855-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="bd855-124">왼쪽 창에서 **일반** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="bd855-125">가운데 창에서 **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="bd855-126">클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="bd855-127">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="bd855-128">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="bd855-128">Scaffold the movie model</span></span>

<span data-ttu-id="bd855-129">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="bd855-130">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-132">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bd855-133">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bd855-134">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-134">Name the folder *Movies*</span></span>

<span data-ttu-id="bd855-135">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="bd855-137">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 :::no-loc(Razor)::: Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-137">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="bd855-139">**Entity Framework(CRUD)를 사용하여 :::no-loc(Razor)::: Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-139">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bd855-140">**모델 클래스** 드롭다운에서 **동영상(:::no-loc(Razor):::PagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-140">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="bd855-141">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름을 :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext에서 :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="bd855-142">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="bd855-143">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="bd855-144">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-144">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/3/arp.png)

<span data-ttu-id="bd855-146">*:::no-loc(appsettings.json):::* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-146">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="bd855-148">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-148">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="bd855-149">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="bd855-150">**Windows의 경우** : 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-150">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="bd855-151">**macOS 및 Linux의 경우** : 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-151">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-152">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd855-153">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bd855-154">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bd855-155">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-155">Name the folder *Movies*</span></span>

<span data-ttu-id="bd855-156">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 스캐폴딩...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

<span data-ttu-id="bd855-158">**새 스캐폴딩** 대화 상자에서 **Entity Framework를 사용한 :::no-loc(Razor)::: Pages(CRUD)** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-158">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="bd855-160">**Entity Framework(CRUD)를 사용하여 :::no-loc(Razor)::: Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-160">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bd855-161">**모델 클래스** 드롭다운에서 **동영상(:::no-loc(Razor):::PagesMovie.Models)** 을 선택하거나 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-161">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="bd855-162">**데이터 컨텍스트 클래스** 행에 새 클래스의 이름 :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-162">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="bd855-163">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="bd855-164">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="bd855-165">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-165">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

<span data-ttu-id="bd855-167">*:::no-loc(appsettings.json):::* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-167">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="bd855-168">EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="bd855-168">Add EF tools</span></span>

<span data-ttu-id="bd855-169">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="bd855-170">위의 명령은 .NET Core CLI에 대한 Entity Framework Core 도구를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="bd855-171">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="bd855-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-173">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="bd855-174">*Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-174">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bd855-175">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bd855-175">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="bd855-176">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="bd855-176">Updated</span></span>

* <span data-ttu-id="bd855-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bd855-177">*Startup.cs*</span></span>

<span data-ttu-id="bd855-178">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-179">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd855-180">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="bd855-181">*Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-181">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bd855-182">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bd855-182">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="bd855-183">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="bd855-183">Updated</span></span>

* <span data-ttu-id="bd855-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bd855-184">*Startup.cs*</span></span>

<span data-ttu-id="bd855-185">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd855-187">스캐폴드 프로세스는 다음 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="bd855-188">*Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-188">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="bd855-189">생성된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="bd855-190">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="bd855-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-192">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="bd855-193">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-193">Add an initial migration.</span></span>
* <span data-ttu-id="bd855-194">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="bd855-195">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="bd855-197">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-199">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="bd855-200">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="bd855-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="bd855-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="bd855-202">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="bd855-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="bd855-203">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="bd855-204">migrations 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="bd855-205">이 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="bd855-206">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="bd855-207">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="bd855-208">`update` 명령은 적용되지 않은 마이그레이션에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="bd855-209">이 경우 `update`는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="bd855-211">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="bd855-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="bd855-212">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bd855-213">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bd855-214">이러한 서비스(예: :::no-loc(Razor)::: Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-214">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="bd855-215">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bd855-216">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="bd855-217">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bd855-218">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="bd855-219">`:::no-loc(Razor):::PagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-219">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="bd855-220">데이터 컨텍스트(`:::no-loc(Razor):::PagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-220">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="bd855-221">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="bd855-222">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="bd855-223">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="bd855-224">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bd855-225">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="bd855-226">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd855-228">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-229">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd855-230">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="bd855-231">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-231">Test the app</span></span>

* <span data-ttu-id="bd855-232">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="bd855-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="bd855-233">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="bd855-233">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="bd855-234">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="bd855-235">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-235">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="bd855-237">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bd855-238">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="bd855-239">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd855-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="bd855-240">**Edit** , **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-240">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="bd855-241">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd855-242">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bd855-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bd855-243">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="bd855-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd855-244">이 섹션에서는 플랫폼 간 [SQLite 데이터베이스](https://www.sqlite.org/index.html)에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="bd855-245">ASP.NET Core 템플릿으로 만든 앱이 SQLite 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="bd855-246">앱의 모델 클래스를 [EF Core(Entity Framework Core)](/ef/core)([SQLite EF Core 데이터베이스 공급자](/ef/core/providers/sqlite))와 함께 사용하여 데이터베이스를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="bd855-247">EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="bd855-248">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="bd855-249">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="bd855-250">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="bd855-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-252">**:::no-loc(Razor):::PagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-252">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="bd855-253">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-253">Name the folder *Models*.</span></span>

<span data-ttu-id="bd855-254">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-254">Right click the *Models* folder.</span></span> <span data-ttu-id="bd855-255">**추가** > **클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="bd855-256">클래스의 이름을 **동영상** 으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bd855-258">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="bd855-259">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-260">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bd855-261">솔루션 탐색기에서 **:::no-loc(Razor):::PagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-261">In Solution Explorer, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="bd855-262">폴더 이름을 *Models* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="bd855-263">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="bd855-264">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="bd855-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="bd855-265">왼쪽 창에서 **일반** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="bd855-266">가운데 창에서 **빈 클래스** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="bd855-267">클래스 이름을 **Movie** 로 지정하고 **새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="bd855-268">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="bd855-269">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="bd855-269">Scaffold the movie model</span></span>

<span data-ttu-id="bd855-270">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="bd855-271">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-273">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bd855-274">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bd855-275">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-275">Name the folder *Movies*</span></span>

<span data-ttu-id="bd855-276">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="bd855-278">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 :::no-loc(Razor)::: Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-278">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="bd855-280">**Entity Framework(CRUD)를 사용하여 :::no-loc(Razor)::: Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-280">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="bd855-281">**모델 클래스** 드롭다운에서 **동영상(:::no-loc(Razor):::PagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-281">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="bd855-282">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름인 **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext** 를 수용합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="bd855-283">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-283">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arp.png)

<span data-ttu-id="bd855-285">*:::no-loc(appsettings.json):::* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-285">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="bd855-287">프로젝트 디렉터리( *Program.cs* , *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-287">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="bd855-288">**Windows의 경우** : 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-288">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="bd855-289">**macOS 및 Linux의 경우** : 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-289">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-290">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd855-291">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="bd855-292">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="bd855-293">폴더 이름을 *Movies* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-293">Name the folder *Movies*</span></span>

<span data-ttu-id="bd855-294">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

<span data-ttu-id="bd855-296">**새 스캐폴딩 추가** 대화 상자에서 **Entity Framework를 사용한 :::no-loc(Razor)::: Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-296">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="bd855-298">**Entity Framework(CRUD)를 사용하여 :::no-loc(Razor)::: Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-298">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bd855-299">**모델 클래스** 드롭다운에서 **동영상** 을 선택하거나 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="bd855-300">**데이터 컨텍스트 클래스** 행에 **:::no-loc(Razor):::PagesMovieContext** 를 입력하거나 선택합니다. 그러면 올바른 네임스페이스의 새 db 컨텍스트 클래스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-300">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="bd855-301">이 경우 클래스는 **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext** 입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-301">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="bd855-302">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-302">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

<span data-ttu-id="bd855-304">*:::no-loc(appsettings.json):::* 파일이 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-304">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="bd855-305">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="bd855-306">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="bd855-306">Files created</span></span>

* <span data-ttu-id="bd855-307">*Pages/Movies* : 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-307">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="bd855-308">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="bd855-308">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="bd855-309">파일 업데이트됨</span><span class="sxs-lookup"><span data-stu-id="bd855-309">File updated</span></span>

* <span data-ttu-id="bd855-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bd855-310">*Startup.cs*</span></span>

<span data-ttu-id="bd855-311">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="bd855-312">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="bd855-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd855-314">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="bd855-315">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-315">Add an initial migration.</span></span>
* <span data-ttu-id="bd855-316">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="bd855-317">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="bd855-319">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="bd855-320">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="bd855-321">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다( *:::no-loc(Razor):::PagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="bd855-321">The schema is based on the model specified in the `DbContext` (In the *:::no-loc(Razor):::PagesMovieContext.cs* file).</span></span> <span data-ttu-id="bd855-322">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="bd855-323">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="bd855-324">자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd855-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="bd855-325">`Update-Database` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="bd855-326">`Up` 메서드는 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-328">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="bd855-329">이전 명령은 다음 경고를 생성합니다. " *엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다. This will cause values to be silently truncated if they do not fit in the default precision and scale. 'HasColumnType()'을 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다.* " 이 경고는 무시할 수 있으며 이후 자습서에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-329">The preceding commands generate the following warning: " *No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd855-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="bd855-331">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="bd855-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="bd855-332">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bd855-333">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bd855-334">이러한 서비스(예: :::no-loc(Razor)::: Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-334">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="bd855-335">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bd855-336">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="bd855-337">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bd855-338">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="bd855-339">`:::no-loc(Razor):::PagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-339">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="bd855-340">데이터 컨텍스트(`:::no-loc(Razor):::PagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-340">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="bd855-341">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="bd855-342">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="bd855-343">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="bd855-344">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bd855-345">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="bd855-346">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *:::no-loc(appsettings.json):::* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd855-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd855-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd855-348">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd855-349">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd855-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd855-350">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="bd855-351">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-351">Test the app</span></span>

* <span data-ttu-id="bd855-352">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="bd855-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="bd855-353">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="bd855-353">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="bd855-354">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="bd855-355">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-355">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="bd855-357">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bd855-358">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="bd855-359">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd855-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="bd855-360">**Edit** , **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-360">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="bd855-361">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd855-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd855-362">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bd855-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bd855-363">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="bd855-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
