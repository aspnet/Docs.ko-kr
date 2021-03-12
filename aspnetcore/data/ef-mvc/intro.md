---
title: '자습서: ASP.NET MVC 웹앱에서 EF Core 시작'
description: 이 페이지는 Contoso University 샘플 EF/MVC 앱을 빌드하는 방법을 설명하는 자습서 시리즈 중 첫 번째입니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 5bb1a05f5e81730d0785c1d6a69a8cafb68f85ab
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586685"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="b16fb-103">자습서: ASP.NET MVC 웹앱에서 EF Core 시작</span><span class="sxs-lookup"><span data-stu-id="b16fb-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="b16fb-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b16fb-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="b16fb-105">Contoso University 샘플 웹앱은 EF(Entity Framework) Core 및 Visual Studio를 사용하여 ASP.NET Core MVC 웹앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-105">The Contoso University sample web app demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="b16fb-106">샘플 앱은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b16fb-107">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b16fb-108">이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b16fb-109">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b16fb-109">Prerequisites</span></span>

* <span data-ttu-id="b16fb-110">ASP.NET Core MVC를 처음 사용하는 경우 이 자습서를 시작하기 전에 [ASP.NET Core MVC 시작](xref:tutorials/first-mvc-app/start-mvc) 자습서를 마치세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="b16fb-111">데이터베이스 엔진</span><span class="sxs-lookup"><span data-stu-id="b16fb-111">Database engines</span></span>

