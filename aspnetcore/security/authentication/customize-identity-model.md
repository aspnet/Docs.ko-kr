---
title: 'Identity ASP.NET Core에서 모델 사용자 지정'
author: ajcvickers
description: '이 문서에서는에 대 한 기본 Entity Framework Core 데이터 모델을 사용자 지정 하는 방법을 설명 합니다 ASP.NET Core Identity .'
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052047"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="39fd7-103">Identity ASP.NET Core에서 모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="39fd7-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="39fd7-104">[Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="39fd7-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="39fd7-105">ASP.NET Core Identity ASP.NET Core apps에서 사용자 계정을 관리 하 고 저장 하기 위한 프레임 워크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="39fd7-106">Identity 는 **개별 사용자 계정이** 인증 메커니즘으로 선택 된 경우 프로젝트에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="39fd7-107">기본적으로에서는 Identity EF (Entity Framework) 핵심 데이터 모델을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="39fd7-108">이 문서에서는 모델을 사용자 지정 하는 방법을 설명 합니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="39fd7-108">This article describes how to customize the Identity model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="39fd7-109">Identity 마이그레이션 EF Core</span><span class="sxs-lookup"><span data-stu-id="39fd7-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="39fd7-110">모델을 검사 하기 전에에서 Identity [EF Core 마이그레이션을](/ef/core/managing-schemas/migrations/) 사용 하 여 데이터베이스를 만들고 업데이트 하는 방법을 이해 하는 것이 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="39fd7-111">최상위 수준에서 프로세스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="39fd7-112">[코드에서 데이터 모델](/ef/core/modeling/)을 정의 하거나 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="39fd7-113">마이그레이션을 추가 하 여이 모델을 데이터베이스에 적용할 수 있는 변경 내용으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="39fd7-114">마이그레이션이 제대로 진행 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="39fd7-115">마이그레이션을 적용 하 여 데이터베이스를 모델과 동기화 되도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="39fd7-116">1 ~ 4 단계를 반복 하 여 모델을 구체화 하 고 데이터베이스를 동기화 된 상태로 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="39fd7-117">다음 방법 중 하나를 사용 하 여 마이그레이션을 추가 하 고 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="39fd7-118">Visual Studio를 사용 하는 경우 PMC ( **패키지 관리자 콘솔** ) 창</span><span class="sxs-lookup"><span data-stu-id="39fd7-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="39fd7-119">자세한 내용은 [EF CORE PMC tools](/ef/core/miscellaneous/cli/powershell)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="39fd7-120">명령줄을 사용 하는 경우 .NET Core CLI입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="39fd7-121">자세한 내용은 [EF Core .net 명령줄 도구](/ef/core/miscellaneous/cli/dotnet)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="39fd7-122">앱이 실행 될 때 오류 페이지에서 **마이그레이션 적용** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="39fd7-123">ASP.NET Core에는 개발 시간 오류 페이지 처리기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="39fd7-124">처리기는 앱이 실행 될 때 마이그레이션을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="39fd7-125">프로덕션 앱은 일반적으로 마이그레이션에서 SQL 스크립트를 생성 하 고 데이터베이스 변경 내용을 제어 된 앱 및 데이터베이스 배포의 일부로 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="39fd7-126">를 사용 하 여 새 앱을 Identity 만든 경우 위의 1 단계와 2 단계는 이미 완료 된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="39fd7-127">즉, 초기 데이터 모델이 이미 존재 하 고 초기 마이그레이션이 프로젝트에 추가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="39fd7-128">초기 마이그레이션은 여전히 데이터베이스에 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="39fd7-129">초기 마이그레이션은 다음 방법 중 하나를 통해 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="39fd7-130">`Update-Database`PMC에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="39fd7-131">`dotnet ef database update`명령 셸에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="39fd7-132">앱이 실행 될 때 오류 페이지에서 **마이그레이션 적용** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="39fd7-133">모델이 변경 되 면 위의 단계를 반복 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="39fd7-134">Identity모델</span><span class="sxs-lookup"><span data-stu-id="39fd7-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="39fd7-135">엔터티 형식</span><span class="sxs-lookup"><span data-stu-id="39fd7-135">Entity types</span></span>

<span data-ttu-id="39fd7-136">Identity모델은 다음 엔터티 형식으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="39fd7-137">엔터티 유형</span><span class="sxs-lookup"><span data-stu-id="39fd7-137">Entity type</span></span>|<span data-ttu-id="39fd7-138">설명</span><span class="sxs-lookup"><span data-stu-id="39fd7-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="39fd7-139">사용자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="39fd7-140">역할을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="39fd7-141">사용자가 소유 하는 클레임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="39fd7-142">사용자에 대 한 인증 토큰을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="39fd7-143">사용자를 로그인에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="39fd7-144">역할 내의 모든 사용자에 게 부여 되는 클레임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="39fd7-145">사용자와 역할을 연결 하는 조인 엔터티입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="39fd7-146">엔터티 형식 관계</span><span class="sxs-lookup"><span data-stu-id="39fd7-146">Entity type relationships</span></span>

<span data-ttu-id="39fd7-147">[엔터티 형식은](#entity-types) 다음과 같은 방법으로 서로 관련 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="39fd7-148">각각은 `User` 다를 수 있습니다 `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="39fd7-149">각각은 `User` 다를 수 있습니다 `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="39fd7-150">각각은 `User` 다를 수 있습니다 `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="39fd7-151">각 `Role` 에는 여러 개의 연결 된가 있을 수 있습니다 `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="39fd7-152">각에는 `User` 여러 개의 연결 된가 있을 수 있으며 `Roles` , 각는 다 수 `Role` 와 연결 될 수 있습니다 `Users` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="39fd7-153">이는 데이터베이스에서 조인 테이블이 필요한 다대다 관계입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="39fd7-154">조인 테이블은 엔터티로 표시 됩니다 `UserRole` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="39fd7-155">기본 모델 구성</span><span class="sxs-lookup"><span data-stu-id="39fd7-155">Default model configuration</span></span>

<span data-ttu-id="39fd7-156">Identity모델을 구성 하 고 사용 하기 위해 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 에서 상속 되는 여러 *컨텍스트 클래스* 를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="39fd7-157">이 구성은 컨텍스트 클래스의 [Onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) 메서드에서 [EF CORE Code First 흐름 API](/ef/core/modeling/) 를 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="39fd7-158">기본 구성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="39fd7-159">모델 제네릭 형식</span><span class="sxs-lookup"><span data-stu-id="39fd7-159">Model generic types</span></span>

<span data-ttu-id="39fd7-160">Identity 위에 나열 된 각 엔터티 형식에 대 한 기본 CLR ( [공용 언어 런타임](/dotnet/standard/glossary#clr) ) 형식을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="39fd7-161">이러한 형식에는 모두 접두사가 붙습니다 *Identity* .</span><span class="sxs-lookup"><span data-stu-id="39fd7-161">These types are all prefixed with *Identity* :</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="39fd7-162">이러한 형식을 직접 사용 하는 대신 형식을 앱 자체 형식에 대 한 기본 클래스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="39fd7-163">에 `DbContext` 의해 정의 된 클래스는 Identity 제네릭 이며이는 모델에 있는 하나 이상의 엔터티 형식에 대해 서로 다른 CLR 형식을 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="39fd7-164">이러한 제네릭 형식을 사용 하 여 `User` PK (기본 키) 데이터 형식을 변경할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="39fd7-165">역할 지원과 함께를 사용 하는 경우 Identity <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> 클래스를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="39fd7-166">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="39fd7-167">또한 역할 없이를 사용할 수 있습니다 Identity .이 경우에는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> 클래스를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="39fd7-168">모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="39fd7-168">Customize the model</span></span>

<span data-ttu-id="39fd7-169">모델 사용자 지정의 시작점은 적절 한 컨텍스트 형식에서 파생 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="39fd7-170">[모델 제네릭 형식](#model-generic-types) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="39fd7-171">이 컨텍스트 형식은 일반적으로으로 호출 되며 `ApplicationDbContext` ASP.NET Core 템플릿에서 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="39fd7-172">컨텍스트는 다음과 같은 두 가지 방법으로 모델을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="39fd7-173">제네릭 형식 매개 변수에 대 한 엔터티 및 키 형식 제공</span><span class="sxs-lookup"><span data-stu-id="39fd7-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="39fd7-174">`OnModelCreating`를 재정의 하 여 이러한 형식의 매핑을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="39fd7-175">재정의할 때 `OnModelCreating` 를 `base.OnModelCreating` 먼저 호출 해야 합니다. 재정의 구성은 다음에 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="39fd7-176">일반적으로 EF Core는 구성에 대 한 최신 wins 정책을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="39fd7-177">예를 들어 `ToTable` 엔터티 형식에 대 한 메서드가 먼저 한 테이블 이름으로 호출 된 후 나중에 다른 테이블 이름으로 다시 호출 되는 경우 두 번째 호출의 테이블 이름이 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="39fd7-178">사용자 지정 사용자 데이터</span><span class="sxs-lookup"><span data-stu-id="39fd7-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="39fd7-179">[사용자 지정 사용자 데이터](xref:security/authentication/add-user-data) 는에서 상속 하 여 지원 됩니다 `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="39fd7-180">이 형식의 이름을 다음과 같이 하는 것이 일반적인 방법입니다 `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="39fd7-181">`ApplicationUser`형식을 컨텍스트에 대 한 제네릭 인수로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="39fd7-182">클래스에서를 재정의할 필요가 없습니다 `OnModelCreating` `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="39fd7-183">EF Core는 `CustomTag` 규칙에 따라 속성을 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="39fd7-184">그러나 새 열을 만들려면 데이터베이스를 업데이트 해야 합니다 `CustomTag` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="39fd7-185">열을 만들려면 마이그레이션을 추가 하 고에 설명 된 대로 데이터베이스를 업데이트 하 고 [ Identity 마이그레이션 EF Core](#identity-and-ef-core-migrations)합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="39fd7-186">*Pages/Shared/_LoginPartial* 를 업데이트 하 고 `IdentityUser` 를로 바꿉니다 `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="39fd7-187">*영역/ Identity / Identity HostingStartup.cs* 를 업데이트 하거나 `Startup.ConfigureServices` `IdentityUser` 로 대체 `ApplicationUser` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="39fd7-188">ASP.NET Core 2.1 이상에서는 Identity 가 Razor 클래스 라이브러리로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="39fd7-189">자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="39fd7-190">따라서 앞의 코드에서를 호출 해야 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="39fd7-191">스 캐 폴더를 Identity 사용 하 여 프로젝트에 파일을 추가 하는 경우 Identity 에 대 한 호출을 제거 `AddDefaultUI` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="39fd7-192">자세한 내용은 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="39fd7-192">For more information, see:</span></span>

* [<span data-ttu-id="39fd7-193">스 캐 폴드 Identity</span><span class="sxs-lookup"><span data-stu-id="39fd7-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="39fd7-194">사용자 지정 사용자 데이터 추가, 다운로드 및 삭제 Identity</span><span class="sxs-lookup"><span data-stu-id="39fd7-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="39fd7-195">기본 키 유형 변경</span><span class="sxs-lookup"><span data-stu-id="39fd7-195">Change the primary key type</span></span>

<span data-ttu-id="39fd7-196">데이터베이스를 만든 후 PK 열의 데이터 형식이 변경 되 면 많은 데이터베이스 시스템에 문제가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="39fd7-197">PK를 변경 하는 작업은 일반적으로 테이블을 삭제 하 고 다시 만드는 작업을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="39fd7-198">따라서 데이터베이스를 만들 때 초기 마이그레이션에서 키 유형을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="39fd7-199">PK 종류를 변경 하려면 다음 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="39fd7-200">PK 변경 전에 데이터베이스를 만든 경우 `Drop-Database` (PMC) 또는 `dotnet ef database drop` (.NET Core CLI)을 실행 하 여 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="39fd7-201">데이터베이스 삭제를 확인 한 후에는 `Remove-Migration` (PMC) 또는 (.NET Core CLI)를 사용 하 여 초기 마이그레이션을 제거 `dotnet ef migrations remove` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="39fd7-202">`ApplicationDbContext`에서 파생 되도록 클래스를 업데이트 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="39fd7-203">의 새 키 유형을 지정 `TKey` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="39fd7-204">예를 들어 `Guid` 키 유형을 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="39fd7-205">위의 코드에서는 <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> 새 키 형식을 사용 하기 위해 및 제네릭 클래스를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="39fd7-206">위의 코드에서는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> 새 키 형식을 사용 하기 위해 및 제네릭 클래스를 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="39fd7-207">`Startup.ConfigureServices` 일반 사용자를 사용 하도록 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="39fd7-208">사용자 지정 클래스를 사용 하는 경우 `ApplicationUser` 에서 상속 하도록 클래스를 업데이트 `IdentityUser` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="39fd7-209">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="39fd7-210">`ApplicationDbContext`사용자 지정 클래스를 참조 하도록 업데이트 합니다 `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="39fd7-211">에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="39fd7-212">[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="39fd7-213">ASP.NET Core 2.1 이상에서는 Identity 가 Razor 클래스 라이브러리로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="39fd7-214">자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="39fd7-215">따라서 앞의 코드에서를 호출 해야 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="39fd7-216">스 캐 폴더를 Identity 사용 하 여 프로젝트에 파일을 추가 하는 경우 Identity 에 대 한 호출을 제거 `AddDefaultUI` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="39fd7-217">[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="39fd7-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>메서드는 `TKey` 기본 키의 데이터 형식을 나타내는 형식을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="39fd7-219">사용자 지정 클래스를 사용 하는 경우 `ApplicationRole` 에서 상속 하도록 클래스를 업데이트 `IdentityRole<TKey>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="39fd7-220">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="39fd7-221">`ApplicationDbContext`사용자 지정 클래스를 참조 하도록 업데이트 `ApplicationRole` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="39fd7-222">예를 들어, 다음 클래스는 사용자 지정 `ApplicationUser` 및 사용자 지정를 참조 합니다 `ApplicationRole` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="39fd7-223">에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="39fd7-224">[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="39fd7-225">ASP.NET Core 2.1 이상에서는 Identity 가 Razor 클래스 라이브러리로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="39fd7-226">자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="39fd7-227">따라서 앞의 코드에서를 호출 해야 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="39fd7-228">스 캐 폴더를 Identity 사용 하 여 프로젝트에 파일을 추가 하는 경우 Identity 에 대 한 호출을 제거 `AddDefaultUI` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="39fd7-229">에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="39fd7-230">[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="39fd7-231">에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="39fd7-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>메서드는 `TKey` 기본 키의 데이터 형식을 나타내는 형식을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="39fd7-233">탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="39fd7-233">Add navigation properties</span></span>

<span data-ttu-id="39fd7-234">관계에 대 한 모델 구성을 변경 하면 다른 변경을 수행 하는 것 보다 더 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="39fd7-235">새 추가 관계를 만드는 대신 기존 관계를 바꾸려면 주의 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="39fd7-236">특히 변경 된 관계는 동일한 FK (외래 키) 속성을 기존 관계로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="39fd7-237">예를 들어 및 간의 관계 `Users` 는 `UserClaims` 기본적으로 다음과 같이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="39fd7-238">이 관계의 FK는 속성으로 지정 됩니다 `UserClaim.UserId` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="39fd7-239">`HasMany` 및 `WithOne` 는 탐색 속성 없이 관계를 만들기 위해 인수 없이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="39fd7-240">`ApplicationUser`사용자가 연결 된를 참조할 수 있도록 탐색 속성을에 추가 합니다 `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="39fd7-241">`TKey`의는 `IdentityUserClaim<TKey>` PK 사용자에 대해 지정 된 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="39fd7-242">이 경우 `TKey` 는 `string` 기본값이 사용 되 고 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="39fd7-243">엔터티 형식 **not** 에 대 한 PK 형식이 아닙니다 `UserClaim` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="39fd7-244">이제 탐색 속성이 있으므로에서 구성 해야 합니다 `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="39fd7-245">관계는에 대 한 호출에 지정 된 탐색 속성을 사용 하는 것과 정확히 동일 하 게 구성 됩니다 `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="39fd7-246">탐색 속성은 데이터베이스가 아니라 EF 모델에만 존재 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="39fd7-247">관계에 대 한 FK가 변경 되지 않았기 때문에 이러한 종류의 모델 변경에는 데이터베이스를 업데이트할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="39fd7-248">변경을 수행한 후 마이그레이션을 추가 하 여이를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="39fd7-249">`Up`및 `Down` 메서드는 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="39fd7-250">모든 사용자 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="39fd7-250">Add all User navigation properties</span></span>

<span data-ttu-id="39fd7-251">위의 섹션을 지침으로 사용 하 여 다음 예제에서는 사용자의 모든 관계에 대 한 단방향 탐색 속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="39fd7-252">사용자 및 역할 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="39fd7-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="39fd7-253">위의 섹션을 지침으로 사용 하 여 다음 예제에서는 사용자 및 역할에 대 한 모든 관계에 대 한 탐색 속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="39fd7-254">참고:</span><span class="sxs-lookup"><span data-stu-id="39fd7-254">Notes:</span></span>

* <span data-ttu-id="39fd7-255">또한이 예제에는 `UserRole` 사용자에서 역할로 다 대 다 관계를 탐색 하는 데 필요한 조인 엔터티만 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="39fd7-256">`Application{...}`현재 형식이 형식 대신 사용 되는 것을 반영 하도록 탐색 속성의 형식을 변경 해야 합니다 `Identity{...}` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `Identity{...}` types.</span></span>
* <span data-ttu-id="39fd7-257">`Application{...}`제네릭 정의에서를 사용 해야 합니다 `ApplicationContext` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="39fd7-258">모든 탐색 속성 추가</span><span class="sxs-lookup"><span data-stu-id="39fd7-258">Add all navigation properties</span></span>

<span data-ttu-id="39fd7-259">위의 섹션을 지침으로 사용 하 여 다음 예제에서는 모든 엔터티 형식의 모든 관계에 대 한 탐색 속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="39fd7-260">복합 키 사용</span><span class="sxs-lookup"><span data-stu-id="39fd7-260">Use composite keys</span></span>

<span data-ttu-id="39fd7-261">이전 섹션에서는 모델에 사용 된 키의 유형을 변경 하는 방법을 보여 줍니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="39fd7-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="39fd7-262">Identity복합 키를 사용 하도록 키 모델을 변경 하는 것은 지원 되지 않거나 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="39fd7-263">에서 복합 키를 사용 하는 경우 Identity Identity 관리자 코드가 모델과 상호 작용 하는 방식을 변경 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="39fd7-264">이 사용자 지정은이 문서의 범위를 벗어났습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="39fd7-265">테이블/열 이름 및 패싯 변경</span><span class="sxs-lookup"><span data-stu-id="39fd7-265">Change table/column names and facets</span></span>

<span data-ttu-id="39fd7-266">테이블과 열의 이름을 변경 하려면를 호출 `base.OnModelCreating` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="39fd7-267">그런 다음 구성을 추가 하 여 기본값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="39fd7-268">예를 들어 모든 테이블의 이름을 변경 하려면 Identity 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="39fd7-269">이러한 예제에서는 기본 Identity 형식을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-269">These examples use the default Identity types.</span></span> <span data-ttu-id="39fd7-270">과 같은 앱 유형을 사용 하는 경우 `ApplicationUser` 기본 유형 대신 해당 유형을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="39fd7-271">다음 예에서는 일부 열 이름을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="39fd7-272">특정 *패싯을* 사용 하 여 일부 유형의 데이터베이스 열을 구성할 수 있습니다 (예: `string` 허용 되는 최대 길이).</span><span class="sxs-lookup"><span data-stu-id="39fd7-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="39fd7-273">다음 예에서는 모델의 여러 속성에 대해 열 최대 길이를 설정 합니다 `string` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="39fd7-274">다른 스키마에 매핑</span><span class="sxs-lookup"><span data-stu-id="39fd7-274">Map to a different schema</span></span>

<span data-ttu-id="39fd7-275">스키마는 데이터베이스 공급자에서 다르게 동작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="39fd7-276">SQL Server의 경우 기본값은 *dbo* 스키마에 모든 테이블을 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="39fd7-277">다른 스키마에서 테이블을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="39fd7-278">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="39fd7-279">지연 로드</span><span class="sxs-lookup"><span data-stu-id="39fd7-279">Lazy loading</span></span>

<span data-ttu-id="39fd7-280">이 섹션에서는 모델의 지연 로드 프록시에 대 한 지원이 Identity 추가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="39fd7-281">지연 로드는 탐색 속성을 먼저 로드 하지 않고 사용할 수 있기 때문에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="39fd7-282">엔터티 형식은 [EF Core 설명서](/ef/core/querying/related-data#lazy-loading)에 설명 된 대로 여러 가지 방법으로 지연 로드에 적합 하 게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="39fd7-283">간단한 설명을 위해 다음이 필요한 지연 로드 프록시를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="39fd7-284">[Microsoft.entityframeworkcore.tools.dotnet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="39fd7-285">Services.adddbcontext 내에서에 대 한 호출 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> 입니다. [ \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)</span><span class="sxs-lookup"><span data-stu-id="39fd7-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="39fd7-286">탐색 속성이 있는 공용 엔터티 형식 `public virtual` 입니다.</span><span class="sxs-lookup"><span data-stu-id="39fd7-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="39fd7-287">다음 예제에서는에서를 호출 하는 방법을 보여 줍니다 `UseLazyLoadingProxies` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="39fd7-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="39fd7-288">엔터티 형식에 탐색 속성을 추가 하는 방법에 대 한 지침은 앞의 예제를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="39fd7-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39fd7-289">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="39fd7-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
