---
title: ASP.NET Core에서 Entity Framework Core를 사용한 Razor Pages - 자습서 1/8
author: rick-anderson
description: Entity Framework Core를 사용하여 Razor Pages 앱을 만드는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 5849f4bfb9d0355177ceb5c2f4236c6d39dd9e92
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340038"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="aed20-103">ASP.NET Core에서 Entity Framework Core를 사용한 Razor Pages - 자습서 1/8</span><span class="sxs-lookup"><span data-stu-id="aed20-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="aed20-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aed20-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aed20-105">[ASP.NET Core Razor Pages](xref:razor-pages/index) 앱에서 EF(Entity Framework) Core를 사용하는 방법을 보여 주는 자습서 시리즈 중 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="aed20-106">이 자습서에서는 가상 Contoso 대학의 웹 사이트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="aed20-107">이 사이트에는 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="aed20-108">이 자습서에서는 Code First 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="aed20-109">Database First 방법을 사용하여 이 자습서를 수행하는 데 대한 정보는 [이 Github 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16897)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="aed20-110">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="aed20-111">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="aed20-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aed20-112">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="aed20-112">Prerequisites</span></span>

* <span data-ttu-id="aed20-113">Razor Pages를 처음 사용한다면 이 자습서를 시작하기 전에 Razor [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) 자습서 시리즈를 진행하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="aed20-116">데이터베이스 엔진</span><span class="sxs-lookup"><span data-stu-id="aed20-116">Database engines</span></span>

