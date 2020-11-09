---
title: 'ASP.NET Membership authentication에서 ASP.NET Core 2.0으로 마이그레이션 Identity'
author: isaac2004
description: 'ASP.NET Core 2.0으로 멤버 자격 인증을 사용 하 여 기존 ASP.NET apps를 마이그레이션하는 방법에 대해 알아봅니다 Identity .'
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051358"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="412fc-103">ASP.NET Membership authentication에서 ASP.NET Core 2.0으로 마이그레이션 Identity</span><span class="sxs-lookup"><span data-stu-id="412fc-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="412fc-104">작성자: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="412fc-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="412fc-105">이 문서에서는 ASP.NET Core 2.0에 멤버 자격 인증을 사용 하 여 ASP.NET apps에 대 한 데이터베이스 스키마를 마이그레이션하는 방법을 보여 줍니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="412fc-106">이 문서에서는 ASP.NET 멤버 자격 기반 응용 프로그램의 데이터베이스 스키마를에 사용 되는 데이터베이스 스키마로 마이그레이션하는 데 필요한 단계를 제공 합니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="412fc-107">ASP.NET 멤버 기반 인증에서 ASP.NET로 마이그레이션하는 방법에 대 한 자세한 내용은 Identity [SQL 멤버 자격에서 ASP.NET Identity 로 기존 앱 마이그레이션 ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412fc-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="412fc-108">에 대 한 자세한 내용은 ASP.NET Core Identity [ Identity ASP.NET Core 소개를](xref:security/authentication/identity)참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412fc-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="412fc-109">멤버 자격 스키마 검토</span><span class="sxs-lookup"><span data-stu-id="412fc-109">Review of Membership schema</span></span>

<span data-ttu-id="412fc-110">ASP.NET 2.0 이전에는 개발자가 앱에 대 한 전체 인증 및 권한 부여 프로세스를 만드는 작업을 수행 했습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="412fc-111">ASP.NET 2.0를 사용 하 여 멤버 자격이 도입 되었으므로 ASP.NET apps 내에서 보안을 처리 하는 상용구 솔루션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="412fc-112">개발자는 이제 명령을 사용 하 여 스키마를 SQL Server 데이터베이스로 부트스트랩 할 수 있었습니다 <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> .</span><span class="sxs-lookup"><span data-stu-id="412fc-112">Developers were now able to bootstrap a schema into a SQL Server database with the <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> command.</span></span> <span data-ttu-id="412fc-113">이 명령을 실행 하면 데이터베이스에서 다음 테이블이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-113">After running this command, the following tables were created in the database.</span></span>

  ![멤버 자격 테이블](identity/_static/membership-tables.png)

