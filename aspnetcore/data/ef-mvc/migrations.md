---
title: 자습서 5부, Contoso University 샘플에 마이그레이션 적용
description: Contoso University 자습서 시리즈의 5부입니다. ASP.NET Core MVC 앱에서 데이터 모델 변경 관리를 위해 EF Core 마이그레이션 기능을 사용합니다.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: aebbc3f29b0356c7993abd83869ab21d3613bf61
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589350"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="7b82e-104">자습서: 5부, Contoso University 샘플에 마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="7b82e-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="7b82e-105">이 자습서에서는 데이터 모델 변경을 관리하기 위해 EF Core 마이그레이션 기능을 사용하기 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="7b82e-106">이후의 자습서에서는 데이터 모델을 변경하면서 더 많은 마이그레이션을 추가하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="7b82e-107">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7b82e-108">마이그레이션에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="7b82e-108">Learn about migrations</span></span>
> * <span data-ttu-id="7b82e-109">초기 마이그레이션 만들기</span><span class="sxs-lookup"><span data-stu-id="7b82e-109">Create an initial migration</span></span>
> * <span data-ttu-id="7b82e-110">Up 및 Down 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="7b82e-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="7b82e-111">데이터 모델 스냅샷에 대해 알아보기</span><span class="sxs-lookup"><span data-stu-id="7b82e-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="7b82e-112">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="7b82e-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b82e-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="7b82e-113">Prerequisites</span></span>