<span data-ttu-id="aed20-117">Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="aed20-118">Visual Studio Code 지침에서는 플랫폼 간 데이터베이스 엔진인 [SQLite](https://www.sqlite.org/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="aed20-119">SQLite를 사용하도록 선택하는 경우 [SQLite용 DB 브라우저](https://sqlitebrowser.org/)와 같이 SQLite 데이터베이스를 관리하고 볼 수 있는 타사 도구를 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="aed20-120">문제 해결</span><span class="sxs-lookup"><span data-stu-id="aed20-120">Troubleshooting</span></span>

<span data-ttu-id="aed20-121">해결할 수 없는 문제가 발생한 경우 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="aed20-122">도움을 얻으려면 [ASP.NET Core 태그](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core 태그](https://stackoverflow.com/questions/tagged/entity-framework-core)를 사용하여 StackOverflow.com에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="aed20-123">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="aed20-123">The sample app</span></span>

<span data-ttu-id="aed20-124">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="aed20-125">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="aed20-126">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-126">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index30.png)

![학생 편집 페이지](intro/_static/student-edit30.png)

<span data-ttu-id="aed20-129">이 사이트의 UI 스타일은 기본 제공 프로젝트 템플릿을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="aed20-130">이 자습서에서는 UI를 사용자 지정하는 방법이 아닌 EF Core를 ASP.NET Core와 함께 사용하는 방법을 중점적으로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="aed20-131">웹앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="aed20-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="aed20-133">Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="aed20-134">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="aed20-135">**새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `ContosoUniversity`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="aed20-136">코드를 복사할 때 `namespace`가 일치하도록 대문자 표시를 포함하여 이 이름을 정확히 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="aed20-137">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-137">Select **Create**.</span></span>
1. <span data-ttu-id="aed20-138">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="aed20-139">드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="aed20-140">**ASP.NET Core 웹앱(Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="aed20-140">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="aed20-141">
      ![새 ASP.NET Core 프로젝트 **만들기** 대화 상자](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="aed20-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-143">터미널에서 프로젝트 폴더를 만들어야 하는 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="aed20-144">다음 명령을 실행하여 Razor Pages 프로젝트를 만들고 `cd`를 사용하여 새 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="aed20-145">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="aed20-145">Set up the site style</span></span>

<span data-ttu-id="aed20-146">다음 코드를 복사하여 *Pages/Shared/_Layout.cshtml* 파일에 붙여넣습니다. [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="aed20-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="aed20-147">레이아웃 파일은 사이트 머리글, 바닥글 및 메뉴를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="aed20-148">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="aed20-149">모든 “ContosoUniversity”가 “Contoso University”로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="aed20-150">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-150">There are three occurrences.</span></span>
* <span data-ttu-id="aed20-151">**홈** 및 **프라이버시** 메뉴 항목이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="aed20-152">**정보**,**학생**, **과정**, **강사** 및 **부서** 에 대해 항목이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="aed20-153">*Pages/Index.cshtml* 에서 파일의 내용을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="aed20-154">위의 코드는 ASP.NET Core에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="aed20-155">앱을 실행하여 홈페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="aed20-156">데이터 모델</span><span class="sxs-lookup"><span data-stu-id="aed20-156">The data model</span></span>

<span data-ttu-id="aed20-157">다음 섹션에서는 데이터 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-157">The following sections create a data model:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="aed20-159">학생은 개수와 관계없이 원하는 과정에 등록할 수 있으며 한 과정에는 여러 명이 등록될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="aed20-160">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-160">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

* <span data-ttu-id="aed20-162">프로젝트 폴더에 *Models* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="aed20-163">다음 코드로 *Models/Student.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="aed20-164">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="aed20-165">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="aed20-166">따라서 `Student` 클래스 기본 키의 자동으로 인식되는 대체 이름은 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="aed20-167">자세한 내용은 [EF Core - 키](/ef/core/modeling/keys?tabs=data-annotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="aed20-168">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="aed20-169">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="aed20-170">이 경우 `Student` 엔터티의 `Enrollments` 속성은 해당 Student에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="aed20-171">예를 데이터베이스의 Student 행에 두 개의 관련 Enrollment 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 Enrollment 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="aed20-172">데이터베이스에서 StudentID 열에 학생 ID 값이 포함된 경우 Enrollment 행은 Student 행과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="aed20-173">예를 들어 Student 행에 ID=1이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="aed20-174">관련 Enrollment 행에는 StudentID=1이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="aed20-175">StudentID는 Enrollment 테이블의 ‘외래 키’입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="aed20-176">관련 Enrollment 엔터티가 여러 개 있을 수 있으므로 `Enrollments` 속성은 `ICollection<Enrollment>`로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="aed20-177">`List<Enrollment>` 또는`HashSet<Enrollment>`와 같은 다른 컬렉션 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="aed20-178">`ICollection<Enrollment>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<Enrollment>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="aed20-179">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-179">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="aed20-181">다음 코드로 *Models/Enrollment.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="aed20-182">`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="aed20-183">프로덕션 데이터 모델의 경우 하나의 패턴을 선택하고 일관되게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="aed20-184">이 자습서에서는 두 항목이 작동하는 것을 보여 주기 위해 둘 다 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="aed20-185">`classname` 없이 `ID`를 사용하면 특정 종류의 데이터 모델 변경 내용을 더 쉽게 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="aed20-186">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="aed20-187">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 [nullable](/dotnet/csharp/programming-guide/nullable-types/)이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="aed20-188">Null인 등급은 0등급과는 다릅니다. Null은 알 수 없거나 아직 할당되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="aed20-189">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="aed20-190">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="aed20-191">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="aed20-192">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="aed20-193">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="aed20-194">예를 들어 `Student` 엔터티의 기본 키는 `ID`이므로 `StudentID`는 `Student` 탐색 속성의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="aed20-195">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="aed20-196">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="aed20-197">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-197">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="aed20-199">다음 코드로 *Models/Course.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="aed20-200">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="aed20-201">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="aed20-202">`DatabaseGenerated` 특성을 사용하면 데이터베이스에서 기본 키를 생성하도록 하지 않고 앱에서 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="aed20-203">프로젝트를 빌드하여 컴파일러 오류가 없는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="aed20-204">학생 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="aed20-204">Scaffold Student pages</span></span>

<span data-ttu-id="aed20-205">이 섹션에서는 ASP.NET Core 스캐폴딩 도구를 사용하여 다음을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="aed20-206">EF Core `DbContext` 클래스.</span><span class="sxs-lookup"><span data-stu-id="aed20-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="aed20-207">컨텍스트는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="aed20-208">이 클래스는 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 클래스에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="aed20-209">`Student` 엔터티에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하는 Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="aed20-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-211">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="aed20-212">**솔루션 탐색기** 에서 *Pages/Students* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aed20-213">**새 스캐폴드 항목 추가** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="aed20-214">왼쪽 탭에서 **설치 > 공통 > Razor 페이지** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="aed20-215">**RazorEntity Framework를 사용한 Razor 페이지(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="aed20-216">**Entity Framework(CRUD)를 사용한 Razor Pages 추가** 대화 상자에서 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="aed20-217">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="aed20-218">**데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="aed20-219">데이터 컨텍스트 이름을 `ContosoUniversityContext`가 아닌 `SchoolContext`로 끝나게 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="aed20-220">업데이트된 컨텍스트 이름: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="aed20-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="aed20-221">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-221">Select **Add**.</span></span>

<span data-ttu-id="aed20-222">다음 패키지가 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-224">다음 .NET Core CLI 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="aed20-225">스캐폴딩에는 Microsoft.VisualStudio.Web.CodeGeneration.Design 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="aed20-226">앱은 SQL Server를 사용하지 않지만 스캐폴딩 도구에는 SQL Server 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="aed20-227">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="aed20-228">다음 명령을 실행하여 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="aed20-229">다음 명령을 실행하여 학생 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="aed20-230">**Windows**</span><span class="sxs-lookup"><span data-stu-id="aed20-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="aed20-231">**macOS 또는 Linux**</span><span class="sxs-lookup"><span data-stu-id="aed20-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="aed20-232">앞의 단계가 실패할 경우 프로젝트를 빌드하고 스캐폴드 단계를 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="aed20-233">스캐폴딩 프로세스:</span><span class="sxs-lookup"><span data-stu-id="aed20-233">The scaffolding process:</span></span>

* <span data-ttu-id="aed20-234">*Pages/Students* 폴더에서 다음 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="aed20-235">*Create.cshtml* 및 *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-236">*Delete.cshtml* 및 *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-237">*Details.cshtml* 및 *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-238">*Edit.cshtml* 및 *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-239">*Index.cshtml* 및 *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="aed20-240">*Data/SchoolContext.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="aed20-241">*Startup.cs* 의 종속성 주입에 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="aed20-242">*appsettings.json* 에 데이터베이스 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="aed20-243">데이터베이스 연결 문자열</span><span class="sxs-lookup"><span data-stu-id="aed20-243">Database connection string</span></span>

<span data-ttu-id="aed20-244">스캐폴딩 도구가 *appsettings.json* 파일에 연결 문자열을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-246">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="aed20-247">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="aed20-248">기본적으로 LocalDB는 `C:/Users/<user>` 디렉터리에 *.mdf* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-250">SQLite 연결 문자열을 *CU.db* 로 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-250">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="aed20-251">데이터베이스 컨텍스트 클래스 업데이트</span><span class="sxs-lookup"><span data-stu-id="aed20-251">Update the database context class</span></span>

<span data-ttu-id="aed20-252">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="aed20-253">컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aed20-254">컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="aed20-255">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="aed20-256">다음 코드로 *Data/SchoolContext.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="aed20-257">앞의 코드가 단수 `DbSet<Student> Student`에서 복수 `DbSet<Student> Students`로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="aed20-258">Razor 페이지 코드가 새 `DBSet` 이름과 일치하도록 `_context.Student.`에서</span><span class="sxs-lookup"><span data-stu-id="aed20-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="aed20-259">`_context.Students.`로 전역 변경을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-259">to: `_context.Students.`</span></span>

<span data-ttu-id="aed20-260">8개 발생 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-260">There are 8 occurrences.</span></span>

<span data-ttu-id="aed20-261">엔터티 집합은 여러 엔터티를 포함하기 때문에 다수의 개발자들이 복수인 `DBSet` 속성 이름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="aed20-262">강조 표시된 코드:</span><span class="sxs-lookup"><span data-stu-id="aed20-262">The highlighted code:</span></span>

* <span data-ttu-id="aed20-263">각 엔터티 집합에 대한 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="aed20-264">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="aed20-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="aed20-265">엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="aed20-266">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="aed20-267"><xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="aed20-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="aed20-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="aed20-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="aed20-269">`SchoolContext`가 초기화되었을 때 모델이 잠기고 컨텍스트를 초기화하는 데 사용되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="aed20-270">자습서의 뒷부분에서 `Student` 엔터티가 다른 엔터티에 대한 참조를 포함하기 때문에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="aed20-271">프로젝트를 빌드하여 컴파일러 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="aed20-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="aed20-272">Startup.cs</span></span>

<span data-ttu-id="aed20-273">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aed20-274">서비스(예: `SchoolContext`)는 앱 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="aed20-275">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="aed20-276">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aed20-277">스캐폴딩 도구는 종속성 주입 컨테이너에 컨텍스트 클래스를 자동으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-279">다음 강조 표시된 줄을 스캐폴더가 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-281">스캐폴드가 추가한 코드가 `UseSqlite`를 호출하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="aed20-282">프로덕션 데이터베이스 사용에 대한 자세한 내용은 [개발에 SQLite, 프로덕션에 SQL Server 사용](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="aed20-283">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aed20-284">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="aed20-285">데이터베이스 예외 필터 추가</span><span class="sxs-lookup"><span data-stu-id="aed20-285">Add the database exception filter</span></span>

<span data-ttu-id="aed20-286">다음 코드와 같이 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A>를 `ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="aed20-288">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="aed20-289">PMC에서 다음을 입력하여 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="aed20-291">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 패키지는 Entity Framework Core 오류 페이지에 대한 ASP.NET Core 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="aed20-292">이 미들웨어는 Entity Framework Core 마이그레이션의 오류를 검색 및 진단하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="aed20-293">`AddDatabaseDeveloperPageExceptionFilter`는 [개발 환경](xref:fundamentals/environments)에서 유용한 오류 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="aed20-294">데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="aed20-294">Create the database</span></span>

<span data-ttu-id="aed20-295">*Program.cs* 를 업데이트하여 데이터베이스가 없는 경우 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="aed20-296">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 메서드는 컨텍스트의 데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="aed20-297">데이터베이스가 없는 경우 데이터베이스 및 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="aed20-298">`EnsureCreated`를 사용하면 다음 워크플로가 데이터 모델 변경 내용을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="aed20-299">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-299">Delete the database.</span></span> <span data-ttu-id="aed20-300">모든 기존 데이터가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-300">Any existing data is lost.</span></span>
* <span data-ttu-id="aed20-301">데이터 모델을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-301">Change the data model.</span></span> <span data-ttu-id="aed20-302">예를 들어 `EmailAddress` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="aed20-303">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-303">Run the app.</span></span>
* <span data-ttu-id="aed20-304">`EnsureCreated`는 새 스키마를 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="aed20-305">이 워크플로는 데이터를 보존할 필요가 없는 경우 스키마가 빠르게 업데이트되는 개발 초기에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="aed20-306">데이터베이스에 입력된 데이터를 보존해야 하는 경우는 상황이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="aed20-307">이 경우에는 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="aed20-308">자습서 시리즈의 뒷부분에서는 `EnsureCreated`에서 만든 데이터베이스를 삭제하고 마이그레이션을 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="aed20-309">`EnsureCreated`에서 만든 데이터베이스는 마이그레이션을 사용하여 업데이트할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="aed20-310">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-310">Test the app</span></span>

* <span data-ttu-id="aed20-311">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-311">Run the app.</span></span>
* <span data-ttu-id="aed20-312">**학생** 링크 및 **새로 만들기** 를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="aed20-313">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="aed20-314">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="aed20-314">Seed the database</span></span>

<span data-ttu-id="aed20-315">`EnsureCreated` 메서드는 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="aed20-316">이 섹션에서는 테스트 데이터로 데이터베이스를 채우는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="aed20-317">다음 코드로 *Data/DbInitializer.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="aed20-318">이 코드는 데이터베이스에 학생이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="aed20-319">학생이 없는 경우 테스트 데이터를 데이터베이스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="aed20-320">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 만들어 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="aed20-321">*Program.cs* 에서 `EnsureCreated` 호출을 `DbInitializer.Initialize` 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-321">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-323">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="aed20-324">데이터베이스를 삭제하려면 `Y`로 답합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-326">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="aed20-327">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-327">Restart the app.</span></span>
* <span data-ttu-id="aed20-328">학생 페이지를 선택하여 시드된 데이터를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="aed20-329">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="aed20-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-331">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="aed20-332">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="aed20-333">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="aed20-334">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="aed20-335">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="aed20-336">**Student** 테이블을 마우스 오른쪽 단추로 클릭하고 **코드 보기** 를 클릭하여 `Student` 모델이 `Student` 테이블 스키마에 어떻게 매핑되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-338">SQLite 도구를 사용하여 데이터베이스 스키마 및 시드된 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="aed20-339">데이터베이스 파일은 *CU.db* 로 이름이 지정되고 프로젝트 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="aed20-340">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="aed20-340">Asynchronous code</span></span>

<span data-ttu-id="aed20-341">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="aed20-342">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="aed20-343">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="aed20-344">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="aed20-345">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="aed20-346">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="aed20-347">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="aed20-348">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="aed20-349">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="aed20-350">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="aed20-351">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="aed20-352">반환되는 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="aed20-353">`Task<T>` 반환 형식은 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="aed20-354">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="aed20-355">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="aed20-356">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="aed20-357">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="aed20-358">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="aed20-359">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="aed20-360">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="aed20-361">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="aed20-362">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="aed20-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="aed20-363">비동기 코드의 성능 이점을 이용하려면 라이브러리 패키지(예: 페이징)가 쿼리를 데이터베이스에 보내는 EF Core 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="aed20-364">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="aed20-365">성능 고려 사항</span><span class="sxs-lookup"><span data-stu-id="aed20-365">Performance considerations</span></span>

<span data-ttu-id="aed20-366">일반적으로 웹 페이지는 임의의 행 수를 로드하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="aed20-367">쿼리는 페이징이나 제한 방법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="aed20-368">예를 들어 앞의 쿼리는 `Take`를 사용하여 반환되는 행을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="aed20-369">부분적으로 열거를 통해 데이터베이스 예외가 발생할 경우, 뷰에서 대규모 테이블을 열거하면 일부분이 생성된 HTTP 200 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="aed20-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize>의 기본값은 1024입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="aed20-371">다음 코드는 `MaxModelBindingCollectionSize`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="aed20-372">페이징에 대해서는 자습서 뒷부분에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="aed20-373">다음 단계</span><span class="sxs-lookup"><span data-stu-id="aed20-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="aed20-374">다음 자습서</span><span class="sxs-lookup"><span data-stu-id="aed20-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="aed20-375">[ASP.NET Core Razor Pages](xref:razor-pages/index) 앱에서 EF(Entity Framework) Core를 사용하는 방법을 보여 주는 자습서 시리즈 중 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="aed20-376">이 자습서에서는 가상 Contoso 대학의 웹 사이트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="aed20-377">이 사이트에는 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="aed20-378">이 자습서에서는 Code First 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="aed20-379">Database First 방법을 사용하여 이 자습서를 수행하는 데 대한 정보는 [이 Github 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16897)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="aed20-380">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="aed20-381">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="aed20-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aed20-382">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="aed20-382">Prerequisites</span></span>

* <span data-ttu-id="aed20-383">Razor Pages를 처음 사용한다면 이 자습서를 시작하기 전에 Razor [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) 자습서 시리즈를 진행하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="aed20-386">데이터베이스 엔진</span><span class="sxs-lookup"><span data-stu-id="aed20-386">Database engines</span></span>

<span data-ttu-id="aed20-387">Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="aed20-388">Visual Studio Code 지침에서는 플랫폼 간 데이터베이스 엔진인 [SQLite](https://www.sqlite.org/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="aed20-389">SQLite를 사용하도록 선택하는 경우 [SQLite용 DB 브라우저](https://sqlitebrowser.org/)와 같이 SQLite 데이터베이스를 관리하고 볼 수 있는 타사 도구를 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="aed20-390">문제 해결</span><span class="sxs-lookup"><span data-stu-id="aed20-390">Troubleshooting</span></span>

<span data-ttu-id="aed20-391">해결할 수 없는 문제가 발생한 경우 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="aed20-392">도움을 얻으려면 [ASP.NET Core 태그](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core 태그](https://stackoverflow.com/questions/tagged/entity-framework-core)를 사용하여 StackOverflow.com에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="aed20-393">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="aed20-393">The sample app</span></span>

<span data-ttu-id="aed20-394">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="aed20-395">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="aed20-396">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-396">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index30.png)

![학생 편집 페이지](intro/_static/student-edit30.png)

<span data-ttu-id="aed20-399">이 사이트의 UI 스타일은 기본 제공 프로젝트 템플릿을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="aed20-400">이 자습서에서는 UI를 사용자 지정하는 방법이 아닌 EF Core를 사용하는 방법을 중점적으로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="aed20-401">페이지 맨 위에 있는 링크를 따라 완료된 프로젝트의 소스 코드를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="aed20-402">*cu30* 폴더에는 자습서의 ASP.NET Core 3.0 버전에 대한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="aed20-403">자습서 1~7의 코드 상태를 반영하는 파일은 *cu30snapshots* 폴더에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-405">완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="aed20-406">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-406">Build the project.</span></span>
* <span data-ttu-id="aed20-407">PMC(패키지 관리자 콘솔)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="aed20-408">프로젝트를 실행하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-410">완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="aed20-411">*ContosoUniversity.csproj* 를 삭제하고 *ContosoUniversitySQLite.csproj* 의 이름을 *ContosoUniversity.csproj* 로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-411">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="aed20-412">*Program.cs* 에서 `StartupSQLite`를 사용하도록 `#define Startup`을 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-412">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="aed20-413">*appSettings.json* 을 삭제하고 *appSettingsSQLite.json* 의 이름을 *appSettings.json* 으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-413">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="aed20-414">*Migrations* 폴더를 삭제하고 *MigrationsSQL* 의 이름을 *Migrations* 로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="aed20-415">`#if SQLiteVersion`에 대한 전체 검색을 수행하고 `#if SQLiteVersion` 및 관련된 `#endif` 문을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="aed20-416">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-416">Build the project.</span></span>
* <span data-ttu-id="aed20-417">프로젝트 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="aed20-418">SQLite 도구에서 다음 SQL 문을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="aed20-419">프로젝트를 실행하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="aed20-420">웹앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="aed20-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-422">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="aed20-423">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="aed20-424">프로젝트 이름을 *ContosoUniversity* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="aed20-425">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 대문자 표시를 포함하여 정확한 이름을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="aed20-426">드롭다운에서 **.NET Core** 및 **ASP.NET Core 3.0** 을 선택한 후 **웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-428">터미널에서 프로젝트 폴더를 만들어야 하는 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="aed20-429">다음 명령을 실행하여 Razor Pages 프로젝트를 만들고 `cd`를 사용하여 새 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="aed20-430">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="aed20-430">Set up the site style</span></span>

<span data-ttu-id="aed20-431">*Pages/Shared/_Layout.cshtml* 을 업데이트하여 사이트 헤더, 바닥글 및 메뉴를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="aed20-432">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="aed20-433">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-433">There are three occurrences.</span></span>

* <span data-ttu-id="aed20-434">**홈** 및 **프라이버시** 메뉴 항목을 삭제하고 **정보**, **학생**, **과정**, **강사** 및 **부서** 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="aed20-435">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="aed20-436">*Pages/Index.cshtml* 에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET Core에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-436">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="aed20-437">앱을 실행하여 홈페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="aed20-438">데이터 모델</span><span class="sxs-lookup"><span data-stu-id="aed20-438">The data model</span></span>

<span data-ttu-id="aed20-439">다음 섹션에서는 데이터 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-439">The following sections create a data model:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="aed20-441">학생은 개수와 관계없이 원하는 과정에 등록할 수 있으며 한 과정에는 여러 명이 등록될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="aed20-442">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-442">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

* <span data-ttu-id="aed20-444">프로젝트 폴더에 *Models* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="aed20-445">다음 코드로 *Models/Student.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="aed20-446">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="aed20-447">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="aed20-448">따라서 `Student` 클래스 기본 키의 자동으로 인식되는 대체 이름은 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="aed20-449">자세한 내용은 [EF Core - 키](/ef/core/modeling/keys?tabs=data-annotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="aed20-450">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="aed20-451">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="aed20-452">이 경우 `Student` 엔터티의 `Enrollments` 속성은 해당 Student에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="aed20-453">예를 데이터베이스의 Student 행에 두 개의 관련 Enrollment 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 Enrollment 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="aed20-454">데이터베이스에서 StudentID 열에 학생 ID 값이 포함된 경우 Enrollment 행은 Student 행과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="aed20-455">예를 들어 Student 행에 ID=1이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="aed20-456">관련 Enrollment 행에는 StudentID=1이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="aed20-457">StudentID는 Enrollment 테이블의 ‘외래 키’입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="aed20-458">관련 Enrollment 엔터티가 여러 개 있을 수 있으므로 `Enrollments` 속성은 `ICollection<Enrollment>`로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="aed20-459">`List<Enrollment>` 또는`HashSet<Enrollment>`와 같은 다른 컬렉션 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="aed20-460">`ICollection<Enrollment>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<Enrollment>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="aed20-461">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-461">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="aed20-463">다음 코드로 *Models/Enrollment.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="aed20-464">`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="aed20-465">프로덕션 데이터 모델의 경우 하나의 패턴을 선택하고 일관되게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="aed20-466">이 자습서에서는 두 항목이 작동하는 것을 보여 주기 위해 둘 다 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="aed20-467">`classname` 없이 `ID`를 사용하면 특정 종류의 데이터 모델 변경 내용을 더 쉽게 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="aed20-468">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="aed20-469">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 [nullable](/dotnet/csharp/programming-guide/nullable-types/)이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="aed20-470">Null인 등급은 0등급과는 다릅니다. Null은 알 수 없거나 아직 할당되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="aed20-471">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="aed20-472">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="aed20-473">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="aed20-474">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="aed20-475">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="aed20-476">예를 들어 `Student` 엔터티의 기본 키는 `ID`이므로 `StudentID`는 `Student` 탐색 속성의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="aed20-477">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="aed20-478">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="aed20-479">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-479">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="aed20-481">다음 코드로 *Models/Course.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="aed20-482">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="aed20-483">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="aed20-484">`DatabaseGenerated` 특성을 사용하면 데이터베이스에서 기본 키를 생성하도록 하지 않고 앱에서 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="aed20-485">프로젝트를 빌드하여 컴파일러 오류가 없는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="aed20-486">학생 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="aed20-486">Scaffold Student pages</span></span>

<span data-ttu-id="aed20-487">이 섹션에서는 ASP.NET Core 스캐폴딩 도구를 사용하여 다음을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="aed20-488">EF Core ‘컨텍스트’ 클래스.</span><span class="sxs-lookup"><span data-stu-id="aed20-488">An EF Core *context* class.</span></span> <span data-ttu-id="aed20-489">컨텍스트는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="aed20-490">이 클래스는 `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="aed20-491">`Student` 엔터티에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하는 Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="aed20-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-493">*Pages* 폴더에 *Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="aed20-494">**솔루션 탐색기** 에서 *Pages/Students* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-494">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aed20-495">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="aed20-496">**Entity Framework(CRUD)를 사용한 Razor Pages 추가** 대화 상자에서 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="aed20-497">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="aed20-498">**데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="aed20-499">데이터 컨텍스트 이름을 *ContosoUniversity.Models.ContosoUniversityContext* 에서 *ContosoUniversity.Data.SchoolContext* 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="aed20-500">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-500">Select **Add**.</span></span>

<span data-ttu-id="aed20-501">다음 패키지가 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-503">다음 .NET Core CLI 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="aed20-504">스캐폴딩에는 Microsoft.VisualStudio.Web.CodeGeneration.Design 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="aed20-505">앱은 SQL Server를 사용하지 않지만 스캐폴딩 도구에는 SQL Server 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="aed20-506">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="aed20-507">다음 명령을 실행하여 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="aed20-508">다음 명령을 실행하여 학생 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="aed20-509">**Windows**</span><span class="sxs-lookup"><span data-stu-id="aed20-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="aed20-510">**macOS 또는 Linux**</span><span class="sxs-lookup"><span data-stu-id="aed20-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="aed20-511">이전 단계에서 문제가 발생하면 프로젝트를 빌드하고 스캐폴드 단계를 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="aed20-512">스캐폴딩 프로세스:</span><span class="sxs-lookup"><span data-stu-id="aed20-512">The scaffolding process:</span></span>

* <span data-ttu-id="aed20-513">*Pages/Students* 폴더에서 다음 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="aed20-514">*Create.cshtml* 및 *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-515">*Delete.cshtml* 및 *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-516">*Details.cshtml* 및 *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-517">*Edit.cshtml* 및 *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="aed20-518">*Index.cshtml* 및 *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="aed20-519">*Data/SchoolContext.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="aed20-520">*Startup.cs* 의 종속성 주입에 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="aed20-521">*appsettings.json* 에 데이터베이스 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="aed20-522">데이터베이스 연결 문자열</span><span class="sxs-lookup"><span data-stu-id="aed20-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-524">*appsettings.json* 파일은 연결 문자열 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="aed20-525">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="aed20-526">기본적으로 LocalDB는 `C:/Users/<user>` 디렉터리에 *.mdf* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-528">*CU.db* 라는 SQLite 데이터베이스 파일을 가리키도록 연결 문자열을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-528">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="aed20-529">데이터베이스 컨텍스트 클래스 업데이트</span><span class="sxs-lookup"><span data-stu-id="aed20-529">Update the database context class</span></span>

<span data-ttu-id="aed20-530">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="aed20-531">컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aed20-532">컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="aed20-533">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="aed20-534">다음 코드로 *Data/SchoolContext.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="aed20-535">강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="aed20-536">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="aed20-536">In EF Core terminology:</span></span>

* <span data-ttu-id="aed20-537">엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="aed20-538">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aed20-539">엔터티 집합은 여러 엔터티를 포함하므로 DBSet 속성은 복수형 이름이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="aed20-540">스캐폴딩 도구로 `Student` DBSet를 만들었으므로 이 단계에서는 이 DBSet를 복수형 `Students`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="aed20-541">Razor Pages 코드가 새 DBSet 이름과 일치하게 만들려면 전체 프로젝트에서 `_context.Student`를 `_context.Students`로 전체 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="aed20-542">8개 발생 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-542">There are 8 occurrences.</span></span>

<span data-ttu-id="aed20-543">프로젝트를 빌드하여 컴파일러 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="aed20-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="aed20-544">Startup.cs</span></span>

<span data-ttu-id="aed20-545">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aed20-546">서비스(예: EF Core 데이터베이스 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aed20-547">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aed20-548">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aed20-549">스캐폴딩 도구는 종속성 주입 컨테이너에 컨텍스트 클래스를 자동으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-551">`ConfigureServices`에서 강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-553">`ConfigureServices`에서 스캐폴더에서 추가된 코드가 `UseSqlite`를 호출하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="aed20-554">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aed20-555">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="aed20-556">데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="aed20-556">Create the database</span></span>

<span data-ttu-id="aed20-557">*Program.cs* 를 업데이트하여 데이터베이스가 없는 경우 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="aed20-558">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 메서드는 컨텍스트의 데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="aed20-559">데이터베이스가 없는 경우 데이터베이스 및 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="aed20-560">`EnsureCreated`를 사용하면 다음 워크플로가 데이터 모델 변경 내용을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="aed20-561">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-561">Delete the database.</span></span> <span data-ttu-id="aed20-562">모든 기존 데이터가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-562">Any existing data is lost.</span></span>
* <span data-ttu-id="aed20-563">데이터 모델을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-563">Change the data model.</span></span> <span data-ttu-id="aed20-564">예를 들어 `EmailAddress` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="aed20-565">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-565">Run the app.</span></span>
* <span data-ttu-id="aed20-566">`EnsureCreated`는 새 스키마를 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="aed20-567">이 워크플로는 데이터를 보존할 필요가 없는 경우 스키마가 빠르게 업데이트되는 개발 초기에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="aed20-568">데이터베이스에 입력된 데이터를 보존해야 하는 경우는 상황이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="aed20-569">이 경우에는 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="aed20-570">자습서 시리즈의 뒷부분에서는 `EnsureCreated`에서 만든 데이터베이스를 삭제하고 마이그레이션을 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="aed20-571">`EnsureCreated`에서 만든 데이터베이스는 마이그레이션을 사용하여 업데이트할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="aed20-572">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-572">Test the app</span></span>

* <span data-ttu-id="aed20-573">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-573">Run the app.</span></span>
* <span data-ttu-id="aed20-574">**학생** 링크 및 **새로 만들기** 를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="aed20-575">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="aed20-576">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="aed20-576">Seed the database</span></span>

<span data-ttu-id="aed20-577">`EnsureCreated` 메서드는 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="aed20-578">이 섹션에서는 테스트 데이터로 데이터베이스를 채우는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="aed20-579">다음 코드로 *Data/DbInitializer.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="aed20-580">이 코드는 데이터베이스에 학생이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="aed20-581">학생이 없는 경우 테스트 데이터를 데이터베이스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="aed20-582">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 만들어 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="aed20-583">*Program.cs* 에서 `EnsureCreated` 호출을 `DbInitializer.Initialize` 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-583">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-585">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-587">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="aed20-588">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-588">Restart the app.</span></span>

* <span data-ttu-id="aed20-589">학생 페이지를 선택하여 시드된 데이터를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="aed20-590">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="aed20-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-592">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="aed20-593">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="aed20-594">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="aed20-595">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="aed20-596">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="aed20-597">**Student** 테이블을 마우스 오른쪽 단추로 클릭하고 **코드 보기** 를 클릭하여 `Student` 모델이 `Student` 테이블 스키마에 어떻게 매핑되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-599">SQLite 도구를 사용하여 데이터베이스 스키마 및 시드된 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="aed20-600">데이터베이스 파일은 *CU.db* 로 이름이 지정되고 프로젝트 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="aed20-601">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="aed20-601">Asynchronous code</span></span>

<span data-ttu-id="aed20-602">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="aed20-603">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="aed20-604">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="aed20-605">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="aed20-606">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="aed20-607">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="aed20-608">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="aed20-609">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="aed20-610">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="aed20-611">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="aed20-612">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="aed20-613">반환되는 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="aed20-614">`Task<T>` 반환 형식은 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="aed20-615">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="aed20-616">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="aed20-617">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="aed20-618">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="aed20-619">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="aed20-620">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="aed20-621">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="aed20-622">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="aed20-623">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="aed20-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="aed20-624">비동기 코드의 성능 이점을 이용하려면 라이브러리 패키지(예: 페이징)가 쿼리를 데이터베이스에 보내는 EF Core 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="aed20-625">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="aed20-626">다음 단계</span><span class="sxs-lookup"><span data-stu-id="aed20-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="aed20-627">다음 자습서</span><span class="sxs-lookup"><span data-stu-id="aed20-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aed20-628">Contoso University 샘플 웹앱은 EF(Entity Framework) Core를 사용하여 ASP.NET Core Razor Pages 앱을 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="aed20-629">샘플 앱은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="aed20-630">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="aed20-631">이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="aed20-632">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="aed20-633">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="aed20-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aed20-634">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="aed20-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="aed20-637">[Razor Pages](xref:razor-pages/index) 숙련도.</span><span class="sxs-lookup"><span data-stu-id="aed20-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="aed20-638">신규 프로그래머는 이 시리즈를 시작하기 전에 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 먼저 완료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="aed20-639">문제 해결</span><span class="sxs-lookup"><span data-stu-id="aed20-639">Troubleshooting</span></span>

<span data-ttu-id="aed20-640">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="aed20-641">도움을 얻으려면 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="aed20-642">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="aed20-642">The Contoso University web app</span></span>

<span data-ttu-id="aed20-643">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="aed20-644">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="aed20-645">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-645">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

<span data-ttu-id="aed20-648">이 사이트의 UI 스타일은 기본 제공 템플릿에서 생성된 것과 가깝습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="aed20-649">이 자습서에서는 UI가 아닌 Razor 페이지를 사용한 EF Core를 중점적으로 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="aed20-650">ContosoUniversity Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="aed20-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-652">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="aed20-653">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="aed20-654">프로젝트 이름을 **ContosoUniversity** 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="aed20-655">코드를 복사/붙여넣을 때 네임스페이스와 일치할 수 있도록 프로젝트 이름을 *ContosoUniversity* 로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="aed20-656">드롭다운에서 **ASP.NET Core 2.1** 을 선택한 다음, **웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="aed20-657">이전 단계의 이미지는 [Razor 웹앱 만들기](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="aed20-658">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="aed20-660">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="aed20-660">Set up the site style</span></span>

<span data-ttu-id="aed20-661">몇 가지 변경 내용으로 사이트 메뉴, 레이아웃 및 홈페이지를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="aed20-662">*Pages/Shared/_Layout.cshtml* 을 다음 변경 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="aed20-663">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="aed20-664">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-664">There are three occurrences.</span></span>

* <span data-ttu-id="aed20-665">**학생**, **강좌**, **강사** 및 **부서** 에 대한 메뉴 항목을 추가하고 **연락처** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-665">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="aed20-666">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-666">The changes are highlighted.</span></span> <span data-ttu-id="aed20-667">(모든 표시가 표시되지는 *않습니다*.)</span><span class="sxs-lookup"><span data-stu-id="aed20-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="aed20-668">*Pages/Index.cshtml* 에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-668">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="aed20-669">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="aed20-669">Create the data model</span></span>

<span data-ttu-id="aed20-670">Contoso University 앱에 대한 엔터티 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="aed20-671">다음과 같은 세 가지 엔터티로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-671">Start with the following three entities:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="aed20-673">`Student` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="aed20-674">`Course` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="aed20-675">학생은 여러 강좌에 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="aed20-676">강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="aed20-677">다음 섹션에서 각각에 대한 이러한 엔터티에 대한 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="aed20-678">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-678">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="aed20-680">*모델* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-680">Create a *Models* folder.</span></span> <span data-ttu-id="aed20-681">*모델* 폴더에서 다음 코드로 *Student.cs* 라는 이름의 클래스 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="aed20-682">`ID` 속성은 이 클래스에 해당하는 DB(데이터베이스) 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="aed20-683">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="aed20-684">`classnameID`에서 `classname`은 클래스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="aed20-685">기본 키가 자동으로 인식되는 경우 대안은 앞의 예제에서 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="aed20-686">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="aed20-687">탐색 속성은 이 엔터티와 관련된 다른 엔터티에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="aed20-688">이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student`에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="aed20-689">예를 들어 DB의 학생 행에 두 개의 관련 등록 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="aed20-690">관련된 `Enrollment` 행은 `StudentID` 열에서 해당 학생의 기본 키 값을 포함하는 열입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="aed20-691">예를 들어 ID=1인 학생에 `Enrollment` 테이블의 두 개 행이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="aed20-692">`Enrollment` 테이블에 `StudentID` = 1인 두 개의 행이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="aed20-693">`StudentID`는 `Student` 테이블에서 학생을 지정하는 `Enrollment` 테이블의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="aed20-694">탐색 속성이 여러 엔터티를 포함하는 경우 탐색 속성은 `ICollection<T>`와 같은 목록 유형이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="aed20-695">`ICollection<T>`는 지정할 수 있으며, `List<T>` 또는 `HashSet<T>`와 같은 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="aed20-696">`ICollection<T>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="aed20-697">여러 엔터티를 포함하는 탐색 속성은 다대다 및 일대다 관계에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="aed20-698">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-698">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="aed20-700">*모델* 폴더에서 다음 코드로 *Enrollment.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="aed20-701">`EnrollmentID` 속성은 기본 키입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="aed20-702">이 엔터티는 `Student` 엔터티 같은 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="aed20-703">일반적으로 개발자는 하나의 패턴을 선택하여 데이터 모델 전체에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="aed20-704">자습서의 뒷부분에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 내용이 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="aed20-705">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="aed20-706">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="aed20-707">Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="aed20-708">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="aed20-709">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="aed20-710">`Student` 엔터티는 여러 `Enrollment` 엔터티를 포함하는 `Student.Enrollments` 탐색 속성과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="aed20-711">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="aed20-712">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="aed20-713">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="aed20-714">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 기본 키가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="aed20-715">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="aed20-716">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="aed20-717">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="aed20-717">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="aed20-719">*모델* 폴더에서 다음 코드로 *Course.cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="aed20-720">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="aed20-721">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="aed20-722">앱은 `DatabaseGenerated` 특성을 통해 DB가 생성하도록 하는 대신 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="aed20-723">학생 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="aed20-723">Scaffold the student model</span></span>

<span data-ttu-id="aed20-724">이 섹션에서는 학생 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="aed20-725">즉, 스캐폴드 도구는 학생 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="aed20-726">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-726">Build the project.</span></span>
* <span data-ttu-id="aed20-727">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aed20-729">**솔루션 탐색기** 에서 *Pages/Students* 폴더 > **추가** > **스캐폴드 항목 새로 만들기** 를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-729">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aed20-730">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="aed20-731">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="aed20-732">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="aed20-733">**데이터 컨텍스트 클래스** 행에서 **+** (더하기)를 선택 하고 생성된 이름을 서명하고 **ContosoUniversity.Models.SchoolContext** 로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="aed20-734">**데이터 컨텍스트 클래스** 드롭다운에서 **ContosoUniversity.Models.SchoolContext** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="aed20-735">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-735">Select **Add**.</span></span>

![CRUD 대화 상자](intro/_static/s1.png)

<span data-ttu-id="aed20-737">이전 단계에서 문제가 발생한 경우 [영화 모델 스캐폴드](xref:tutorials/razor-pages/model#scaffold-the-movie-model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aed20-739">다음 명령을 실행하여 학생 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="aed20-740">스캐폴드 프로세스는 다음 파일을 생성하고 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="aed20-741">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="aed20-741">Files created</span></span>

* <span data-ttu-id="aed20-742">*Pages/Students* 만들기, 삭제, 세부 정보, 편집, 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="aed20-743">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="aed20-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="aed20-744">파일 업데이트</span><span class="sxs-lookup"><span data-stu-id="aed20-744">File updates</span></span>

* <span data-ttu-id="aed20-745">*Startup.cs* : 이 파일의 변경 내용은 다음 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="aed20-746">*appsettings.json* : 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="aed20-747">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="aed20-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="aed20-748">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aed20-749">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aed20-750">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aed20-751">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aed20-752">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="aed20-753">*Startup.cs* 의 `ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="aed20-754">강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="aed20-755">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aed20-756">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="aed20-757">기본 업데이트</span><span class="sxs-lookup"><span data-stu-id="aed20-757">Update main</span></span>

<span data-ttu-id="aed20-758">*Program.cs* 에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-758">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="aed20-759">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="aed20-760">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="aed20-761">`EnsureCreated` 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="aed20-762">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="aed20-763">`EnsureCreated`는 컨텍스트에 대한 데이터베이스가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="aed20-764">존재하는 경우 아무런 동작이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-764">If it exists, no action is taken.</span></span> <span data-ttu-id="aed20-765">존재하지 않는 경우 데이터베이스 및 해당하는 모든 스키마가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="aed20-766">`EnsureCreated`는 데이터베이스를 만드는 데 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="aed20-767">`EnsureCreated`를 사용하여 만든 데이터베이스는 나중에 마이그레이션을 사용하여 업데이트될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="aed20-768">앱 시작 시 다음 작업 흐름을 허용하는 `EnsureCreated`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="aed20-769">DB를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-769">Delete the DB.</span></span>
* <span data-ttu-id="aed20-770">DB 스키마를 변경합니다(예: `EmailAddress` 필드 추가).</span><span class="sxs-lookup"><span data-stu-id="aed20-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="aed20-771">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-771">Run the app.</span></span>
* <span data-ttu-id="aed20-772">`EnsureCreated`가 `EmailAddress` 열이 있는 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="aed20-773">`EnsureCreated`는 스키마가 빠르게 발전하는 경우 개발 초기에 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="aed20-774">나중에 자습서에서 DB가 삭제되고 마이그레이션이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="aed20-775">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="aed20-775">Test the app</span></span>

<span data-ttu-id="aed20-776">앱을 실행하고 cookie 정책에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="aed20-777">이 앱은 개인 정보를 보관하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="aed20-778">[EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)에서 cookie 정책을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="aed20-779">**학생** 링크 및 **새로 만들기** 를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="aed20-780">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="aed20-781">SchoolContext DB 컨텍스트 검사</span><span class="sxs-lookup"><span data-stu-id="aed20-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="aed20-782">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 DB 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="aed20-783">데이터 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aed20-784">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="aed20-785">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="aed20-786">*SchoolContext.cs* 를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="aed20-787">강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="aed20-788">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="aed20-788">In EF Core terminology:</span></span>

* <span data-ttu-id="aed20-789">엔터티 집합은 일반적으로 DB 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="aed20-790">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aed20-791">`DbSet<Enrollment>` 및 `DbSet<Course>`를 생략할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="aed20-792">`Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 EF Core는 이를 암시적으로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="aed20-793">이 자습서의 경우 `DbSet<Enrollment>` 및 `DbSet<Course>`를 `SchoolContext`에 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="aed20-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="aed20-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="aed20-795">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="aed20-796">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="aed20-797">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="aed20-798">기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="aed20-799">코드를 추가하여 테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="aed20-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="aed20-800">EF Core가 빈 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="aed20-801">이 섹션에서는 테스트 데이터로 채울 `Initialize` 메서드가 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="aed20-802">*Data* 폴더에서 *DbInitializer.cs* 라는 새 클래스 파일을 만들고 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="aed20-803">참고: 앞의 코드는 `Data`가 아닌 `Models`을 네임스페이스(`namespace ContosoUniversity.Models`)에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="aed20-804">`Models`는 스캐폴더에서 생성된 코드와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="aed20-805">자세한 내용은 [이 GitHub 스캐폴딩 문제](https://github.com/aspnet/Scaffolding/issues/822)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="aed20-806">코드는 DB에 학생이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="aed20-807">DB에 학생이 없는 경우 DB는 테스트 데이터로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="aed20-808">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="aed20-809">`EnsureCreated` 메서드는 자동으로 DB 컨텍스트에 대한 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="aed20-810">DB가 있는 경우 `EnsureCreated`는 DB를 수정하지 않고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="aed20-811">*Program.cs* 에서 `Initialize`를 호출하도록 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-811">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="aed20-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aed20-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aed20-813">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="aed20-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aed20-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aed20-815">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="aed20-816">DB 보기</span><span class="sxs-lookup"><span data-stu-id="aed20-816">View the DB</span></span>

<span data-ttu-id="aed20-817">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="aed20-818">따라서 데이터베이스 이름은 “SchoolContext-{GUID}”입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="aed20-819">GUID는 사용자에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="aed20-820">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="aed20-821">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="aed20-822">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="aed20-823">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="aed20-824">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="aed20-824">Asynchronous code</span></span>

<span data-ttu-id="aed20-825">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="aed20-826">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="aed20-827">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="aed20-828">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="aed20-829">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="aed20-830">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="aed20-831">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="aed20-832">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="aed20-833">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="aed20-834">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="aed20-835">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="aed20-836">반환되는 [작업](/dotnet/api/system.threading.tasks.task) 개체를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="aed20-837">자세한 내용은 [작업 반환 형식](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="aed20-838">암시적 반환 형식 `Task`는 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="aed20-839">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="aed20-840">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="aed20-841">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="aed20-842">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="aed20-843">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="aed20-844">쿼리 또는 명령을 DB에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="aed20-845">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="aed20-846">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="aed20-847">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="aed20-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="aed20-848">비동기 코드의 성능 이점을 활용하려면 쿼리를 DB에 보내는 EF Core 메서드를 호출하는 경우 라이브러리 패키지(예: 페이징)가 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="aed20-849">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aed20-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="aed20-850">다음 자습서에서는 기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="aed20-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="aed20-851">추가 자료</span><span class="sxs-lookup"><span data-stu-id="aed20-851">Additional resources</span></span>

* [<span data-ttu-id="aed20-852">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="aed20-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="aed20-853">다음</span><span class="sxs-lookup"><span data-stu-id="aed20-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