<span data-ttu-id="b16fb-112">Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="b16fb-113">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b16fb-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="b16fb-114">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="b16fb-115">일반적인 오류 목록 및 해결 방법은 [시리즈에서 마지막 자습서의 문제 해결 섹션](advanced.md#common-errors)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="b16fb-116">필요한 내용을 찾지 못한 경우 질문을 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 StackOverflow.com에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="b16fb-117">10 자습서의 시리즈이며, 각각은 이전 자습서에서 수행된 작업을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="b16fb-118">각 자습서를 성공적으로 완료한 후에 프로젝트 복사본의 저장을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="b16fb-119">그런 다음, 문제가 발생한 경우 전체 시리즈의 처음으로 다시 이동하는 대신 이전 자습서부터 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="b16fb-120">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="b16fb-120">Contoso University web app</span></span>

<span data-ttu-id="b16fb-121">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="b16fb-122">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b16fb-123">다음은 앱의 몇 가지 화면입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-123">Here are a few of the screens in the app:</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="b16fb-126">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-126">Create web app</span></span>

1. <span data-ttu-id="b16fb-127">Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-127">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="b16fb-128">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-128">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="b16fb-129">**새 프로젝트 구성** 대화 상자에서 **프로젝트 이름** 으로 `ContosoUniversity`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-129">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="b16fb-130">코드를 복사할 때 `namespace`가 일치하도록 대문자 표시를 포함하여 이 이름을 정확히 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-130">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="b16fb-131">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-131">Select **Create**.</span></span>
1. <span data-ttu-id="b16fb-132">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 다음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-132">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="b16fb-133">드롭다운에서 **.NET Core** 와 **ASP.NET Core 5.0** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-133">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="b16fb-134">**ASP.NET Core 웹앱(Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="b16fb-134">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="b16fb-135">
      ![새 ASP.NET Core 프로젝트 **만들기** 대화 상자](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="b16fb-135">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="b16fb-136">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="b16fb-136">Set up the site style</span></span>

<span data-ttu-id="b16fb-137">몇 가지 기본적 변경으로 사이트 메뉴, 레이아웃, 홈페이지가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-137">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="b16fb-138">*Views/Shared/_Layout.cshtml* 을 열고 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-138">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="b16fb-139">각 `ContosoUniversity` 항목을 `Contoso University`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-139">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="b16fb-140">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-140">There are three occurrences.</span></span>
* <span data-ttu-id="b16fb-141">**정보**, **학생**, **강좌**, **강사** 및 **부서** 메뉴 항목을 추가하고 **개인 정보** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-141">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="b16fb-142">위의 변경 내용은 다음 코드에서 강조 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-142">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="b16fb-143">*Views/Home/Index.cshtml* 에서 파일의 내용을 다음 태그로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-143">In *Views/Home/Index.cshtml*, replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="b16fb-144">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-144">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="b16fb-145">이 자습서에서 만든 페이지의 탭과 함께 홈페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-145">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso University 홈페이지](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="b16fb-147">EF Core NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="b16fb-147">EF Core NuGet packages</span></span>

<span data-ttu-id="b16fb-148">이 자습서에서는 SQL Server를 사용하며 공급자 패키지는 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-148">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="b16fb-149">EF SQL Server 패키지 및 해당 종속성(`Microsoft.EntityFrameworkCore` 및 `Microsoft.EntityFrameworkCore.Relational`)은 EF에 대한 런타임 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-149">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="b16fb-150">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 패키지와 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-150">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="b16fb-151">PMC(패키지 관리자 콘솔)에서 다음 명령을 입력하여 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-151">In the Package Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="b16fb-152">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 패키지는 EF Core 오류 페이지에 대한 ASP.NET Core 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-152">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="b16fb-153">이 미들웨어는 EF Core 마이그레이션의 오류를 검색 및 진단하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-153">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="b16fb-154">EF Core에 사용할 수 있는 다른 데이터베이스 공급자에 대한 정보는 [데이터베이스 공급자](/ef/core/providers/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-154">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="b16fb-155">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-155">Create the data model</span></span>

<span data-ttu-id="b16fb-156">이 앱을 위해 다음 엔터티 클래스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-156">The following entity classes are created for this app:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="b16fb-158">위의 엔터티에는 다음과 같은 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-158">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="b16fb-159">`Student` 엔터티와 `Enrollment` 엔터티 간의 일 대 다 관계.</span><span class="sxs-lookup"><span data-stu-id="b16fb-159">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="b16fb-160">학생 한 명이 여러 강좌에 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-160">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="b16fb-161">`Course` 엔터티와 `Enrollment` 엔터티 간의 일 대 다 관계.</span><span class="sxs-lookup"><span data-stu-id="b16fb-161">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b16fb-162">강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-162">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b16fb-163">다음 섹션에서 이러한 엔터티마다 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-163">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b16fb-164">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="b16fb-164">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="b16fb-166">*Models* 폴더에서 다음 코드로 `Student` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-166">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-167">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키(**PK**) 열입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-167">The `ID` property is the primary key (**PK**) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b16fb-168">기본적으로 EF는 이름이 `ID` 또는 `classnameID`인 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-168">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b16fb-169">예를 들어 PK 이름을 `ID` 대신 `StudentID`로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-169">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="b16fb-170">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-170">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b16fb-171">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-171">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b16fb-172">`Student` 엔터티의 `Enrollments` 속성:</span><span class="sxs-lookup"><span data-stu-id="b16fb-172">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="b16fb-173">해당 `Student` 엔터티와 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-173">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="b16fb-174">데이터베이스의 특정 `Student` 행에 두 개의 관련 `Enrollment` 행이 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="b16fb-174">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="b16fb-175">이 `Student` 엔터티의 `Enrollments` 탐색 속성은 이러한 두 개의 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-175">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="b16fb-176">`Enrollment` 행은 `StudentID` 외래 키(**FK**) 열에 학생의 PK 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-176">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key (**FK**) column.</span></span>

<span data-ttu-id="b16fb-177">탐색 속성 하나에 여러 엔터티가 있을 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="b16fb-177">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="b16fb-178">형식은 `ICollection<T>`, `List<T>`, `HashSet<T>`과 같은 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-178">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="b16fb-179">엔터티는 추가, 삭제, 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-179">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="b16fb-180">다 대 다 및 일 대 다 탐색 관계는 여러 엔터티를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-180">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="b16fb-181">`ICollection<T>`이 사용되는 경우 EF는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-181">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b16fb-182">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="b16fb-182">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="b16fb-184">*Models* 폴더에서 다음 코드로 `Enrollment` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-184">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-185">`EnrollmentID` 속성은 PK입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-185">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="b16fb-186">이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-186">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b16fb-187">`Student` 엔터티는 `ID` 패턴을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-187">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="b16fb-188">일부 개발자는 데이터 모델 전체에서 하나의 패턴을 사용하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-188">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="b16fb-189">이 자습서의 변형은 두 패턴을 모두 사용할 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-189">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="b16fb-190">[이후 자습서](inheritance.md)에서는 클래스 이름 없이 `ID`를 사용하여 데이터 모델에서 상속을 더 쉽게 구현하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-190">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b16fb-191">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-191">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b16fb-192">`Grade` 형식 선언 뒤에 있는 `?`는 `Grade` 속성이 [null 허용](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-192">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="b16fb-193">`null`인 등급은 0 등급과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-193">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="b16fb-194">`null`은 등급이 알려지지 않았거나 아직 할당되지 않았음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-194">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b16fb-195">`StudentID` 속성은 외래 키이며, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-195">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b16fb-196">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티만 보유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-196">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="b16fb-197">이것은 여러 `Enrollment` 엔터티를 보유할 수 있는 `Student.Enrollments` 탐색 속성과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-197">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="b16fb-198">`CourseID` 속성은 FK이며, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-198">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b16fb-199">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-199">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b16fb-200">Entity Framework는 이름이 `<`탐색 속성 이름`><`기본 키 속성 이름`>`으로 지정된 속성을 FK 속성으로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-200">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="b16fb-201">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 PK가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-201">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="b16fb-202">FK 속성의 이름도 `<`기본 키 속성 이름`>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-202">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="b16fb-203">예를 들어 `Course` 엔터티의 PK가 `CourseID`이므로 `CourseID`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-203">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b16fb-204">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="b16fb-204">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="b16fb-206">*Models* 폴더에서 다음 코드로 `Course` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-206">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-207">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b16fb-208">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b16fb-209">[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) 특성은 [이후 자습서](complex-data-model.md)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-209">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="b16fb-210">이 특성을 사용하면 데이터베이스가 특성을 생성하도록 하는 대신 강좌의 PK를 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-210">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="b16fb-211">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-211">Create the database context</span></span>

<span data-ttu-id="b16fb-212">지정된 데이터 모델의 EF 기능을 조정하는 주 클래스는 <xref:Microsoft.EntityFrameworkCore.DbContext> 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-212">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="b16fb-213">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-213">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="b16fb-214">`DbContext` 파생 클래스는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-214">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="b16fb-215">일부 EF 동작은 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-215">Some EF behaviors can be customized.</span></span> <span data-ttu-id="b16fb-216">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b16fb-217">프로젝트 폴더에서 `Data`라는 이름의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-217">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="b16fb-218">*Data* 폴더에서 다음 코드로 `SchoolContext` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-218">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-219">앞의 코드는 엔터티 집합마다 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-219">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="b16fb-220">EF 용어에서</span><span class="sxs-lookup"><span data-stu-id="b16fb-220">In EF terminology:</span></span>

* <span data-ttu-id="b16fb-221">엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-221">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="b16fb-222">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b16fb-223">`DbSet<Enrollment>` 및 `DbSet<Course>` 문을 생략할 수 있으며 동작은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-223">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="b16fb-224">EF가 이러한 문을 암시적으로 포함하는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-224">EF would include them implicitly because:</span></span>

* <span data-ttu-id="b16fb-225">`Student` 엔터티는 `Enrollment` 엔터티를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-225">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="b16fb-226">`Enrollment` 엔터티는 `Course` 엔터티를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-226">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="b16fb-227">데이터베이스가 만들어지면 EF는 `DbSet` 속성 이름과 동일한 이름을 갖는 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-227">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="b16fb-228">컬렉션의 속성 이름은 일반적으로 복수입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-228">Property names for collections are typically plural.</span></span> <span data-ttu-id="b16fb-229">예를 들어 `Student`가 아니라 `Students`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-229">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="b16fb-230">개발자는 테이블 이름을 복수화할지 여부에 대해 동의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-230">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="b16fb-231">이러한 자습서에서는 `DbContext`에서 단수 테이블 이름을 지정하여 기본 동작이 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-231">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="b16fb-232">이렇게 하려면 마지막 DbSet 속성 뒤에 강조 표시된 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-232">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="b16fb-233">`SchoolContext`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-233">Register the `SchoolContext`</span></span>

<span data-ttu-id="b16fb-234">ASP.NET Core는 [종속성 주입](../../fundamentals/dependency-injection.md)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-234">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="b16fb-235">EF 데이터베이스 컨텍스트와 같은 서비스는 앱 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-235">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="b16fb-236">MVC 컨트롤러와 같이 이러한 서비스가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-236">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="b16fb-237">컨텍스트 인스턴스를 가져오는 컨트롤러 생성자 코드는 이 자습서의 뒷부분에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-237">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="b16fb-238">`SchoolContext`를 서비스로 등록하려면 *Startup.cs* 를 열고 강조 표시된 줄을 `ConfigureServices` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-238">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="b16fb-239">`DbContextOptionsBuilder` 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-239">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="b16fb-240">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-240">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="b16fb-241">*appsettings.json* 파일을 열고 다음 표시에 나온 것처럼 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-241">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="b16fb-242">데이터베이스 예외 필터 추가</span><span class="sxs-lookup"><span data-stu-id="b16fb-242">Add the database exception filter</span></span>

<span data-ttu-id="b16fb-243">다음 코드와 같이 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A>를 `ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-243">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=6)]

<span data-ttu-id="b16fb-244">`AddDatabaseDeveloperPageExceptionFilter`는 [개발 환경](xref:fundamentals/environments)에서 유용한 오류 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-244">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b16fb-245">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b16fb-245">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b16fb-246">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="b16fb-247">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b16fb-248">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-248">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b16fb-249">기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-249">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="b16fb-250">테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="b16fb-250">Initialize DB with test data</span></span>

<span data-ttu-id="b16fb-251">EF는 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-251">EF creates an empty database.</span></span> <span data-ttu-id="b16fb-252">이 섹션에서는 테스트 데이터로 채우기 위해 데이터베이스를 만든 후 호출되는 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-252">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="b16fb-253">`EnsureCreated` 메서드는 자동으로 데이터베이스를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-253">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="b16fb-254">[이후의 자습서](migrations.md)에서는 데이터베이스를 삭제하고 다시 만드는 대신 Code First 마이그레이션을 사용하여 데이터베이스 스키마를 변경함으로써 모델 변경 내용을 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-254">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="b16fb-255">*Data* 폴더에서 다음 코드로 `DbInitializer`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-255">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="b16fb-256">위의 코드는 데이터베이스가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-256">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="b16fb-257">데이터베이스를 찾을 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="b16fb-257">If the database is not found;</span></span>
  * <span data-ttu-id="b16fb-258">테스트 데이터를 사용하여 생성 및 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-258">It is created and loaded with test data.</span></span> <span data-ttu-id="b16fb-259">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-259">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="b16fb-260">데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-260">If the database if found, it takes no action.</span></span>

<span data-ttu-id="b16fb-261">다음 코드로 *Program.cs* 를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-261">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="b16fb-262">*Program.cs* 는 앱 시작 시 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-262">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="b16fb-263">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-263">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b16fb-264">`DbInitializer.Initialize` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-264">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="b16fb-265">다음 코드에 나온 것처럼 `Initialize` 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-265">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="b16fb-266">처음으로 앱이 실행되고 데이터베이스가 생성되며 테스트 데이터로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-266">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="b16fb-267">데이터 모델이 변경될 때마다</span><span class="sxs-lookup"><span data-stu-id="b16fb-267">Whenever the data model changes:</span></span>

* <span data-ttu-id="b16fb-268">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-268">Delete the database.</span></span>
* <span data-ttu-id="b16fb-269">시드 메서드를 업데이트하고 새 데이터베이스로 새로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-269">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="b16fb-270">이후 자습서에서는 데이터 모델이 변경될 때 데이터베이스를 삭제 후 다시 만들지 않고 데이터베이스가 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-270">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="b16fb-271">데이터 모델이 변경되어도 데이터는 손실되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-271">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="b16fb-272">컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-272">Create controller and views</span></span>

<span data-ttu-id="b16fb-273">Visual Studio에서 스캐폴딩 엔진을 사용하여 EF에서 데이터를 쿼리하고 저장하는 데 사용하는 MVC 컨트롤러 및 보기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-273">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="b16fb-274">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 작업 메서드와 보기를 자동으로 만드는 작업을 스캐폴딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-274">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="b16fb-275">**솔루션 탐색기** 에서 `Controllers` 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-275">In **Solution Explorer**, right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="b16fb-276">**스캐폴드 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="b16fb-276">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="b16fb-277">**Entity Framework를 사용하며 뷰가 포함된 MVC 컨트롤러** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-277">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="b16fb-278">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-278">Click **Add**.</span></span> <span data-ttu-id="b16fb-279">**Entity Framework를 사용하여 뷰 포함 MVC 컨트롤러 추가** 대화 상자가 표시됩니다. ![학생 스캐폴드](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="b16fb-279">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="b16fb-280">**모델 클래스** 에서 **Student** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-280">In **Model class**, select **Student**.</span></span>
  * <span data-ttu-id="b16fb-281">**데이터 컨텍스트 클래스** 에서 **SchoolContext** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-281">In **Data context class**, select **SchoolContext**.</span></span>
  * <span data-ttu-id="b16fb-282">기본값 **StudentsController** 를 이름으로 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-282">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="b16fb-283">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-283">Click **Add**.</span></span>

<span data-ttu-id="b16fb-284">Visual Studio 스캐폴딩 엔진이 `StudentsController.cs` 파일 및 컨트롤러와 함께 작동하는 뷰 집합(`*.cshtml` 파일)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-284">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="b16fb-285">컨트롤러는 `SchoolContext`를 생성자 매개 변수로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-285">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="b16fb-286">ASP.NET Core 종속성 주입은 `SchoolContext`의 인스턴스를 컨트롤러에 전달하는 것을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-286">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="b16fb-287">이 인스턴스는 `Startup` 클래스에서 구성했습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-287">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="b16fb-288">컨트롤러는 데이터베이스에 모든 학생을 표시하는 `Index` 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-288">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="b16fb-289">메서드는 데이터베이스 컨텍스트 인스턴스의 `Students` 속성을 읽어 학생 엔터티 집합에서 학생의 목록을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-289">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="b16fb-290">이 코드의 비동기 프로그래밍 요소는 이 자습서 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-290">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="b16fb-291">*Views/Students/Index.cshtml* 보기가 테이블에 이 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-291">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="b16fb-292">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-292">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="b16fb-293">학생 탭을 클릭하여 `DbInitializer.Initialize` 메서드가 삽입된 테스트 데이터를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-293">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="b16fb-294">브라우저 창의 폭에 따라 페이지의 맨 위에 `Students` 탭 링크가 표시되거나 링크를 보기 위해 오른쪽 맨 위의 탐색 아이콘을 클릭해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-294">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![좁은 Contoso University 홈페이지](intro/_static/home-page-narrow.png)

![학생 인덱스 페이지](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="b16fb-297">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="b16fb-297">View the database</span></span>

<span data-ttu-id="b16fb-298">앱이 시작되면 `DbInitializer.Initialize` 메서드가 `EnsureCreated`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-298">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="b16fb-299">EF에서 데이터베이스가 없음을 확인했습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-299">EF saw that there was no database:</span></span>

* <span data-ttu-id="b16fb-300">따라서 데이터베이스를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-300">So it created a database.</span></span>
* <span data-ttu-id="b16fb-301">`Initialize` 메서드 코드가 데이터베이스를 데이터로 채웠습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-301">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="b16fb-302">**SQL Server 개체 탐색기**(SSOX)를 사용하여 Visual Studio에서 데이터베이스를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-302">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="b16fb-303">Visual Studio의 **보기** 메뉴에서 **SQL Server 개체 탐색기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-303">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b16fb-304">SSOX에서 **(localdb)\MSSQLLocalDB > Databases** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-304">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="b16fb-305">*appsettings.json* 파일의 연결 문자열에 있는 데이터베이스 이름의 항목인 `ContosoUniversity1`를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-305">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="b16fb-306">**테이블** 노드를 확장하여 데이터베이스의 테이블을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-306">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX의 테이블](intro/_static/ssox-tables.png)

<span data-ttu-id="b16fb-308">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 클릭하여 테이블의 데이터를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-308">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX의 학생 테이블](intro/_static/ssox-student-table.png)

<span data-ttu-id="b16fb-310">`*.mdf` 및 `*.ldf` 데이터베이스 파일은 *C:\Users\\\<username>* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-310">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="b16fb-311">앱 시작 시 실행되는 이니셜라이저 메서드에서 `EnsureCreated`가 호출되기 때문에 다음과 같은 작업을 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-311">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="b16fb-312">`Student` 클래스를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-312">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="b16fb-313">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-313">Delete the database.</span></span>
* <span data-ttu-id="b16fb-314">앱을 중지했다가 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-314">Stop, then start the app.</span></span> <span data-ttu-id="b16fb-315">데이터베이스는 변경 내용에 맞게 자동으로 다시 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-315">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="b16fb-316">예를 들어 `EmailAddress` 속성을 `Student` 클래스에 추가하면 다시 만들어진 테이블에 새 `EmailAddress` 열이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-316">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="b16fb-317">분류 보기에는 새 `EmailAddress` 속성이 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-317">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="b16fb-318">규칙</span><span class="sxs-lookup"><span data-stu-id="b16fb-318">Conventions</span></span>

<span data-ttu-id="b16fb-319">EF가 사용하는 규칙 때문에 EF가 완전한 데이터베이스를 만들기 위해 쓰는 코드의 양은 최소입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-319">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="b16fb-320">`DbSet` 속성의 이름은 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-320">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="b16fb-321">`DbSet` 속성에서 참조하지 않는 엔터티의 경우 엔터티 클래스 이름이 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-321">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="b16fb-322">엔터티 속성 이름은 열 이름에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-322">Entity property names are used for column names.</span></span>
* <span data-ttu-id="b16fb-323">이름이 `ID` 또는 `classnameID`인 엔터티 속성은 PK 속성으로 인식됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-323">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="b16fb-324">이름이 `<`탐색 속성 이름`><`PK 속성 이름`>`으로 지정된 속성은 FK 속성으로 해석됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-324">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="b16fb-325">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 PK가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-325">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="b16fb-326">FK 속성의 이름도 `<`기본 키 속성 이름`>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-326">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="b16fb-327">예를 들어 `Enrollment` 엔터티의 PK가 `EnrollmentID`이므로 `EnrollmentID`로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-327">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="b16fb-328">기본 동작은 재정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-328">Conventional behavior can be overridden.</span></span> <span data-ttu-id="b16fb-329">예를 들어 이 자습서의 앞부분에 나온 것처럼 테이블 이름을 명시적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-329">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="b16fb-330">열 이름과 속성을 PK 또는 FK로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-330">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b16fb-331">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="b16fb-331">Asynchronous code</span></span>

<span data-ttu-id="b16fb-332">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b16fb-333">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b16fb-334">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b16fb-335">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b16fb-336">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b16fb-337">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b16fb-338">비동기 코드는 런타임 시 약간의 오버헤드를 도입하지만 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-338">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b16fb-339">다음 코드에서 `async`, `Task<T>`, `await`, `ToListAsync`는 코드가 비동기적으로 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-339">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b16fb-340">`async` 키워드는 컴파일러에서 메서드 본문의 부분에 대한 콜백을 생성하고 반환되는 `Task<IActionResult>` 개체를 자동으로 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-340">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="b16fb-341">반환 형식 `Task<IActionResult>`는 `IActionResult` 형식의 결과와 함께 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-341">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="b16fb-342">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b16fb-343">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b16fb-344">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b16fb-345">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b16fb-346">EF를 사용하는 비동기 코드를 작성할 때 주의 사항:</span><span class="sxs-lookup"><span data-stu-id="b16fb-346">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="b16fb-347">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b16fb-348">예를 들어 `ToListAsync`, `SingleOrDefaultAsync` 및 `SaveChangesAsync`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-348">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b16fb-349">예를 들어 `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-349">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b16fb-350">EF 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b16fb-350">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="b16fb-351">비동기 EF 메서드를 호출하는 경우 항상 `await` 키워드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-351">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="b16fb-352">비동기 코드의 성능 이점을 활용하기 위해서는 사용되는 라이브러리 패키지 또한 쿼리가 데이터베이스로 전송되도록 하는 EF 메서드를 호출하는 경우 비동기를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-352">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="b16fb-353">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/articles/standard/async)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-353">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="b16fb-354">가져오는 엔터티 제한</span><span class="sxs-lookup"><span data-stu-id="b16fb-354">Limit entities fetched</span></span>

<span data-ttu-id="b16fb-355">쿼리에서 반환되는 항목 수를 제한하는 방법에 대한 자세한 내용은 [성능 고려 사항](xref:data/ef-rp/intro)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-355">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="b16fb-356">기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 수행하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-356">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b16fb-357">기본 CRUD 기능 구현</span><span class="sxs-lookup"><span data-stu-id="b16fb-357">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="b16fb-358">Contoso University 샘플 웹 애플리케이션은 EF(Entity Framework) Core 2.2 및 Visual Studio 2017 또는 2019를 사용하여 ASP.NET Core 2.2 MVC 웹 애플리케이션을 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-358">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="b16fb-359">이 자습서는 ASP.NET Core 3.1에 맞게 업데이트되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-359">This tutorial has not been updated for ASP.NET Core 3.1.</span></span> <span data-ttu-id="b16fb-360">[ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0)에 맞게 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-360">It has been updated for [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).</span></span>

<span data-ttu-id="b16fb-361">샘플 애플리케이션은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-361">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b16fb-362">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-362">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b16fb-363">이는 Contoso University 샘플 애플리케이션을 처음부터 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-363">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b16fb-364">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b16fb-364">Prerequisites</span></span>

* [<span data-ttu-id="b16fb-365">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b16fb-365">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="b16fb-366">다른 워크로드를 포함한 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019):</span><span class="sxs-lookup"><span data-stu-id="b16fb-366">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="b16fb-367">**ASP.NET 및 웹 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="b16fb-367">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="b16fb-368">**.NET Core 플랫폼 간 개발** 워크로드</span><span class="sxs-lookup"><span data-stu-id="b16fb-368">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b16fb-369">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b16fb-369">Troubleshooting</span></span>

<span data-ttu-id="b16fb-370">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="b16fb-371">일반적인 오류 목록 및 해결 방법은 [시리즈에서 마지막 자습서의 문제 해결 섹션](advanced.md#common-errors)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-371">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="b16fb-372">필요한 내용을 찾지 못한 경우 질문을 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 StackOverflow.com에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-372">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="b16fb-373">10 자습서의 시리즈이며, 각각은 이전 자습서에서 수행된 작업을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-373">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="b16fb-374">각 자습서를 성공적으로 완료한 후에 프로젝트 복사본의 저장을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-374">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="b16fb-375">그런 다음, 문제가 발생한 경우 전체 시리즈의 처음으로 다시 이동하는 대신 이전 자습서부터 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-375">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="b16fb-376">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="b16fb-376">Contoso University web app</span></span>

<span data-ttu-id="b16fb-377">이 자습서에서 빌드하는 애플리케이션은 간단한 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-377">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="b16fb-378">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-378">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b16fb-379">다음은 만들 몇 가지 화면입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-379">Here are a few of the screens you'll create.</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="b16fb-382">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-382">Create web app</span></span>

* <span data-ttu-id="b16fb-383">Visual Studio를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-383">Open Visual Studio.</span></span>

* <span data-ttu-id="b16fb-384">**파일** 메뉴에서 **새로 만들기 > 프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-384">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="b16fb-385">왼쪽 창에서 **설치됨 > Visual C# > 웹** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-385">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="b16fb-386">**ASP.NET Core 웹 애플리케이션** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-386">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="b16fb-387">이름으로 **ContosoUniversity** 를 입력하고 **확인** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-387">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![새 프로젝트 대화 상자](intro/_static/new-project2.png)

* <span data-ttu-id="b16fb-389">**새 ASP.NET Core 웹 애플리케이션** 대화 상자가 표시될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-389">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="b16fb-390">**.NET Core**, **ASP.NET Core 2.2** 및 **웹 애플리케이션(Model-View-Controller)** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-390">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="b16fb-391">**인증** 이 **인증 없음** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-391">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="b16fb-392">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-392">Select **OK**</span></span>

  ![새 ASP.NET Core 프로젝트 대화 상자](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="b16fb-394">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="b16fb-394">Set up the site style</span></span>

<span data-ttu-id="b16fb-395">몇 가지 간단한 변경 내용으로 사이트 메뉴, 레이아웃 및 홈 페이지를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-395">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="b16fb-396">*Views/Shared/_Layout.cshtml* 을 열고 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-396">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="b16fb-397">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-397">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b16fb-398">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-398">There are three occurrences.</span></span>

* <span data-ttu-id="b16fb-399">**정보**, **학생**, **강좌**, **강사** 및 **부서** 메뉴 항목을 추가하고 **개인 정보** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-399">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="b16fb-400">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-400">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="b16fb-401">*Views/Home/Index.cshtml* 에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 애플리케이션에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-401">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="b16fb-402">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-402">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="b16fb-403">이 자습서에서 만드는 페이지에 대한 탭이 포함된 홈페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-403">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University 홈페이지](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="b16fb-405">EF Core NuGet 패키지 정보</span><span class="sxs-lookup"><span data-stu-id="b16fb-405">About EF Core NuGet packages</span></span>

<span data-ttu-id="b16fb-406">프로젝트에 EF Core 지원을 추가하려면 대상으로 지정하려는 데이터베이스 공급자를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-406">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="b16fb-407">이 자습서에서는 SQL Server를 사용하며 공급자 패키지는 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-407">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="b16fb-408">이 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있으므로 패키지를 참조할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-408">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="b16fb-409">EF SQL Server 패키지 및 해당 종속성(`Microsoft.EntityFrameworkCore` 및 `Microsoft.EntityFrameworkCore.Relational`)은 EF에 대한 런타임 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-409">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="b16fb-410">[마이그레이션](migrations.md) 자습서에서 나중에 도구 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-410">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="b16fb-411">Entity Framework Core에 사용할 수 있는 다른 데이터베이스 공급자에 대한 정보는 [데이터베이스 공급자](/ef/core/providers/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-411">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="b16fb-412">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-412">Create the data model</span></span>

<span data-ttu-id="b16fb-413">다음으로 Contoso University 애플리케이션에 대한 엔터티 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-413">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="b16fb-414">다음과 같은 세 가지 엔터티로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-414">You'll start with the following three entities.</span></span>

![강좌-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="b16fb-416">`Student` 및 `Enrollment` 엔터티 간에 일대다 관계가 있으며 `Course` 및 `Enrollment` 엔터티 간에 일대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-416">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b16fb-417">즉, 학생은 개수에 관계 없이 강좌에 등록될 수 있으며 강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-417">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b16fb-418">다음 섹션에서 이러한 엔터티 각각에 대한 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-418">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b16fb-419">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="b16fb-419">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="b16fb-421">*Models* 폴더에서 *Student.cs* 라는 클래스 파일을 만들고 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-421">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-422">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-422">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b16fb-423">기본적으로 Entity Framework는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-423">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="b16fb-424">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-424">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b16fb-425">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-425">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b16fb-426">이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student` 엔터티에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-426">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="b16fb-427">즉, 데이터베이스의 `Student` 행에 두 개의 관련된 `Enrollment` 행(해당 StudentID 외래 키 열에 해당 학생의 기본 키 값을 포함하는 행)이 있는 경우 해당 `Student` 엔터티의 `Enrollments` 탐색 속성은 두 개의 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-427">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="b16fb-428">탐색 속성이 여러 엔터티를 포함할 수 있는 경우(다대다 또는 일대다 관계로), 해당 형식은 `ICollection<T>`와 같이 항목이 추가, 삭제 및 업데이트될 수 있는 목록이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-428">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="b16fb-429">`List<T>` 또는 `HashSet<T>`와 같은 형식 또는 `ICollection<T>`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-429">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b16fb-430">`ICollection<T>`를 지정하는 경우 EF는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-430">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b16fb-431">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="b16fb-431">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="b16fb-433">*Models* 폴더에서 *Enrollment.cs* 를 만들고 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-433">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-434">`EnrollmentID` 속성은 기본 키가 됩니다. 이 엔터티는 `Student` 엔터티에서 본 것과 같이 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-434">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="b16fb-435">일반적으로 하나의 패턴을 선택하고 이를 데이터 모델 전체에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-435">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="b16fb-436">여기에서 변형은 패턴 중 하나를 사용할 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-436">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="b16fb-437">[자습서의 뒷부분](inheritance.md)에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-437">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b16fb-438">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-438">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b16fb-439">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-439">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="b16fb-440">Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-440">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b16fb-441">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-441">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b16fb-442">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되어 있으므로 속성은 단일 `Student` 엔터티만 포함할 수 있습니다(이전에 살펴본 여러 `Enrollment` 엔터티를 포함할 수 있는 `Student.Enrollments` 탐색 속성과 달리).</span><span class="sxs-lookup"><span data-stu-id="b16fb-442">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="b16fb-443">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-443">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b16fb-444">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-444">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b16fb-445">Entity Framework는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키 속성으로는 해석합니다(예: `Student` 엔터티의 기본 키가 `ID`이므로 `Student` 탐색 속성의 경우 `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="b16fb-445">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="b16fb-446">외래 키 속성의 이름을 단순히 `<primary key property name>`으로 지정할 수 있습니다(예: `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="b16fb-446">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b16fb-447">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="b16fb-447">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="b16fb-449">*Models* 폴더에서 *Course.cs* 를 만들고 기존 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-449">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-450">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b16fb-451">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b16fb-452">이 시리즈의 [이후의 자습서](complex-data-model.md)에서 `DatabaseGenerated` 특성에 대해 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-452">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="b16fb-453">기본적으로 이 특성을 통해 생성하는 데이터베이스를 갖는 대신 강좌에 대한 기본 키를 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-453">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="b16fb-454">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-454">Create the database context</span></span>

<span data-ttu-id="b16fb-455">특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-455">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b16fb-456">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-456">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="b16fb-457">코드에서 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-457">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="b16fb-458">특정 Entity Framework 동작을 사용자 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-458">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="b16fb-459">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-459">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b16fb-460">프로젝트 폴더에서 *Data* 라는 이름의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-460">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="b16fb-461">*Data* 폴더에서 *SchoolContext.cs* 라는 새로운 클래스 파일을 만들고 템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-461">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-462">이 코드는 각 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-462">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="b16fb-463">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-463">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b16fb-464">`DbSet<Enrollment>` 및 `DbSet<Course>` 문을 생략할 수 있으며 이는 동일하게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-464">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="b16fb-465">`Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 Entity Framework는 이를 암시적으로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-465">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="b16fb-466">데이터베이스가 만들어지면 EF는 `DbSet` 속성 이름과 동일한 이름을 갖는 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-466">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="b16fb-467">컬렉션에 대한 속성 이름은 일반적으로 복수형이지만(Student보다는 Students) 개발자는 테이블 이름을 복수화할지 여부에 대해 동의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-467">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="b16fb-468">이러한 자습서의 경우 DbContext에서 단수 테이블 이름을 지정하여 기본 동작을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-468">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="b16fb-469">이렇게 하려면 마지막 DbSet 속성 뒤에 강조 표시된 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-469">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="b16fb-470">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-470">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="b16fb-471">SchoolContext 등록</span><span class="sxs-lookup"><span data-stu-id="b16fb-471">Register the SchoolContext</span></span>

<span data-ttu-id="b16fb-472">ASP.NET Core는 기본적으로 [종속성 주입](../../fundamentals/dependency-injection.md)을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-472">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="b16fb-473">서비스(예: EF 데이터베이스 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-473">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b16fb-474">이러한 서비스(예: MVC 컨트롤러)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-474">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b16fb-475">이 자습서의 뒷부분에서 컨텍스트 인스턴스를 가져오는 컨트롤러 생성자 코드를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-475">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="b16fb-476">`SchoolContext`를 서비스로 등록하려면 *Startup.cs* 를 열고 강조 표시된 줄을 `ConfigureServices` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-476">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="b16fb-477">`DbContextOptionsBuilder` 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-477">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="b16fb-478">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-478">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="b16fb-479">`ContosoUniversity.Data` 및 `Microsoft.EntityFrameworkCore` 네임스페이스에 대해 `using` 문을 추가한 다음, 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-479">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="b16fb-480">*appsettings.json* 파일을 열고 다음 예제에 나온 것처럼 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-480">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b16fb-481">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b16fb-481">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b16fb-482">연결 문자열은 SQL Server LocalDB 데이터베이스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-482">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="b16fb-483">LocalDB는 프로덕션 사용이 아닌 애플리케이션 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-483">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="b16fb-484">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-484">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b16fb-485">기본적으로 LocalDB는 *.mdf* 데이터베이스 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-485">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="b16fb-486">테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="b16fb-486">Initialize DB with test data</span></span>

<span data-ttu-id="b16fb-487">Entity Framework에서 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-487">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="b16fb-488">이 섹션에서는 테스트 데이터로 채우기 위해 데이터베이스를 만든 후 호출되는 메서드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-488">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="b16fb-489">여기에서 `EnsureCreated` 메서드를 사용하여 자동으로 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-489">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="b16fb-490">[이후의 자습서](migrations.md)에서는 데이터베이스를 삭제하고 다시 작성하는 대신 데이터베이스 스키마를 변경하도록 Code First 마이그레이션을 사용하여 모델 변경 내용을 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-490">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="b16fb-491">*Data* 폴더에서 *DbInitializer.cs* 라는 새 클래스 파일을 만들고 템플릿 코드를 다음 코드로 바꿉니다. 이로 인해 필요할 때 데이터베이스가 만들어지며 테스트 데이터를 새 데이터베이스로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-491">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="b16fb-492">코드는 데이터베이스에 학생이 있는지를 확인하고 없는 경우 데이터베이스가 새 것이며 테스트 데이터로 시드되어야 한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-492">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="b16fb-493">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-493">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b16fb-494">*Program.cs* 에서 애플리케이션 시작 시에 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-494">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="b16fb-495">종속성 주입 컨테이너에서 데이터베이스 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-495">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b16fb-496">컨텍스트를 전달하는 시드 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-496">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="b16fb-497">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-497">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b16fb-498">애플리케이션을 처음으로 실행하면 데이터베이스가 생성되고 테스트 데이터로 시드됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-498">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="b16fb-499">데이터 모델을 변경할 때마다:</span><span class="sxs-lookup"><span data-stu-id="b16fb-499">Whenever you change the data model:</span></span>

 * <span data-ttu-id="b16fb-500">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-500">Delete the database.</span></span>
 * <span data-ttu-id="b16fb-501">시드 메서드를 업데이트하고 같은 방법으로 새 데이터베이스로 새로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-501">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="b16fb-502">이후의 자습서에서는 데이터 모델이 변경될 때 데이터베이스를 삭제하고 다시 작성하지 않고 데이터베이스를 수정하는 방법을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-502">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="b16fb-503">컨트롤러 및 뷰 만들기</span><span class="sxs-lookup"><span data-stu-id="b16fb-503">Create controller and views</span></span>

<span data-ttu-id="b16fb-504">이 섹션에서는 Visual Studio의 스캐폴딩 엔진을 사용하여 데이터 쿼리와 저장에 EF를 사용하는 MVC 컨트롤러 및 뷰를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-504">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="b16fb-505">CRUD 작업 메서드와 보기를 자동으로 만드는 작업을 스캐폴딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-505">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="b16fb-506">스캐폴딩은 사용자 고유의 요구 사항에 맞게 수정할 수 있는 스캐폴드된 코드가 시작 지점인 코드 생성과 다릅니다. 반면에 일반적으로 생성된 코드를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-506">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="b16fb-507">생성된 코드를 사용자 지정해야 하는 경우 partial 클래스를 사용하거나 항목이 변경될 때 코드를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-507">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="b16fb-508">**솔루션 탐색기** 에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 -> 스캐폴드 항목 새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-508">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="b16fb-509">**스캐폴드 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="b16fb-509">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="b16fb-510">**Entity Framework를 사용하며 뷰가 포함된 MVC 컨트롤러** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-510">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="b16fb-511">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-511">Click **Add**.</span></span> <span data-ttu-id="b16fb-512">**Entity Framework를 사용하여 뷰 포함 MVC 컨트롤러 추가** 대화 상자가 표시됩니다. ![학생 스캐폴드](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="b16fb-512">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="b16fb-513">**모델 클래스** 에서 **학생** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-513">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="b16fb-514">**데이터 컨텍스트 클래스** 에서 **SchoolContext** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-514">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="b16fb-515">기본값 **StudentsController** 를 이름으로 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-515">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="b16fb-516">**추가** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-516">Click **Add**.</span></span>

<span data-ttu-id="b16fb-517">Visual Studio 스캐폴딩 엔진은 *StudentsController.cs* 파일 및 컨트롤러와 함께 작동하는 뷰 집합( *.cshtml* 파일)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-517">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="b16fb-518">컨트롤러는 `SchoolContext`를 생성자 매개 변수로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-518">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="b16fb-519">ASP.NET Core 종속성 주입은 `SchoolContext`의 인스턴스를 컨트롤러에 전달하는 것을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-519">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="b16fb-520">이것은 *Startup.cs* 파일에서 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-520">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="b16fb-521">컨트롤러는 데이터베이스에 모든 학생을 표시하는 `Index` 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-521">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="b16fb-522">메서드는 데이터베이스 컨텍스트 인스턴스의 `Students` 속성을 읽어 학생 엔터티 집합에서 학생의 목록을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-522">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="b16fb-523">이 코드의 비동기 프로그래밍 요소에 대해서는 이 자습서 뒷부분에서 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-523">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="b16fb-524">*Views/Students/Index.cshtml* 보기가 테이블에 이 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-524">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="b16fb-525">CTRL+F5 키를 눌러 프로젝트를 실행하거나 메뉴 모음에서 **디버그 > 디버깅하지 않고 시작** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-525">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="b16fb-526">학생 탭을 클릭하여 `DbInitializer.Initialize` 메서드가 삽입된 테스트 데이터를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-526">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="b16fb-527">브라우저 창의 폭에 따라 페이지의 맨 위에 `Students` 탭 링크가 표시되거나 링크를 보기 위해 오른쪽 맨 위의 탐색 아이콘을 클릭해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-527">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![좁은 Contoso University 홈페이지](intro/_static/home-page-narrow.png)

![학생 인덱스 페이지](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="b16fb-530">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="b16fb-530">View the database</span></span>

<span data-ttu-id="b16fb-531">애플리케이션을 시작했을 때 `DbInitializer.Initialize` 메서드는 `EnsureCreated`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-531">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="b16fb-532">EF는 데이터베이스가 없는 것을 확인하고 하나를 만들었습니다. 그런 다음, `Initialize` 메서드 코드의 나머지 부분은 데이터베이스를 데이터로 채웠습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-532">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="b16fb-533">**SQL Server 개체 탐색기**(SSOX)를 사용하여 Visual Studio에서 데이터베이스를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-533">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="b16fb-534">브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-534">Close the browser.</span></span>

<span data-ttu-id="b16fb-535">SSOX 창이 열려 있지 않은 경우 Visual Studio의 **보기** 메뉴에서 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-535">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="b16fb-536">SSOX에서 **(localdb)\MSSQLLocalDB > Databases** 를 클릭한 다음 *appsettings.json* 파일의 연결 문자열에 있는 데이터베이스 이름 항목을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-536">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="b16fb-537">**테이블** 노드를 확장하여 데이터베이스의 테이블을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-537">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX의 테이블](intro/_static/ssox-tables.png)

<span data-ttu-id="b16fb-539">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기** 를 클릭하여 만들어진 열 및 테이블에 삽입된 행을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-539">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX의 학생 테이블](intro/_static/ssox-student-table.png)

<span data-ttu-id="b16fb-541">*.mdf* 및 *.ldf* 데이터베이스 파일은 *C:\Users\\\<username>* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-541">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="b16fb-542">앱 시작 시 실행되는 이니셜라이저 메서드에서 `EnsureCreated`를 호출하기 때문에 이제 `Student` 클래스에 변경 내용을 만들고, 데이터베이스를 삭제하고, 애플리케이션을 다시 시작할 수 있으며, 데이터베이스는 변경 내용에 맞도록 자동으로 다시 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-542">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="b16fb-543">예를 들어 `EmailAddress` 속성을 `Student` 클래스에 추가하는 경우 다시 만들어진 테이블에 새 `EmailAddress` 열이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-543">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="b16fb-544">규칙</span><span class="sxs-lookup"><span data-stu-id="b16fb-544">Conventions</span></span>

<span data-ttu-id="b16fb-545">Entity Framework에서 전체 데이터베이스를 만들 수 있도록 작성해야 했던 코드의 양은 규칙의 사용 또는 Entity Framework에서 만드는 가정으로 인해 최소입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-545">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="b16fb-546">`DbSet` 속성의 이름은 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-546">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="b16fb-547">`DbSet` 속성에서 참조하지 않는 엔터티의 경우 엔터티 클래스 이름이 테이블 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-547">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="b16fb-548">엔터티 속성 이름은 열 이름에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-548">Entity property names are used for column names.</span></span>
* <span data-ttu-id="b16fb-549">ID 또는 classnameID로 명명된 엔터티 속성은 기본 키 속성으로 인식됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-549">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="b16fb-550">속성 이름이 *\<navigation property name>\<primary key property name>* 인 경우 외래 키 속성으로는 해석됩니다(예: `Student` 엔터티의 기본 키가 `ID`이므로 `Student` 탐색 속성의 경우 `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="b16fb-550">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="b16fb-551">외래 키 속성의 이름을 단순히 *\<primary key property name>* 으로 지정할 수 있습니다(예: `Enrollment` 엔터티의 기본 키가 `EnrollmentID`이므로 `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="b16fb-551">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="b16fb-552">기본 동작은 재정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-552">Conventional behavior can be overridden.</span></span> <span data-ttu-id="b16fb-553">예를 들어 이 자습서의 앞부분에서 본 것처럼 테이블 이름을 명시적으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-553">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="b16fb-554">또한 이 시리즈의 [이후의 자습서](complex-data-model.md)에서 볼 수 있듯이 열 이름을 설정하고 기본 키 또는 외래 키로 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-554">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b16fb-555">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="b16fb-555">Asynchronous code</span></span>

<span data-ttu-id="b16fb-556">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-556">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b16fb-557">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-557">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b16fb-558">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-558">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b16fb-559">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-559">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b16fb-560">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-560">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b16fb-561">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-561">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b16fb-562">비동기 코드는 런타임 시 약간의 오버헤드를 도입하지만 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-562">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b16fb-563">다음 코드에서 `async` 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-563">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b16fb-564">`async` 키워드는 컴파일러에서 메서드 본문의 부분에 대한 콜백을 생성하고 반환되는 `Task<IActionResult>` 개체를 자동으로 만들도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-564">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="b16fb-565">반환 형식 `Task<IActionResult>`는 `IActionResult` 형식의 결과와 함께 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-565">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="b16fb-566">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b16fb-567">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b16fb-568">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b16fb-569">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b16fb-570">Entity Framework를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-570">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="b16fb-571">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-571">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b16fb-572">예를 들어 `ToListAsync`, `SingleOrDefaultAsync` 및 `SaveChangesAsync`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-572">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b16fb-573">예를 들어 `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-573">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b16fb-574">EF 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b16fb-574">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="b16fb-575">비동기 EF 메서드를 호출하는 경우 항상 `await` 키워드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-575">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="b16fb-576">비동기 코드의 성능 이점을 활용하려는 경우 사용 중인(예: 페이징) 라이브러리 패키지 또한 쿼리를 데이터베이스에 전송하도록 하는 Entity Framework 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-576">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="b16fb-577">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/articles/standard/async)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b16fb-577">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b16fb-578">다음 단계</span><span class="sxs-lookup"><span data-stu-id="b16fb-578">Next steps</span></span>

<span data-ttu-id="b16fb-579">기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 수행하는 방법을 알아보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="b16fb-579">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b16fb-580">기본 CRUD 기능 구현</span><span class="sxs-lookup"><span data-stu-id="b16fb-580">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
