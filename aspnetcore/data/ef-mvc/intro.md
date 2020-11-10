---
title: '자습서: ASP.NET MVC 웹앱에서 EF Core 시작'
description: 이 페이지는 Contoso University 샘플 EF/MVC 앱을 빌드하는 방법을 설명하는 자습서 시리즈 중 첫 번째입니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365390"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="0bb2d-103">자습서: ASP.NET MVC 웹앱에서 EF Core 시작</span><span class="sxs-lookup"><span data-stu-id="0bb2d-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="0bb2d-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0bb2d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="0bb2d-105">Contoso University 샘플 웹앱은 EF(Entity Framework) Core 및 Visual Studio를 사용하여 ASP.NET Core MVC 웹앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="0bb2d-106">샘플 앱은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0bb2d-107">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0bb2d-108">이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0bb2d-109">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="0bb2d-109">Prerequisites</span></span>

* <span data-ttu-id="0bb2d-110">ASP.NET Core MVC를 처음 사용하는 경우 이 자습서를 시작하기 전에 [ASP.NET Core MVC 시작](xref:tutorials/first-mvc-app/start-mvc) 자습서를 마치세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="0bb2d-111">데이터베이스 엔진</span><span class="sxs-lookup"><span data-stu-id="0bb2d-111">Database engines</span></span>

<span data-ttu-id="0bb2d-112">Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="0bb2d-113">문제 해결</span><span class="sxs-lookup"><span data-stu-id="0bb2d-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="0bb2d-114">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="0bb2d-115">일반적인 오류 목록 및 해결 방법은 [시리즈에서 마지막 자습서의 문제 해결 섹션](advanced.md#common-errors)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="0bb2d-116">필요한 내용을 찾지 못한 경우 질문을 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 StackOverflow.com에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="0bb2d-117">10 자습서의 시리즈이며, 각각은 이전 자습서에서 수행된 작업을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="0bb2d-118">각 자습서를 성공적으로 완료한 후에 프로젝트 복사본의 저장을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="0bb2d-119">그런 다음, 문제가 발생한 경우 전체 시리즈의 처음으로 다시 이동하는 대신 이전 자습서부터 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="0bb2d-120">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="0bb2d-120">Contoso University web app</span></span>

<span data-ttu-id="0bb2d-121">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0bb2d-122">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0bb2d-123">다음은 앱의 몇 가지 화면입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-123">Here are a few of the screens in the app:</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="0bb2d-126">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-126">Create web app</span></span>