<span data-ttu-id="412fc-115">기존 앱을 ASP.NET Core 2.0로 마이그레이션하려면 Identity 이러한 테이블의 데이터를 새 스키마에서 사용 하는 테이블로 마이그레이션해야 합니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="412fc-116">ASP.NET Core Identity 2.0 스키마</span><span class="sxs-lookup"><span data-stu-id="412fc-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="412fc-117">ASP.NET Core 2.0은 [Identity](/aspnet/identity/index) ASP.NET 4.5에 도입 된 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="412fc-118">원칙이 공유 되기는 하지만 프레임 워크 간의 구현은 ASP.NET Core 버전 간에도 다릅니다 ( [인증 마이그레이션 및 Identity ASP.NET Core 2.0](xref:migration/1x-to-2x/index)참조).</span><span class="sxs-lookup"><span data-stu-id="412fc-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="412fc-119">ASP.NET Core 2.0에 대 한 스키마를 보는 가장 빠른 방법은 Identity 새 ASP.NET Core 2.0 앱을 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="412fc-120">Visual Studio 2017에서 다음 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="412fc-121">**File** > **New** > **Project** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-121">Select **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="412fc-122">*핵심 Identity 샘플* 이라는 새 **ASP.NET Core 웹 응용 프로그램** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample* .</span></span>
1. <span data-ttu-id="412fc-123">드롭다운 목록에서 **ASP.NET Core 2.0** 을 선택 하 고 **웹 응용 프로그램** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application** .</span></span> <span data-ttu-id="412fc-124">이 템플릿은 [ Razor 페이지](xref:razor-pages/index) 앱을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="412fc-125">**확인** 을 클릭 하기 전에 **인증 변경** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-125">Before clicking **OK** , click **Change Authentication** .</span></span>
1. <span data-ttu-id="412fc-126">템플릿에 대 한 **개별 사용자 계정을** 선택 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="412fc-127">마지막으로 **확인** 을 클릭 한 다음 **확인** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-127">Finally, click **OK** , then **OK** .</span></span> <span data-ttu-id="412fc-128">Visual Studio는 템플릿을 사용 하 여 프로젝트를 만듭니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="412fc-129">**도구**  >  **NuGet 패키지 관리자**  >  **패키지 관리자 콘솔** 을 선택 하 여 PMC ( **패키지 관리자 콘솔** ) 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="412fc-130">PMC의 프로젝트 루트로 이동 하 고 [EF (Entity Framework) Core](/ef/core) `Update-Database` 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="412fc-131">ASP.NET Core 2.0는 Identity EF Core를 사용 하 여 인증 데이터를 저장 하는 데이터베이스와 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="412fc-132">새로 만든 앱이 작동 하려면이 데이터를 저장할 데이터베이스가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="412fc-133">새 앱을 만든 후 데이터베이스 환경에서 스키마를 검사 하는 가장 빠른 방법은 [EF Core 마이그레이션을](/ef/core/managing-schemas/migrations/)사용 하 여 데이터베이스를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="412fc-134">이 프로세스는 로컬 또는 다른 위치에 있는 데이터베이스를 만들어 해당 스키마를 모방 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="412fc-135">자세한 내용은 위의 설명서를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="412fc-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="412fc-136">EF Core 명령은에 지정 된 데이터베이스에 대 한 연결 문자열을 사용 *appsettings.json* 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-136">EF Core commands use the connection string for the database specified in *appsettings.json* .</span></span> <span data-ttu-id="412fc-137">다음 연결 문자열은 이름이 *.asp* 인 *localhost* 의 데이터베이스를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-137">The following connection string targets a database on *localhost* named *asp-net-core-identity* .</span></span> <span data-ttu-id="412fc-138">이 설정에서는 EF Core 연결 문자열을 사용 하도록 구성 됩니다 `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="412fc-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="412fc-139">SQL Server 개체 탐색기 **보기**  >  **SQL Server Object Explorer** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-139">Select **View** > **SQL Server Object Explorer** .</span></span> <span data-ttu-id="412fc-140">의 속성에 지정 된 데이터베이스 이름에 해당 하는 노드를 확장 `ConnectionStrings:DefaultConnection` *appsettings.json* 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json* .</span></span>

    <span data-ttu-id="412fc-141">`Update-Database`명령은 스키마를 사용 하 여 지정 된 데이터베이스와 앱 초기화에 필요한 모든 데이터를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="412fc-142">다음 그림에서는 앞의 단계를 사용 하 여 만든 테이블 구조를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![::: no loc (Id)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="412fc-144">스키마 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="412fc-144">Migrate the schema</span></span>

<span data-ttu-id="412fc-145">멤버 자격과의 테이블 구조와 필드에는 약간의 차이가 있습니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="412fc-146">이 패턴은 ASP.NET 및 ASP.NET Core apps를 사용 하 여 인증/권한 부여에 대해 크게 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="412fc-147">에서 여전히 사용 되는 키 개체는 Identity *사용자* 및 *역할* 입니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-147">The key objects that are still used with Identity are *Users* and *Roles* .</span></span> <span data-ttu-id="412fc-148">*사용자* , *역할* 및 *UserRoles* 에 대 한 매핑 테이블은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-148">Here are mapping tables for *Users* , *Roles* , and *UserRoles* .</span></span>

### <a name="users"></a><span data-ttu-id="412fc-149">사용자</span><span class="sxs-lookup"><span data-stu-id="412fc-149">Users</span></span>

|Identity<br><span data-ttu-id="412fc-150">( `dbo.AspNetUsers` ) 열</span><span class="sxs-lookup"><span data-stu-id="412fc-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="412fc-151">형식</span><span class="sxs-lookup"><span data-stu-id="412fc-151">Type</span></span>     |<span data-ttu-id="412fc-152">Membership</span><span class="sxs-lookup"><span data-stu-id="412fc-152">Membership</span></span><br><span data-ttu-id="412fc-153">( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) 열</span><span class="sxs-lookup"><span data-stu-id="412fc-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="412fc-154">형식</span><span class="sxs-lookup"><span data-stu-id="412fc-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="412fc-155">일부 필드 매핑은 멤버 자격과의 일 대 일 관계와 비슷합니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="412fc-156">위의 표에서는 기본 멤버 자격 사용자 스키마를 사용 하 여 스키마에 매핑합니다 ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="412fc-157">멤버 자격에 사용 된 다른 모든 사용자 지정 필드는 수동으로 매핑되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="412fc-158">이 매핑에서는 암호 조건과 암호 salts 둘 사이에서 마이그레이션되지 않으므로 암호에 대 한 맵이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="412fc-159">**암호가 null로 유지 되 고 사용자에 게 암호를 재설정 하도록 요청 하는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="412fc-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="412fc-160">에서 ASP.NET Core Identity `LockoutEnd` 사용자가 잠긴 경우에는 나중에 특정 날짜로 설정 해야 합니다. 이는 마이그레이션 스크립트에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="412fc-161">역할</span><span class="sxs-lookup"><span data-stu-id="412fc-161">Roles</span></span>

|Identity<br><span data-ttu-id="412fc-162">( `dbo.AspNetRoles` ) 열</span><span class="sxs-lookup"><span data-stu-id="412fc-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="412fc-163">형식</span><span class="sxs-lookup"><span data-stu-id="412fc-163">Type</span></span>|<span data-ttu-id="412fc-164">Membership</span><span class="sxs-lookup"><span data-stu-id="412fc-164">Membership</span></span><br><span data-ttu-id="412fc-165">( `dbo.aspnet_Roles` ) 열</span><span class="sxs-lookup"><span data-stu-id="412fc-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="412fc-166">형식</span><span class="sxs-lookup"><span data-stu-id="412fc-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="412fc-167">사용자 역할</span><span class="sxs-lookup"><span data-stu-id="412fc-167">User Roles</span></span>

|Identity<br><span data-ttu-id="412fc-168">( `dbo.AspNetUserRoles` ) 열</span><span class="sxs-lookup"><span data-stu-id="412fc-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="412fc-169">형식</span><span class="sxs-lookup"><span data-stu-id="412fc-169">Type</span></span>|<span data-ttu-id="412fc-170">Membership</span><span class="sxs-lookup"><span data-stu-id="412fc-170">Membership</span></span><br><span data-ttu-id="412fc-171">( `dbo.aspnet_UsersInRoles` ) 열</span><span class="sxs-lookup"><span data-stu-id="412fc-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="412fc-172">형식</span><span class="sxs-lookup"><span data-stu-id="412fc-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="412fc-173">*사용자* 및 *역할* 에 대 한 마이그레이션 스크립트를 만들 때 위의 매핑 테이블을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles* .</span></span> <span data-ttu-id="412fc-174">다음 예에서는 데이터베이스 서버에 데이터베이스가 두 개 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="412fc-175">하나의 데이터베이스에는 기존 ASP.NET 멤버 자격 스키마 및 데이터가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="412fc-176">다른 *핵심 Identity 샘플* 데이터베이스는 앞에서 설명한 단계를 사용 하 여 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="412fc-177">자세한 내용은 주석이 인라인으로 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="412fc-178">이전 스크립트를 완료 한 후 이전에 ASP.NET Core Identity 만든 앱은 멤버 자격 사용자로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="412fc-179">사용자는 로그인 하기 전에 암호를 변경 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="412fc-180">멤버 자격 시스템에 전자 메일 주소와 일치 하지 않는 사용자 이름을 가진 사용자가 있는 경우이를 수용 하기 위해 이전에 만든 앱을 변경 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="412fc-181">기본 템플릿에는 `UserName` 와 `Email` 가 동일한 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="412fc-182">서로 다른 경우에는 대신를 사용 하도록 로그인 프로세스를 수정 해야 합니다 `UserName` `Email` .</span><span class="sxs-lookup"><span data-stu-id="412fc-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="412fc-183">`PageModel` *Pages\Account\Login.cshtml.cs* 에 있는 로그인 페이지의에서 `[EmailAddress]` *전자 메일* 속성의 특성을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs* , remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="412fc-184">이름을 *UserName* 으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-184">Rename it to *UserName* .</span></span> <span data-ttu-id="412fc-185">이렇게 하려면 `EmailAddress` *보기* 및 *PageModel* 에서를 언급 하는 위치에 관계 없이 변경 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel* .</span></span> <span data-ttu-id="412fc-186">결과는 다음과 같이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="412fc-186">The result looks like the following:</span></span>

 ![고정 로그인](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="412fc-188">다음 단계</span><span class="sxs-lookup"><span data-stu-id="412fc-188">Next steps</span></span>

<span data-ttu-id="412fc-189">이 자습서에서는 SQL 멤버 자격에서 ASP.NET Core 2.0으로 사용자를 이식 하는 방법에 대해 알아보았습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="412fc-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="412fc-190">에 대 한 자세한 내용은 ASP.NET Core Identity [소개를 Identity ](xref:security/authentication/identity)참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="412fc-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