* [<span data-ttu-id="7b82e-114">정렬, 필터링 및 페이징</span><span class="sxs-lookup"><span data-stu-id="7b82e-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="7b82e-115">마이그레이션 정보</span><span class="sxs-lookup"><span data-stu-id="7b82e-115">About migrations</span></span>

<span data-ttu-id="7b82e-116">새 애플리케이션을 개발하는 경우 데이터 모델은 자주 변경되며 모델이 변경될 때마다 데이터베이스와 동기화를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="7b82e-117">데이터베이스가 존재하지 않는 경우 Entity Framework를 구성하여 만들면서 이러한 자습서를 시작하였습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="7b82e-118">그런 다음, 엔터티 클래스를 추가, 제거 또는 변경하거나 DbContext 클래스를 변경하면서 데이터 모델을 변경할 때마다 데이터베이스를 삭제할 수 있습니다. 또한 EF는 모델에 일치하는 새로운 항목을 만들고 테스트 데이터로 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="7b82e-119">데이터베이스를 데이터 모델과 동기화된 상태로 유지하는 이 메서드는 애플리케이션을 프로덕션 환경에 배포할 때까지 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="7b82e-120">애플리케이션이 프로덕션 환경에서 실행 중인 경우, 일반적으로 새 열을 추가하는 것처럼 사용자가 변경할 때마다 유지하려는 데이터 및 손실하지 않으려는 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="7b82e-121">EF Core 마이그레이션 기능은 EF가 새 데이터베이스를 만드는 대신 데이터베이스 스키마를 업데이트하도록 설정하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="7b82e-122">마이그레이션을 수행하기 위해 **PMC(패키지 관리자 콘솔)** 또는 CLI를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="7b82e-123">이러한 자습서에는 CLI 명령을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="7b82e-124">PMC에 대한 정보는 [이 자습서의 마지막](#pmc)에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="7b82e-125">데이터베이스 삭제</span><span class="sxs-lookup"><span data-stu-id="7b82e-125">Drop the database</span></span>

<span data-ttu-id="7b82e-126">EF Core 도구를 [전역 도구](/ef/core/miscellaneous/cli/dotnet)로서 설치하고 데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-126">Install EF Core tools as a [global tool](/ef/core/miscellaneous/cli/dotnet) and delete the database:</span></span>

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

<span data-ttu-id="7b82e-127">다음 섹션에서는 CLI 명령을 실행하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-127">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="7b82e-128">초기 마이그레이션 만들기</span><span class="sxs-lookup"><span data-stu-id="7b82e-128">Create an initial migration</span></span>

<span data-ttu-id="7b82e-129">변경 내용을 저장하고 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-129">Save your changes and build the project.</span></span> <span data-ttu-id="7b82e-130">그런 다음, 명령 창을 열고 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-130">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="7b82e-131">작업을 수행하는 빠른 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-131">Here's a quick way to do that:</span></span>

* <span data-ttu-id="7b82e-132">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **파일 탐색기에서 폴더 열기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-132">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![파일 탐색기에서 열기 메뉴 항목](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="7b82e-134">주소 표시줄에 “cmd”를 입력하고 Enter 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-134">Enter "cmd" in the address bar and press Enter.</span></span>

  ![열기 명령 창](migrations/_static/open-command-window.png)

<span data-ttu-id="7b82e-136">명령 창에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-136">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="7b82e-137">위의 명령에서는 다음과 유사한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-137">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="7b82e-138">“*cannot access the file ... ContosoUniversity.dll because it is being used by another process.* (다른 프로세스에서 사용 중이므로 ContosoUniversity.dll 파일에 액세스할 수 없음.)” 오류 메시지가 표시되면 Windows 시스템 트레이에서 IIS Express 아이콘을 찾아 마우스 오른쪽 단추로 클릭한 다음, **ContosoUniversity > 사이트 중단** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-138">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="7b82e-139">Up 및 Down 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="7b82e-139">Examine Up and Down methods</span></span>

<span data-ttu-id="7b82e-140">`migrations add` 명령을 실행하면 EF는 데이터베이스를 처음부터 만드는 코드를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-140">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="7b82e-141">이 코드는 *\<timestamp>_InitialCreate.cs* 라는 파일의 *Migrations* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-141">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="7b82e-142">다음 예제와 같이 `InitialCreate` 클래스의 `Up` 메서드는 데이터 모델 엔터티 집합에 해당하는 데이터베이스 테이블을 만들고 `Down` 메서드는 이를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-142">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="7b82e-143">마이그레이션에서는 마이그레이션을 위한 데이터 모델 변경을 구현하기 위해 `Up` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-143">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="7b82e-144">업데이트를 롤백하는 명령을 입력하면 마이그레이션에서 `Down` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-144">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="7b82e-145">이 코드는 `migrations add InitialCreate` 명령을 입력했을 때 만들어진 초기 마이그레이션을 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-145">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="7b82e-146">파일 이름에는 마이그레이션 이름 매개 변수(이 예제에서 “InitialCreate”)가 사용되며 사용자가 원하는 이름일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-146">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="7b82e-147">마이그레이션에서 수행 중인 작업을 요약한 단어 또는 구를 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-147">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="7b82e-148">예를 들어 이후 마이그레이션의 이름을 “AddDepartmentTable”로 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-148">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="7b82e-149">데이터베이스가 이미 존재할 때 초기 마이그레이션을 만든 경우 데이터베이스 만들기 코드가 생성되지만 데이터베이스는 이미 데이터 모델과 일치하기 때문에 실행할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-149">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="7b82e-150">데이터베이스가 아직 없는 다른 환경에 앱을 배포하는 경우 이 코드를 실행하여 데이터베이스를 만들기 때문에 먼저 테스트하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-150">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="7b82e-151">바로 이것이 앞서 연결 문자열의 데이터베이스 이름을 변경한 이유입니다. 따라서 해당 마이그레이션은 처음부터 새로운 데이터베이스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-151">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="7b82e-152">데이터 모델 스냅샷</span><span class="sxs-lookup"><span data-stu-id="7b82e-152">The data model snapshot</span></span>

<span data-ttu-id="7b82e-153">마이그레이션은 현재 데이터베이스 스키마의 *스냅숏* 을 *Migrations/SchoolContextModelSnapshot.cs* 에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-153">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="7b82e-154">마이그레이션을 추가하면 EF가 데이터 모델을 스냅샷 파일과 비교하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-154">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="7b82e-155">마이그레이션을 삭제하려면 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-155">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="7b82e-156">`dotnet ef migrations remove`는 마이그레이션을 삭제하고 스냅샷이 올바르게 다시 설정되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-156">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="7b82e-157">`dotnet ef migrations remove`가 실패하는 경우 `dotnet ef migrations remove -v`를 사용하여 자세한 오류 정보를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="7b82e-157">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="7b82e-158">스냅샷 파일을 사용하는 방법에 대한 자세한 내용은 [팀 환경의 EF Core 마이그레이션](/ef/core/managing-schemas/migrations/teams)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b82e-158">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="7b82e-159">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="7b82e-159">Apply the migration</span></span>

<span data-ttu-id="7b82e-160">명령 창에서 다음 명령을 입력하여 데이터베이스 및 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-160">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="7b82e-161">명령의 출력은 데이터베이스를 설정하는 SQL 명령에 대한 로그가 표시되는 것 외에 `migrations add` 명령과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-161">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="7b82e-162">대부분의 로그는 다음 예제 출력에서 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-162">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="7b82e-163">로그 메시지의 세부 수준을 줄이는 것을 선호하는 경우 *appsettings.Development.json* 파일에서 로그 수준을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-163">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="7b82e-164">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b82e-164">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="7b82e-165">**SQL Server 개체 탐색기** 를 사용하여 첫 번째 자습서에서와 같이 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-165">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="7b82e-166">데이터베이스에 어떤 마이그레이션이 적용되는지를 추적하는 \_\_EFMigrationsHistory 테이블이 추가된 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-166">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="7b82e-167">해당 테이블의 데이터를 보면 첫 번째 마이그레이션에 대해 한 행이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-167">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="7b82e-168">(앞의 CLI 출력 예제의 마지막 로그는 이 행을 만드는 INSERT 문을 보여 줍니다.)</span><span class="sxs-lookup"><span data-stu-id="7b82e-168">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="7b82e-169">애플리케이션을 실행하여 모든 항목이 여전히 이전과 동일하게 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-169">Run the application to verify that everything still works the same as before.</span></span>

![학생 인덱스 페이지](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="7b82e-171">CLI 및 PMC 비교</span><span class="sxs-lookup"><span data-stu-id="7b82e-171">Compare CLI and PMC</span></span>

<span data-ttu-id="7b82e-172">마이그레이션 관리를 위한 EF 도구는 Visual Studio **PMC(패키지 관리자 콘솔)** 에서 PowerShell cmdlet 또는 .NET Core CLI 명령에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-172">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="7b82e-173">이 자습서에는 CLI를 사용하는 방법이 나와 있지만 원하는 경우에 PMC를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-173">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="7b82e-174">PMC 명령을 위한 EF 명령은 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-174">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="7b82e-175">이 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있으므로 앱에 `Microsoft.AspNetCore.App`에 대한 패키지 참조가 있는 경우 패키지 참조를 추가할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-175">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="7b82e-176">**중요:** *.csproj* 파일을 편집하여 CLI에 대해 설치한 것과는 다른 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-176">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="7b82e-177">끝이 `Tools.DotNet`으로 끝나는 CLI 패키지 이름과 달리 이름이 `Tools`로 끝납니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-177">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="7b82e-178">CLI 명령에 대한 자세한 내용은 [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b82e-178">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="7b82e-179">PMC 명령에 대한 자세한 내용은 [패키지 관리자 콘솔(Visual Studio)](/ef/core/miscellaneous/cli/powershell)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b82e-179">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="7b82e-180">코드 가져오기</span><span class="sxs-lookup"><span data-stu-id="7b82e-180">Get the code</span></span>

[<span data-ttu-id="7b82e-181">완성된 애플리케이션을 다운로드하거나 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-181">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="7b82e-182">다음 단계</span><span class="sxs-lookup"><span data-stu-id="7b82e-182">Next step</span></span>

<span data-ttu-id="7b82e-183">데이터 모델 확장에 관한 더 많은 고급 항목을 살펴보려면 다음 자습서로 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-183">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="7b82e-184">방식에 따라 추가 마이그레이션을 만들고 적용하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b82e-184">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7b82e-185">추가 마이그레이션 만들기 및 적용</span><span class="sxs-lookup"><span data-stu-id="7b82e-185">Create and apply additional migrations</span></span>](complex-data-model.md)