* <span data-ttu-id="0bb2d-127">Visual Studio를 시작하고 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="0bb2d-128">프로젝트 이름을 `ContosoUniversity`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="0bb2d-129">코드를 복사할 때 네임스페이스가 일치하도록 대문자 표시를 포함하여 이 이름을 정확히 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="0bb2d-130">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-130">Select **Create**.</span></span>
* <span data-ttu-id="0bb2d-131">드롭다운에서 **.NET Core** 및 **ASP.NET Core 5.0** 을 선택한 다음 **웹 애플리케이션(Model-View-Controller)** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="0bb2d-132">![새 ASP.NET Core 프로젝트 대화 상자](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="0bb2d-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="0bb2d-133">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="0bb2d-133">Set up the site style</span></span>

<span data-ttu-id="0bb2d-134">몇 가지 기본적 변경으로 사이트 메뉴, 레이아웃, 홈페이지가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="0bb2d-135">*Views/Shared/_Layout.cshtml* 을 열고 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="0bb2d-136">각 `ContosoUniversity` 항목을 `Contoso University`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="0bb2d-137">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-137">There are three occurrences.</span></span>
* <span data-ttu-id="0bb2d-138">**정보** , **학생** , **강좌** , **강사** 및 **부서** 메뉴 항목을 추가하고 **개인 정보** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="0bb2d-139">위의 변경 내용은 다음 코드에서 강조 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="0bb2d-140">*Views/Home/Index.cshtml* 에서 파일의 내용을 다음 태그로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="0bb2d-141">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="0bb2d-142">이 자습서에서 만든 페이지의 탭과 함께 홈페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso University 홈페이지](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="0bb2d-144">EF Core NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="0bb2d-144">EF Core NuGet packages</span></span>

<span data-ttu-id="0bb2d-145">이 자습서에서는 SQL Server를 사용하며 공급자 패키지는 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="0bb2d-146">EF SQL Server 패키지 및 해당 종속성(`Microsoft.EntityFrameworkCore` 및 `Microsoft.EntityFrameworkCore.Relational`)은 EF에 대한 런타임 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="0bb2d-147">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 패키지와 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="0bb2d-148">PMC(프로그램 관리자 콘솔)에서 다음 명령을 입력하여 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="0bb2d-149">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 패키지는 EF Core 오류 페이지에 대한 ASP.NET Core 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="0bb2d-150">이 미들웨어는 EF Core 마이그레이션의 오류를 검색 및 진단하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="0bb2d-151">EF Core에 사용할 수 있는 다른 데이터베이스 공급자에 대한 정보는 [데이터베이스 공급자](/ef/core/providers/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="0bb2d-152">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-152">Create the data model</span></span>

<span data-ttu-id="0bb2d-153">이 앱을 위해 다음 엔터티 클래스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-153">The following entity classes are created for this app:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="0bb2d-155">위의 엔터티에는 다음과 같은 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="0bb2d-156">`Student` 엔터티와 `Enrollment` 엔터티 간의 일 대 다 관계.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0bb2d-157">학생 한 명이 여러 강좌에 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="0bb2d-158">`Course` 엔터티와 `Enrollment` 엔터티 간의 일 대 다 관계.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0bb2d-159">강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0bb2d-160">다음 섹션에서 이러한 엔터티마다 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0bb2d-161">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="0bb2d-161">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="0bb2d-163">*Models* 폴더에서 다음 코드로 `Student` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-164">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키( **PK** ) 열입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0bb2d-165">기본적으로 EF는 이름이 `ID` 또는 `classnameID`인 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0bb2d-166">예를 들어 PK 이름을 `ID` 대신 `StudentID`로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="0bb2d-167">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0bb2d-168">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0bb2d-169">`Student` 엔터티의 `Enrollments` 속성:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="0bb2d-170">해당 `Student` 엔터티와 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="0bb2d-171">데이터베이스의 특정 `Student` 행에 두 개의 관련 `Enrollment` 행이 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="0bb2d-172">이 `Student` 엔터티의 `Enrollments` 탐색 속성은 이러한 두 개의 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="0bb2d-173">`Enrollment` 행은 `StudentID` 외래 키( **FK** ) 열에 학생의 PK 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="0bb2d-174">탐색 속성 하나에 여러 엔터티가 있을 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="0bb2d-175">형식은 `ICollection<T>`, `List<T>`, `HashSet<T>`과 같은 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="0bb2d-176">엔터티는 추가, 삭제, 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="0bb2d-177">다 대 다 및 일 대 다 탐색 관계는 여러 엔터티를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="0bb2d-178">`ICollection<T>`이 사용되는 경우 EF는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0bb2d-179">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="0bb2d-179">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="0bb2d-181">*Models* 폴더에서 다음 코드로 `Enrollment` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-182">`EnrollmentID` 속성은 PK입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="0bb2d-183">이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0bb2d-184">`Student` 엔터티는 `ID` 패턴을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="0bb2d-185">일부 개발자는 데이터 모델 전체에서 하나의 패턴을 사용하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="0bb2d-186">이 자습서의 변형은 두 패턴을 모두 사용할 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="0bb2d-187">[이후 자습서](inheritance.md)에서는 클래스 이름 없이 `ID`를 사용하여 데이터 모델에서 상속을 더 쉽게 구현하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0bb2d-188">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0bb2d-189">`Grade` 형식 선언 뒤에 있는 `?`는 `Grade` 속성이 [null 허용](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="0bb2d-190">`null`인 등급은 0 등급과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="0bb2d-191">`null`은 등급이 알려지지 않았거나 아직 할당되지 않았음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0bb2d-192">`StudentID` 속성은 외래 키이며, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0bb2d-193">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티만 보유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="0bb2d-194">이것은 여러 `Enrollment` 엔터티를 보유할 수 있는 `Student.Enrollments` 탐색 속성과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0bb2d-195">`CourseID` 속성은 FK이며, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0bb2d-196">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0bb2d-197">Entity Framework는 이름이 `<`탐색 속성 이름`><`기본 키 속성 이름`>`으로 지정된 속성을 FK 속성으로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="0bb2d-198">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 PK가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="0bb2d-199">FK 속성의 이름도 `<`기본 키 속성 이름`>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="0bb2d-200">예를 들어 `Course` 엔터티의 PK가 `CourseID`이므로 `CourseID`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0bb2d-201">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="0bb2d-201">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="0bb2d-203">*Models* 폴더에서 다음 코드로 `Course` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-204">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0bb2d-205">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0bb2d-206">[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) 특성은 [이후 자습서](complex-data-model.md)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="0bb2d-207">이 특성을 사용하면 데이터베이스가 특성을 생성하도록 하는 대신 강좌의 PK를 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="0bb2d-208">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-208">Create the database context</span></span>

<span data-ttu-id="0bb2d-209">지정된 데이터 모델의 EF 기능을 조정하는 주 클래스는 <xref:Microsoft.EntityFrameworkCore.DbContext> 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="0bb2d-210">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="0bb2d-211">`DbContext` 파생 클래스는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="0bb2d-212">일부 EF 동작은 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="0bb2d-213">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0bb2d-214">프로젝트 폴더에서 `Data`라는 이름의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="0bb2d-215">*Data* 폴더에서 다음 코드로 `SchoolContext` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-216">앞의 코드는 엔터티 집합마다 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="0bb2d-217">EF 용어에서</span><span class="sxs-lookup"><span data-stu-id="0bb2d-217">In EF terminology:</span></span>

* <span data-ttu-id="0bb2d-218">엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="0bb2d-219">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0bb2d-220">`DbSet<Enrollment>` 및 `DbSet<Course>` 문을 생략할 수 있으며 동작은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="0bb2d-221">EF가 이러한 문을 암시적으로 포함하는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="0bb2d-222">`Student` 엔터티는 `Enrollment` 엔터티를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="0bb2d-223">`Enrollment` 엔터티는 `Course` 엔터티를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="0bb2d-224">데이터베이스가 만들어지면 EF는 `DbSet` 속성 이름과 동일한 이름을 갖는 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="0bb2d-225">컬렉션의 속성 이름은 일반적으로 복수입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="0bb2d-226">예를 들어 `Student`가 아니라 `Students`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="0bb2d-227">개발자는 테이블 이름을 복수화할지 여부에 대해 동의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="0bb2d-228">이러한 자습서에서는 `DbContext`에서 단수 테이블 이름을 지정하여 기본 동작이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="0bb2d-229">이렇게 하려면 마지막 DbSet 속성 뒤에 강조 표시된 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="0bb2d-230">`SchoolContext`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="0bb2d-231">ASP.NET Core는 [종속성 주입](../../fundamentals/dependency-injection.md)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="0bb2d-232">EF 데이터베이스 컨텍스트와 같은 서비스는 앱 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="0bb2d-233">MVC 컨트롤러와 같이 이러한 서비스가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0bb2d-234">컨텍스트 인스턴스를 가져오는 컨트롤러 생성자 코드는 이 자습서의 뒷부분에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="0bb2d-235">`SchoolContext`를 서비스로 등록하려면 *Startup.cs* 를 열고 강조 표시된 줄을 `ConfigureServices` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="0bb2d-236">`DbContextOptionsBuilder` 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="0bb2d-237">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="0bb2d-238">*appsettings.json* 파일을 열고 다음 표시에 나온 것처럼 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-238">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="0bb2d-239">데이터베이스 예외 필터 추가</span><span class="sxs-lookup"><span data-stu-id="0bb2d-239">Add the database exception filter</span></span>

<span data-ttu-id="0bb2d-240">다음 코드와 같이 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A>를 `ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="0bb2d-241">`AddDatabaseDeveloperPageExceptionFilter`는 [개발 환경](xref:fundamentals/environments)에서 유용한 오류 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0bb2d-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0bb2d-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0bb2d-243">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0bb2d-244">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0bb2d-245">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0bb2d-246">기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="0bb2d-247">테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="0bb2d-247">Initialize DB with test data</span></span>

<span data-ttu-id="0bb2d-248">EF는 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-248">EF creates an empty database.</span></span> <span data-ttu-id="0bb2d-249">이 섹션에서는 테스트 데이터로 채우기 위해 데이터베이스를 만든 후 호출되는 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="0bb2d-250">`EnsureCreated` 메서드는 자동으로 데이터베이스를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="0bb2d-251">[이후의 자습서](migrations.md)에서는 데이터베이스를 삭제하고 다시 만드는 대신 Code First 마이그레이션을 사용하여 데이터베이스 스키마를 변경함으로써 모델 변경 내용을 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="0bb2d-252">*Data* 폴더에서 다음 코드로 `DbInitializer`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="0bb2d-253">위의 코드는 데이터베이스가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="0bb2d-254">데이터베이스를 찾을 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="0bb2d-254">If the database is not found;</span></span>
  * <span data-ttu-id="0bb2d-255">테스트 데이터를 사용하여 생성 및 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-255">It is created and loaded with test data.</span></span> <span data-ttu-id="0bb2d-256">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="0bb2d-257">데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="0bb2d-258">다음 코드로 *Program.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="0bb2d-259">*Program.cs* 는 앱 시작 시 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="0bb2d-260">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0bb2d-261">`DbInitializer.Initialize` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="0bb2d-262">다음 코드에 나온 것처럼 `Initialize` 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="0bb2d-263">처음으로 앱이 실행되고 데이터베이스가 생성되며 테스트 데이터로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="0bb2d-264">데이터 모델이 변경될 때마다</span><span class="sxs-lookup"><span data-stu-id="0bb2d-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="0bb2d-265">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-265">Delete the database.</span></span>
* <span data-ttu-id="0bb2d-266">시드 메서드를 업데이트하고 새 데이터베이스로 새로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="0bb2d-267">이후 자습서에서는 데이터 모델이 변경될 때 데이터베이스를 삭제 후 다시 만들지 않고 데이터베이스가 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="0bb2d-268">데이터 모델이 변경되어도 데이터는 손실되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="0bb2d-269">컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-269">Create controller and views</span></span>

<span data-ttu-id="0bb2d-270">Visual Studio에서 스캐폴딩 엔진을 사용하여 EF에서 데이터를 쿼리하고 저장하는 데 사용하는 MVC 컨트롤러 및 보기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="0bb2d-271">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 작업 메서드와 보기를 자동으로 만드는 작업을 스캐폴딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="0bb2d-272">**솔루션 탐색기** 에서 `Controllers` 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="0bb2d-273">**스캐폴드 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="0bb2d-274">**Entity Framework를 사용하며 뷰가 포함된 MVC 컨트롤러** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="0bb2d-275">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-275">Click **Add**.</span></span> <span data-ttu-id="0bb2d-276">**Entity Framework를 사용하여 뷰 포함 MVC 컨트롤러 추가** 대화 상자가 표시됩니다. ![학생 스캐폴드](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="0bb2d-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="0bb2d-277">**모델 클래스** 에서 **Student** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="0bb2d-278">**데이터 컨텍스트 클래스** 에서 **SchoolContext** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="0bb2d-279">기본값 **StudentsController** 를 이름으로 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="0bb2d-280">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-280">Click **Add**.</span></span>

<span data-ttu-id="0bb2d-281">Visual Studio 스캐폴딩 엔진이 `StudentsController.cs` 파일 및 컨트롤러와 함께 작동하는 뷰 집합(`*.cshtml` 파일)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="0bb2d-282">컨트롤러는 `SchoolContext`를 생성자 매개 변수로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="0bb2d-283">ASP.NET Core 종속성 주입은 `SchoolContext`의 인스턴스를 컨트롤러에 전달하는 것을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="0bb2d-284">이 인스턴스는 `Startup` 클래스에서 구성했습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="0bb2d-285">컨트롤러는 데이터베이스에 모든 학생을 표시하는 `Index` 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="0bb2d-286">메서드는 데이터베이스 컨텍스트 인스턴스의 `Students` 속성을 읽어 학생 엔터티 집합에서 학생의 목록을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="0bb2d-287">이 코드의 비동기 프로그래밍 요소는 이 자습서 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="0bb2d-288">*Views/Students/Index.cshtml* 보기가 테이블에 이 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="0bb2d-289">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="0bb2d-290">학생 탭을 클릭하여 `DbInitializer.Initialize` 메서드가 삽입된 테스트 데이터를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="0bb2d-291">브라우저 창의 폭에 따라 페이지의 맨 위에 `Students` 탭 링크가 표시되거나 링크를 보기 위해 오른쪽 맨 위의 탐색 아이콘을 클릭해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![좁은 Contoso University 홈페이지](intro/_static/home-page-narrow.png)

![학생 인덱스 페이지](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="0bb2d-294">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-294">View the database</span></span>

<span data-ttu-id="0bb2d-295">앱이 시작되면 `DbInitializer.Initialize` 메서드가 `EnsureCreated`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="0bb2d-296">EF에서 데이터베이스가 없음을 확인했습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="0bb2d-297">따라서 데이터베이스를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-297">So it created a database.</span></span>
* <span data-ttu-id="0bb2d-298">`Initialize` 메서드 코드가 데이터베이스를 데이터로 채웠습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="0bb2d-299">**SQL Server 개체 탐색기** (SSOX)를 사용하여 Visual Studio에서 데이터베이스를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="0bb2d-300">Visual Studio의 **보기** 메뉴에서 **SQL Server 개체 탐색기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0bb2d-301">SSOX에서 **(localdb)\MSSQLLocalDB > Databases** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="0bb2d-302">*appsettings.json* 파일의 연결 문자열에 있는 데이터베이스 이름의 항목인 `ContosoUniversity1`를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="0bb2d-303">**테이블** 노드를 확장하여 데이터베이스의 테이블을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-303">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX의 테이블](intro/_static/ssox-tables.png)

<span data-ttu-id="0bb2d-305">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 클릭하여 테이블의 데이터를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX의 학생 테이블](intro/_static/ssox-student-table.png)

<span data-ttu-id="0bb2d-307">`*.mdf` 및 `*.ldf` 데이터베이스 파일은 *C:\Users\\\<username>* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="0bb2d-308">앱 시작 시 실행되는 이니셜라이저 메서드에서 `EnsureCreated`가 호출되기 때문에 다음과 같은 작업을 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="0bb2d-309">`Student` 클래스를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="0bb2d-310">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-310">Delete the database.</span></span>
* <span data-ttu-id="0bb2d-311">앱을 중지했다가 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-311">Stop, then start the app.</span></span> <span data-ttu-id="0bb2d-312">데이터베이스는 변경 내용에 맞게 자동으로 다시 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="0bb2d-313">예를 들어 `EmailAddress` 속성을 `Student` 클래스에 추가하면 다시 만들어진 테이블에 새 `EmailAddress` 열이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="0bb2d-314">분류 보기에는 새 `EmailAddress` 속성이 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="0bb2d-315">규칙</span><span class="sxs-lookup"><span data-stu-id="0bb2d-315">Conventions</span></span>

<span data-ttu-id="0bb2d-316">EF가 사용하는 규칙 때문에 EF가 완전한 데이터베이스를 만들기 위해 쓰는 코드의 양은 최소입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="0bb2d-317">`DbSet` 속성의 이름은 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="0bb2d-318">`DbSet` 속성에서 참조하지 않는 엔터티의 경우 엔터티 클래스 이름이 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="0bb2d-319">엔터티 속성 이름은 열 이름에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="0bb2d-320">이름이 `ID` 또는 `classnameID`인 엔터티 속성은 PK 속성으로 인식됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="0bb2d-321">이름이 `<`탐색 속성 이름`><`PK 속성 이름`>`으로 지정된 속성은 FK 속성으로 해석됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="0bb2d-322">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 PK가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="0bb2d-323">FK 속성의 이름도 `<`기본 키 속성 이름`>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="0bb2d-324">예를 들어 `Enrollment` 엔터티의 PK가 `EnrollmentID`이므로 `EnrollmentID`로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="0bb2d-325">기본 동작은 재정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="0bb2d-326">예를 들어 이 자습서의 앞부분에 나온 것처럼 테이블 이름을 명시적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="0bb2d-327">열 이름과 속성을 PK 또는 FK로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0bb2d-328">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="0bb2d-328">Asynchronous code</span></span>

<span data-ttu-id="0bb2d-329">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0bb2d-330">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0bb2d-331">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0bb2d-332">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0bb2d-333">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0bb2d-334">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0bb2d-335">비동기 코드는 런타임 시 약간의 오버헤드를 도입하지만 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0bb2d-336">다음 코드에서 `async`, `Task<T>`, `await`, `ToListAsync`는 코드가 비동기적으로 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0bb2d-337">`async` 키워드는 컴파일러에서 메서드 본문의 부분에 대한 콜백을 생성하고 반환되는 `Task<IActionResult>` 개체를 자동으로 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="0bb2d-338">반환 형식 `Task<IActionResult>`는 `IActionResult` 형식의 결과와 함께 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="0bb2d-339">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0bb2d-340">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0bb2d-341">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0bb2d-342">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0bb2d-343">EF를 사용하는 비동기 코드를 작성할 때 주의 사항:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="0bb2d-344">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0bb2d-345">예를 들어 `ToListAsync`, `SingleOrDefaultAsync` 및 `SaveChangesAsync`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0bb2d-346">예를 들어 `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0bb2d-347">EF 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="0bb2d-348">비동기 EF 메서드를 호출하는 경우 항상 `await` 키워드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="0bb2d-349">비동기 코드의 성능 이점을 활용하기 위해서는 사용되는 라이브러리 패키지 또한 쿼리가 데이터베이스로 전송되도록 하는 EF 메서드를 호출하는 경우 비동기를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="0bb2d-350">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/articles/standard/async)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="0bb2d-351">가져오는 엔터티 제한</span><span class="sxs-lookup"><span data-stu-id="0bb2d-351">Limit entities fetched</span></span>

<span data-ttu-id="0bb2d-352">쿼리에서 반환되는 항목 수를 제한하는 방법에 대한 자세한 내용은 [성능 고려 사항](xref:data/ef-rp/intro)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="0bb2d-353">기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 수행하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0bb2d-354">기본 CRUD 기능 구현</span><span class="sxs-lookup"><span data-stu-id="0bb2d-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="0bb2d-355">Contoso University 샘플 웹 애플리케이션은 EF(Entity Framework) Core 2.2 및 Visual Studio 2017 또는 2019를 사용하여 ASP.NET Core 2.2 MVC 웹 애플리케이션을 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="0bb2d-356">샘플 애플리케이션은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0bb2d-357">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0bb2d-358">이는 Contoso University 샘플 애플리케이션을 처음부터 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0bb2d-359">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="0bb2d-359">Prerequisites</span></span>

* [<span data-ttu-id="0bb2d-360">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0bb2d-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="0bb2d-361">다른 워크로드를 포함한 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019):</span><span class="sxs-lookup"><span data-stu-id="0bb2d-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="0bb2d-362">**ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="0bb2d-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="0bb2d-363">**.NET Core 플랫폼 간 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="0bb2d-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0bb2d-364">문제 해결</span><span class="sxs-lookup"><span data-stu-id="0bb2d-364">Troubleshooting</span></span>

<span data-ttu-id="0bb2d-365">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="0bb2d-366">일반적인 오류 목록 및 해결 방법은 [시리즈에서 마지막 자습서의 문제 해결 섹션](advanced.md#common-errors)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="0bb2d-367">필요한 내용을 찾지 못한 경우 질문을 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 StackOverflow.com에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="0bb2d-368">10 자습서의 시리즈이며, 각각은 이전 자습서에서 수행된 작업을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="0bb2d-369">각 자습서를 성공적으로 완료한 후에 프로젝트 복사본의 저장을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="0bb2d-370">그런 다음, 문제가 발생한 경우 전체 시리즈의 처음으로 다시 이동하는 대신 이전 자습서부터 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="0bb2d-371">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="0bb2d-371">Contoso University web app</span></span>

<span data-ttu-id="0bb2d-372">이 자습서에서 빌드하는 애플리케이션은 간단한 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="0bb2d-373">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0bb2d-374">다음은 만들 몇 가지 화면입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-374">Here are a few of the screens you'll create.</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="0bb2d-377">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-377">Create web app</span></span>

* <span data-ttu-id="0bb2d-378">Visual Studio를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-378">Open Visual Studio.</span></span>

* <span data-ttu-id="0bb2d-379">**파일** 메뉴에서 **새로 만들기 > 프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="0bb2d-380">왼쪽 창에서 **설치됨 > Visual C# > 웹** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="0bb2d-381">**ASP.NET Core 웹 애플리케이션** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="0bb2d-382">이름으로 **ContosoUniversity** 를 입력하고 **확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![새 프로젝트 대화 상자](intro/_static/new-project2.png)

* <span data-ttu-id="0bb2d-384">**새 ASP.NET Core 웹 애플리케이션** 대화 상자가 표시될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="0bb2d-385">**.NET Core** , **ASP.NET Core 2.2** 및 **웹 애플리케이션(Model-View-Controller)** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="0bb2d-386">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="0bb2d-387">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-387">Select **OK**</span></span>

  ![새 ASP.NET Core 프로젝트 대화 상자](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="0bb2d-389">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="0bb2d-389">Set up the site style</span></span>

<span data-ttu-id="0bb2d-390">몇 가지 간단한 변경 내용으로 사이트 메뉴, 레이아웃 및 홈 페이지를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="0bb2d-391">*Views/Shared/_Layout.cshtml* 을 열고 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="0bb2d-392">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0bb2d-393">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-393">There are three occurrences.</span></span>

* <span data-ttu-id="0bb2d-394">**정보** , **학생** , **강좌** , **강사** 및 **부서** 메뉴 항목을 추가하고 **개인 정보** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="0bb2d-395">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="0bb2d-396">*Views/Home/Index.cshtml* 에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 애플리케이션에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="0bb2d-397">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="0bb2d-398">이 자습서에서 만드는 페이지에 대한 탭이 포함된 홈페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University 홈페이지](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="0bb2d-400">EF Core NuGet 패키지 정보</span><span class="sxs-lookup"><span data-stu-id="0bb2d-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="0bb2d-401">프로젝트에 EF Core 지원을 추가하려면 대상으로 지정하려는 데이터베이스 공급자를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="0bb2d-402">이 자습서에서는 SQL Server를 사용하며 공급자 패키지는 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="0bb2d-403">이 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있으므로 패키지를 참조할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="0bb2d-404">EF SQL Server 패키지 및 해당 종속성(`Microsoft.EntityFrameworkCore` 및 `Microsoft.EntityFrameworkCore.Relational`)은 EF에 대한 런타임 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="0bb2d-405">[마이그레이션](migrations.md) 자습서에서 나중에 도구 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="0bb2d-406">Entity Framework Core에 사용할 수 있는 다른 데이터베이스 공급자에 대한 정보는 [데이터베이스 공급자](/ef/core/providers/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="0bb2d-407">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-407">Create the data model</span></span>

<span data-ttu-id="0bb2d-408">다음으로 Contoso University 애플리케이션에 대한 엔터티 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="0bb2d-409">다음과 같은 세 가지 엔터티로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-409">You'll start with the following three entities.</span></span>

![강좌-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="0bb2d-411">`Student` 및 `Enrollment` 엔터티 간에 일대다 관계가 있으며 `Course` 및 `Enrollment` 엔터티 간에 일대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0bb2d-412">즉, 학생은 개수에 관계 없이 강좌에 등록될 수 있으며 강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0bb2d-413">다음 섹션에서 이러한 엔터티 각각에 대한 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0bb2d-414">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="0bb2d-414">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="0bb2d-416">*Models* 폴더에서 *Student.cs* 라는 클래스 파일을 만들고 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-417">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0bb2d-418">기본적으로 Entity Framework는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="0bb2d-419">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0bb2d-420">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0bb2d-421">이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student` 엔터티에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="0bb2d-422">즉, 데이터베이스의 `Student` 행에 두 개의 관련된 `Enrollment` 행(해당 StudentID 외래 키 열에 해당 학생의 기본 키 값을 포함하는 행)이 있는 경우 해당 `Student` 엔터티의 `Enrollments` 탐색 속성은 두 개의 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="0bb2d-423">탐색 속성이 여러 엔터티를 포함할 수 있는 경우(다대다 또는 일대다 관계로), 해당 형식은 `ICollection<T>`와 같이 항목이 추가, 삭제 및 업데이트될 수 있는 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="0bb2d-424">`List<T>` 또는 `HashSet<T>`와 같은 형식 또는 `ICollection<T>`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0bb2d-425">`ICollection<T>`를 지정하는 경우 EF는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0bb2d-426">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="0bb2d-426">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="0bb2d-428">*Models* 폴더에서 *Enrollment.cs* 를 만들고 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-429">`EnrollmentID` 속성은 기본 키가 됩니다. 이 엔터티는 `Student` 엔터티에서 본 것과 같이 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="0bb2d-430">일반적으로 하나의 패턴을 선택하고 이를 데이터 모델 전체에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="0bb2d-431">여기에서 변형은 패턴 중 하나를 사용할 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="0bb2d-432">[자습서의 뒷부분](inheritance.md)에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0bb2d-433">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0bb2d-434">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0bb2d-435">Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0bb2d-436">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0bb2d-437">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되어 있으므로 속성은 단일 `Student` 엔터티만 포함할 수 있습니다(이전에 살펴본 여러 `Enrollment` 엔터티를 포함할 수 있는 `Student.Enrollments` 탐색 속성과 달리).</span><span class="sxs-lookup"><span data-stu-id="0bb2d-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="0bb2d-438">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0bb2d-439">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0bb2d-440">Entity Framework는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키 속성으로는 해석합니다(예: `Student` 엔터티의 기본 키가 `ID`이므로 `Student` 탐색 속성의 경우 `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="0bb2d-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="0bb2d-441">외래 키 속성의 이름을 단순히 `<primary key property name>`으로 지정할 수 있습니다(예: `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="0bb2d-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0bb2d-442">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="0bb2d-442">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="0bb2d-444">*Models* 폴더에서 *Course.cs* 를 만들고 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-445">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0bb2d-446">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0bb2d-447">이 시리즈의 [이후의 자습서](complex-data-model.md)에서 `DatabaseGenerated` 특성에 대해 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="0bb2d-448">기본적으로 이 특성을 통해 생성하는 데이터베이스를 갖는 대신 강좌에 대한 기본 키를 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="0bb2d-449">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-449">Create the database context</span></span>

<span data-ttu-id="0bb2d-450">특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0bb2d-451">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="0bb2d-452">코드에서 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="0bb2d-453">특정 Entity Framework 동작을 사용자 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="0bb2d-454">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0bb2d-455">프로젝트 폴더에서 *Data* 라는 이름의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="0bb2d-456">*Data* 폴더에서 *SchoolContext.cs* 라는 새로운 클래스 파일을 만들고 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-457">이 코드는 각 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="0bb2d-458">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0bb2d-459">`DbSet<Enrollment>` 및 `DbSet<Course>` 문을 생략할 수 있으며 이는 동일하게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="0bb2d-460">`Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 Entity Framework는 이를 암시적으로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="0bb2d-461">데이터베이스가 만들어지면 EF는 `DbSet` 속성 이름과 동일한 이름을 갖는 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="0bb2d-462">컬렉션에 대한 속성 이름은 일반적으로 복수형이지만(Student보다는 Students) 개발자는 테이블 이름을 복수화할지 여부에 대해 동의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="0bb2d-463">이러한 자습서의 경우 DbContext에서 단수 테이블 이름을 지정하여 기본 동작을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="0bb2d-464">이렇게 하려면 마지막 DbSet 속성 뒤에 강조 표시된 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="0bb2d-465">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="0bb2d-466">SchoolContext 등록</span><span class="sxs-lookup"><span data-stu-id="0bb2d-466">Register the SchoolContext</span></span>

<span data-ttu-id="0bb2d-467">ASP.NET Core는 기본적으로 [종속성 주입](../../fundamentals/dependency-injection.md)을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="0bb2d-468">서비스(예: EF 데이터베이스 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0bb2d-469">이러한 서비스(예: MVC 컨트롤러)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0bb2d-470">이 자습서의 뒷부분에서 컨텍스트 인스턴스를 가져오는 컨트롤러 생성자 코드를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="0bb2d-471">`SchoolContext`를 서비스로 등록하려면 *Startup.cs* 를 열고 강조 표시된 줄을 `ConfigureServices` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="0bb2d-472">`DbContextOptionsBuilder` 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="0bb2d-473">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="0bb2d-474">`ContosoUniversity.Data` 및 `Microsoft.EntityFrameworkCore` 네임스페이스에 대해 `using` 문을 추가한 다음, 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="0bb2d-475">*appsettings.json* 파일을 열고 다음 예제에 나온 것처럼 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-475">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0bb2d-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0bb2d-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0bb2d-477">연결 문자열은 SQL Server LocalDB 데이터베이스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="0bb2d-478">LocalDB는 프로덕션 사용이 아닌 애플리케이션 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="0bb2d-479">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0bb2d-480">기본적으로 LocalDB는 *.mdf* 데이터베이스 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="0bb2d-481">테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="0bb2d-481">Initialize DB with test data</span></span>

<span data-ttu-id="0bb2d-482">Entity Framework에서 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="0bb2d-483">이 섹션에서는 테스트 데이터로 채우기 위해 데이터베이스를 만든 후 호출되는 메서드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="0bb2d-484">여기에서 `EnsureCreated` 메서드를 사용하여 자동으로 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="0bb2d-485">[이후의 자습서](migrations.md)에서는 데이터베이스를 삭제하고 다시 작성하는 대신 데이터베이스 스키마를 변경하도록 Code First 마이그레이션을 사용하여 모델 변경 내용을 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="0bb2d-486">*Data* 폴더에서 *DbInitializer.cs* 라는 새 클래스 파일을 만들고 템플릿 코드를 다음 코드로 바꿉니다. 이로 인해 필요할 때 데이터베이스가 만들어지며 테스트 데이터를 새 데이터베이스로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0bb2d-487">코드는 데이터베이스에 학생이 있는지를 확인하고 없는 경우 데이터베이스가 새 것이며 테스트 데이터로 시드되어야 한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="0bb2d-488">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0bb2d-489">*Program.cs* 에서 애플리케이션 시작 시에 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="0bb2d-490">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0bb2d-491">컨텍스트를 전달하는 시드 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="0bb2d-492">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0bb2d-493">애플리케이션을 처음으로 실행하면 데이터베이스가 생성되고 테스트 데이터로 시드됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="0bb2d-494">데이터 모델을 변경할 때마다:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="0bb2d-495">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-495">Delete the database.</span></span>
 * <span data-ttu-id="0bb2d-496">시드 메서드를 업데이트하고 같은 방법으로 새 데이터베이스로 새로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="0bb2d-497">이후의 자습서에서는 데이터 모델이 변경될 때 데이터베이스를 삭제하고 다시 작성하지 않고 데이터베이스를 수정하는 방법을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="0bb2d-498">컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-498">Create controller and views</span></span>

<span data-ttu-id="0bb2d-499">이 섹션에서는 Visual Studio의 스캐폴딩 엔진을 사용하여 데이터 쿼리와 저장에 EF를 사용하는 MVC 컨트롤러 및 뷰를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="0bb2d-500">CRUD 작업 메서드와 보기를 자동으로 만드는 작업을 스캐폴딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="0bb2d-501">스캐폴딩은 사용자 고유의 요구 사항에 맞게 수정할 수 있는 스캐폴드된 코드가 시작 지점인 코드 생성과 다릅니다. 반면에 일반적으로 생성된 코드를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="0bb2d-502">생성된 코드를 사용자 지정해야 하는 경우 partial 클래스를 사용하거나 항목이 변경될 때 코드를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="0bb2d-503">**솔루션 탐색기** 에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 -> 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="0bb2d-504">**스캐폴드 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="0bb2d-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="0bb2d-505">**Entity Framework를 사용하며 뷰가 포함된 MVC 컨트롤러** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="0bb2d-506">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-506">Click **Add**.</span></span> <span data-ttu-id="0bb2d-507">**Entity Framework를 사용하여 뷰 포함 MVC 컨트롤러 추가** 대화 상자가 표시됩니다. ![학생 스캐폴드](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="0bb2d-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="0bb2d-508">**모델 클래스** 에서 **학생** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="0bb2d-509">**데이터 컨텍스트 클래스** 에서 **SchoolContext** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="0bb2d-510">기본값 **StudentsController** 를 이름으로 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="0bb2d-511">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-511">Click **Add**.</span></span>

<span data-ttu-id="0bb2d-512">Visual Studio 스캐폴딩 엔진은 *StudentsController.cs* 파일 및 컨트롤러와 함께 작동하는 뷰 집합( *.cshtml* 파일)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="0bb2d-513">컨트롤러는 `SchoolContext`를 생성자 매개 변수로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="0bb2d-514">ASP.NET Core 종속성 주입은 `SchoolContext`의 인스턴스를 컨트롤러에 전달하는 것을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="0bb2d-515">이것은 *Startup.cs* 파일에서 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="0bb2d-516">컨트롤러는 데이터베이스에 모든 학생을 표시하는 `Index` 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="0bb2d-517">메서드는 데이터베이스 컨텍스트 인스턴스의 `Students` 속성을 읽어 학생 엔터티 집합에서 학생의 목록을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="0bb2d-518">이 코드의 비동기 프로그래밍 요소에 대해서는 이 자습서 뒷부분에서 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="0bb2d-519">*Views/Students/Index.cshtml* 보기가 테이블에 이 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="0bb2d-520">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="0bb2d-521">학생 탭을 클릭하여 `DbInitializer.Initialize` 메서드가 삽입된 테스트 데이터를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="0bb2d-522">브라우저 창의 폭에 따라 페이지의 맨 위에 `Students` 탭 링크가 표시되거나 링크를 보기 위해 오른쪽 맨 위의 탐색 아이콘을 클릭해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![좁은 Contoso University 홈페이지](intro/_static/home-page-narrow.png)

![학생 인덱스 페이지](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="0bb2d-525">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="0bb2d-525">View the database</span></span>

<span data-ttu-id="0bb2d-526">애플리케이션을 시작했을 때 `DbInitializer.Initialize` 메서드는 `EnsureCreated`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="0bb2d-527">EF는 데이터베이스가 없는 것을 확인하고 하나를 만들었습니다. 그런 다음, `Initialize` 메서드 코드의 나머지 부분은 데이터베이스를 데이터로 채웠습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="0bb2d-528">**SQL Server 개체 탐색기** (SSOX)를 사용하여 Visual Studio에서 데이터베이스를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="0bb2d-529">브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-529">Close the browser.</span></span>

<span data-ttu-id="0bb2d-530">SSOX 창이 열려 있지 않은 경우 Visual Studio의 **보기** 메뉴에서 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="0bb2d-531">SSOX에서 **(localdb)\MSSQLLocalDB > Databases** 를 클릭한 다음 *appsettings.json* 파일의 연결 문자열에 있는 데이터베이스 이름 항목을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="0bb2d-532">**테이블** 노드를 확장하여 데이터베이스의 테이블을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-532">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX의 테이블](intro/_static/ssox-tables.png)

<span data-ttu-id="0bb2d-534">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만들어진 열 및 테이블에 삽입된 행을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX의 학생 테이블](intro/_static/ssox-student-table.png)

<span data-ttu-id="0bb2d-536">*.mdf* 및 *.ldf* 데이터베이스 파일은 *C:\Users\\\<username>* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="0bb2d-537">앱 시작 시 실행되는 이니셜라이저 메서드에서 `EnsureCreated`를 호출하기 때문에 이제 `Student` 클래스에 변경 내용을 만들고, 데이터베이스를 삭제하고, 애플리케이션을 다시 시작할 수 있으며, 데이터베이스는 변경 내용에 맞도록 자동으로 다시 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="0bb2d-538">예를 들어 `EmailAddress` 속성을 `Student` 클래스에 추가하는 경우 다시 만들어진 테이블에 새 `EmailAddress` 열이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="0bb2d-539">규칙</span><span class="sxs-lookup"><span data-stu-id="0bb2d-539">Conventions</span></span>

<span data-ttu-id="0bb2d-540">Entity Framework에서 전체 데이터베이스를 만들 수 있도록 작성해야 했던 코드의 양은 규칙의 사용 또는 Entity Framework에서 만드는 가정으로 인해 최소입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="0bb2d-541">`DbSet` 속성의 이름은 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="0bb2d-542">`DbSet` 속성에서 참조하지 않는 엔터티의 경우 엔터티 클래스 이름이 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="0bb2d-543">엔터티 속성 이름은 열 이름에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="0bb2d-544">ID 또는 classnameID로 명명된 엔터티 속성은 기본 키 속성으로 인식됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="0bb2d-545">속성 이름이 *\<navigation property name>\<primary key property name>* 인 경우 외래 키 속성으로는 해석됩니다(예: `Student` 엔터티의 기본 키가 `ID`이므로 `Student` 탐색 속성의 경우 `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="0bb2d-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="0bb2d-546">외래 키 속성의 이름을 단순히 *\<primary key property name>* 으로 지정할 수 있습니다(예: `Enrollment` 엔터티의 기본 키가 `EnrollmentID`이므로 `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="0bb2d-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="0bb2d-547">기본 동작은 재정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="0bb2d-548">예를 들어 이 자습서의 앞부분에서 본 것처럼 테이블 이름을 명시적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="0bb2d-549">또한 이 시리즈의 [이후의 자습서](complex-data-model.md)에서 볼 수 있듯이 열 이름을 설정하고 기본 키 또는 외래 키로 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0bb2d-550">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="0bb2d-550">Asynchronous code</span></span>

<span data-ttu-id="0bb2d-551">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0bb2d-552">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0bb2d-553">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0bb2d-554">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0bb2d-555">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0bb2d-556">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0bb2d-557">비동기 코드는 런타임 시 약간의 오버헤드를 도입하지만 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0bb2d-558">다음 코드에서 `async` 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0bb2d-559">`async` 키워드는 컴파일러에서 메서드 본문의 부분에 대한 콜백을 생성하고 반환되는 `Task<IActionResult>` 개체를 자동으로 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="0bb2d-560">반환 형식 `Task<IActionResult>`는 `IActionResult` 형식의 결과와 함께 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="0bb2d-561">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0bb2d-562">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0bb2d-563">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0bb2d-564">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0bb2d-565">Entity Framework를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="0bb2d-566">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0bb2d-567">예를 들어 `ToListAsync`, `SingleOrDefaultAsync` 및 `SaveChangesAsync`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0bb2d-568">예를 들어 `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0bb2d-569">EF 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="0bb2d-570">비동기 EF 메서드를 호출하는 경우 항상 `await` 키워드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="0bb2d-571">비동기 코드의 성능 이점을 활용하려는 경우 사용 중인(예: 페이징) 라이브러리 패키지 또한 쿼리를 데이터베이스에 전송하도록 하는 Entity Framework 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="0bb2d-572">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/articles/standard/async)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0bb2d-573">다음 단계</span><span class="sxs-lookup"><span data-stu-id="0bb2d-573">Next steps</span></span>

<span data-ttu-id="0bb2d-574">기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 수행하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="0bb2d-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0bb2d-575">기본 CRUD 기능 구현</span><span class="sxs-lookup"><span data-stu-id="0bb2d-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
