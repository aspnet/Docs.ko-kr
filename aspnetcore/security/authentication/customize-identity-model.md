---
title: Identity ASP.NET Core에서 모델 사용자 지정
author: ajcvickers
description: 이 문서에서는에 대 한 기본 Entity Framework Core 데이터 모델을 사용자 지정 하는 방법을 설명 합니다 ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052047"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a>Identity ASP.NET Core에서 모델 사용자 지정

[Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity ASP.NET Core apps에서 사용자 계정을 관리 하 고 저장 하기 위한 프레임 워크를 제공 합니다. Identity 는 **개별 사용자 계정이** 인증 메커니즘으로 선택 된 경우 프로젝트에 추가 됩니다. 기본적으로에서는 Identity EF (Entity Framework) 핵심 데이터 모델을 사용 합니다. 이 문서에서는 모델을 사용자 지정 하는 방법을 설명 합니다 Identity .

## <a name="no-locidentity-and-ef-core-migrations"></a>Identity 마이그레이션 EF Core

모델을 검사 하기 전에에서 Identity [EF Core 마이그레이션을](/ef/core/managing-schemas/migrations/) 사용 하 여 데이터베이스를 만들고 업데이트 하는 방법을 이해 하는 것이 유용 합니다. 최상위 수준에서 프로세스는 다음과 같습니다.

1. [코드에서 데이터 모델](/ef/core/modeling/)을 정의 하거나 업데이트 합니다.
1. 마이그레이션을 추가 하 여이 모델을 데이터베이스에 적용할 수 있는 변경 내용으로 변환 합니다.
1. 마이그레이션이 제대로 진행 되는지 확인 합니다.
1. 마이그레이션을 적용 하 여 데이터베이스를 모델과 동기화 되도록 업데이트 합니다.
1. 1 ~ 4 단계를 반복 하 여 모델을 구체화 하 고 데이터베이스를 동기화 된 상태로 유지 합니다.

다음 방법 중 하나를 사용 하 여 마이그레이션을 추가 하 고 적용 합니다.

* Visual Studio를 사용 하는 경우 PMC ( **패키지 관리자 콘솔** ) 창 자세한 내용은 [EF CORE PMC tools](/ef/core/miscellaneous/cli/powershell)를 참조 하세요.
* 명령줄을 사용 하는 경우 .NET Core CLI입니다. 자세한 내용은 [EF Core .net 명령줄 도구](/ef/core/miscellaneous/cli/dotnet)를 참조 하세요.
* 앱이 실행 될 때 오류 페이지에서 **마이그레이션 적용** 단추를 클릭 합니다.

ASP.NET Core에는 개발 시간 오류 페이지 처리기가 있습니다. 처리기는 앱이 실행 될 때 마이그레이션을 적용할 수 있습니다. 프로덕션 앱은 일반적으로 마이그레이션에서 SQL 스크립트를 생성 하 고 데이터베이스 변경 내용을 제어 된 앱 및 데이터베이스 배포의 일부로 배포 합니다.

를 사용 하 여 새 앱을 Identity 만든 경우 위의 1 단계와 2 단계는 이미 완료 된 것입니다. 즉, 초기 데이터 모델이 이미 존재 하 고 초기 마이그레이션이 프로젝트에 추가 되었습니다. 초기 마이그레이션은 여전히 데이터베이스에 적용 해야 합니다. 초기 마이그레이션은 다음 방법 중 하나를 통해 적용할 수 있습니다.

* `Update-Database`PMC에서를 실행 합니다.
* `dotnet ef database update`명령 셸에서를 실행 합니다.
* 앱이 실행 될 때 오류 페이지에서 **마이그레이션 적용** 단추를 클릭 합니다.

모델이 변경 되 면 위의 단계를 반복 합니다.

## <a name="the-no-locidentity-model"></a>Identity모델

### <a name="entity-types"></a>엔터티 형식

Identity모델은 다음 엔터티 형식으로 구성 됩니다.

|엔터티 유형|설명                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |사용자를 나타냅니다.                                         |
|`Role`     |역할을 나타냅니다.                                           |
|`UserClaim`|사용자가 소유 하는 클레임을 나타냅니다.                    |
|`UserToken`|사용자에 대 한 인증 토큰을 나타냅니다.               |
|`UserLogin`|사용자를 로그인에 연결 합니다.                              |
|`RoleClaim`|역할 내의 모든 사용자에 게 부여 되는 클레임을 나타냅니다.|
|`UserRole` |사용자와 역할을 연결 하는 조인 엔터티입니다.               |

### <a name="entity-type-relationships"></a>엔터티 형식 관계

[엔터티 형식은](#entity-types) 다음과 같은 방법으로 서로 관련 됩니다.

* 각각은 `User` 다를 수 있습니다 `UserClaims` .
* 각각은 `User` 다를 수 있습니다 `UserLogins` .
* 각각은 `User` 다를 수 있습니다 `UserTokens` .
* 각 `Role` 에는 여러 개의 연결 된가 있을 수 있습니다 `RoleClaims` .
* 각에는 `User` 여러 개의 연결 된가 있을 수 있으며 `Roles` , 각는 다 수 `Role` 와 연결 될 수 있습니다 `Users` . 이는 데이터베이스에서 조인 테이블이 필요한 다대다 관계입니다. 조인 테이블은 엔터티로 표시 됩니다 `UserRole` .

### <a name="default-model-configuration"></a>기본 모델 구성

Identity모델을 구성 하 고 사용 하기 위해 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 에서 상속 되는 여러 *컨텍스트 클래스* 를 정의 합니다. 이 구성은 컨텍스트 클래스의 [Onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) 메서드에서 [EF CORE Code First 흐름 API](/ef/core/modeling/) 를 사용 하 여 수행 됩니다. 기본 구성은 다음과 같습니다.

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

### <a name="model-generic-types"></a>모델 제네릭 형식

Identity 위에 나열 된 각 엔터티 형식에 대 한 기본 CLR ( [공용 언어 런타임](/dotnet/standard/glossary#clr) ) 형식을 정의 합니다. 이러한 형식에는 모두 접두사가 붙습니다 *Identity* .

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

이러한 형식을 직접 사용 하는 대신 형식을 앱 자체 형식에 대 한 기본 클래스로 사용할 수 있습니다. 에 `DbContext` 의해 정의 된 클래스는 Identity 제네릭 이며이는 모델에 있는 하나 이상의 엔터티 형식에 대해 서로 다른 CLR 형식을 사용할 수 있도록 합니다. 이러한 제네릭 형식을 사용 하 여 `User` PK (기본 키) 데이터 형식을 변경할 수도 있습니다.

역할 지원과 함께를 사용 하는 경우 Identity <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> 클래스를 사용 해야 합니다. 다음은 그 예입니다.

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

또한 역할 없이를 사용할 수 있습니다 Identity .이 경우에는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> 클래스를 사용 해야 합니다.

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

## <a name="customize-the-model"></a>모델 사용자 지정

모델 사용자 지정의 시작점은 적절 한 컨텍스트 형식에서 파생 하는 것입니다. [모델 제네릭 형식](#model-generic-types) 섹션을 참조 하세요. 이 컨텍스트 형식은 일반적으로으로 호출 되며 `ApplicationDbContext` ASP.NET Core 템플릿에서 생성 됩니다.

컨텍스트는 다음과 같은 두 가지 방법으로 모델을 구성 하는 데 사용 됩니다.

* 제네릭 형식 매개 변수에 대 한 엔터티 및 키 형식 제공
* `OnModelCreating`를 재정의 하 여 이러한 형식의 매핑을 수정 합니다.

재정의할 때 `OnModelCreating` 를 `base.OnModelCreating` 먼저 호출 해야 합니다. 재정의 구성은 다음에 호출 해야 합니다. 일반적으로 EF Core는 구성에 대 한 최신 wins 정책을 포함 합니다. 예를 들어 `ToTable` 엔터티 형식에 대 한 메서드가 먼저 한 테이블 이름으로 호출 된 후 나중에 다른 테이블 이름으로 다시 호출 되는 경우 두 번째 호출의 테이블 이름이 사용 됩니다.

### <a name="custom-user-data"></a>사용자 지정 사용자 데이터

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[사용자 지정 사용자 데이터](xref:security/authentication/add-user-data) 는에서 상속 하 여 지원 됩니다 `IdentityUser` . 이 형식의 이름을 다음과 같이 하는 것이 일반적인 방법입니다 `ApplicationUser` .

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

`ApplicationUser`형식을 컨텍스트에 대 한 제네릭 인수로 사용 합니다.

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

클래스에서를 재정의할 필요가 없습니다 `OnModelCreating` `ApplicationDbContext` . EF Core는 `CustomTag` 규칙에 따라 속성을 매핑합니다. 그러나 새 열을 만들려면 데이터베이스를 업데이트 해야 합니다 `CustomTag` . 열을 만들려면 마이그레이션을 추가 하 고에 설명 된 대로 데이터베이스를 업데이트 하 고 [ Identity 마이그레이션 EF Core](#identity-and-ef-core-migrations)합니다.

*Pages/Shared/_LoginPartial* 를 업데이트 하 고 `IdentityUser` 를로 바꿉니다 `ApplicationUser` .

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

*영역/ Identity / Identity HostingStartup.cs* 를 업데이트 하거나 `Startup.ConfigureServices` `IdentityUser` 로 대체 `ApplicationUser` 합니다.

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

ASP.NET Core 2.1 이상에서는 Identity 가 Razor 클래스 라이브러리로 제공 됩니다. 자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요. 따라서 앞의 코드에서를 호출 해야 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 합니다. 스 캐 폴더를 Identity 사용 하 여 프로젝트에 파일을 추가 하는 경우 Identity 에 대 한 호출을 제거 `AddDefaultUI` 합니다. 자세한 내용은 다음을 참조하십시오.

* [스 캐 폴드 Identity](xref:security/authentication/scaffold-identity)
* [사용자 지정 사용자 데이터 추가, 다운로드 및 삭제 Identity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>기본 키 유형 변경

데이터베이스를 만든 후 PK 열의 데이터 형식이 변경 되 면 많은 데이터베이스 시스템에 문제가 있을 수 있습니다. PK를 변경 하는 작업은 일반적으로 테이블을 삭제 하 고 다시 만드는 작업을 포함 합니다. 따라서 데이터베이스를 만들 때 초기 마이그레이션에서 키 유형을 지정 해야 합니다.

PK 종류를 변경 하려면 다음 단계를 수행 합니다.

1. PK 변경 전에 데이터베이스를 만든 경우 `Drop-Database` (PMC) 또는 `dotnet ef database drop` (.NET Core CLI)을 실행 하 여 삭제 합니다.
2. 데이터베이스 삭제를 확인 한 후에는 `Remove-Migration` (PMC) 또는 (.NET Core CLI)를 사용 하 여 초기 마이그레이션을 제거 `dotnet ef migrations remove` 합니다.
3. `ApplicationDbContext`에서 파생 되도록 클래스를 업데이트 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> 합니다. 의 새 키 유형을 지정 `TKey` 합니다. 예를 들어 `Guid` 키 유형을 사용 하려면 다음을 수행 합니다.

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

    위의 코드에서는 <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> 새 키 형식을 사용 하기 위해 및 제네릭 클래스를 지정 해야 합니다.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    위의 코드에서는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> 새 키 형식을 사용 하기 위해 및 제네릭 클래스를 지정 해야 합니다.

    ::: moniker-end

    `Startup.ConfigureServices` 일반 사용자를 사용 하도록 업데이트 해야 합니다.

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

4. 사용자 지정 클래스를 사용 하는 경우 `ApplicationUser` 에서 상속 하도록 클래스를 업데이트 `IdentityUser` 합니다. 다음은 그 예입니다.

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    `ApplicationDbContext`사용자 지정 클래스를 참조 하도록 업데이트 합니다 `ApplicationUser` .

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

    에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.

    ASP.NET Core 2.1 이상에서는 Identity 가 Razor 클래스 라이브러리로 제공 됩니다. 자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요. 따라서 앞의 코드에서를 호출 해야 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 합니다. 스 캐 폴더를 Identity 사용 하 여 프로젝트에 파일을 추가 하는 경우 Identity 에 대 한 호출을 제거 `AddDefaultUI` 합니다.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>메서드는 `TKey` 기본 키의 데이터 형식을 나타내는 형식을 허용 합니다.

    ::: moniker-end

5. 사용자 지정 클래스를 사용 하는 경우 `ApplicationRole` 에서 상속 하도록 클래스를 업데이트 `IdentityRole<TKey>` 합니다. 다음은 그 예입니다.

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    `ApplicationDbContext`사용자 지정 클래스를 참조 하도록 업데이트 `ApplicationRole` 합니다. 예를 들어, 다음 클래스는 사용자 지정 `ApplicationUser` 및 사용자 지정를 참조 합니다 `ApplicationRole` .

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.

    ASP.NET Core 2.1 이상에서는 Identity 가 Razor 클래스 라이브러리로 제공 됩니다. 자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요. 따라서 앞의 코드에서를 호출 해야 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> 합니다. 스 캐 폴더를 Identity 사용 하 여 프로젝트에 파일을 추가 하는 경우 Identity 에 대 한 호출을 제거 `AddDefaultUI` 합니다.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 개체를 분석 하 여 기본 키의 데이터 형식을 유추 합니다.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    에서 서비스를 추가할 때 사용자 지정 데이터베이스 컨텍스트 클래스를 등록 합니다 Identity `Startup.ConfigureServices` .

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>메서드는 `TKey` 기본 키의 데이터 형식을 나타내는 형식을 허용 합니다.

    ::: moniker-end

### <a name="add-navigation-properties"></a>탐색 속성 추가

관계에 대 한 모델 구성을 변경 하면 다른 변경을 수행 하는 것 보다 더 어려울 수 있습니다. 새 추가 관계를 만드는 대신 기존 관계를 바꾸려면 주의 해야 합니다. 특히 변경 된 관계는 동일한 FK (외래 키) 속성을 기존 관계로 지정 해야 합니다. 예를 들어 및 간의 관계 `Users` 는 `UserClaims` 기본적으로 다음과 같이 지정 됩니다.

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

이 관계의 FK는 속성으로 지정 됩니다 `UserClaim.UserId` . `HasMany` 및 `WithOne` 는 탐색 속성 없이 관계를 만들기 위해 인수 없이 호출 됩니다.

`ApplicationUser`사용자가 연결 된를 참조할 수 있도록 탐색 속성을에 추가 합니다 `UserClaims` .

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`TKey`의는 `IdentityUserClaim<TKey>` PK 사용자에 대해 지정 된 유형입니다. 이 경우 `TKey` 는 `string` 기본값이 사용 되 고 있기 때문입니다. 엔터티 형식 **not** 에 대 한 PK 형식이 아닙니다 `UserClaim` .

이제 탐색 속성이 있으므로에서 구성 해야 합니다 `OnModelCreating` .

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

관계는에 대 한 호출에 지정 된 탐색 속성을 사용 하는 것과 정확히 동일 하 게 구성 됩니다 `HasMany` .

탐색 속성은 데이터베이스가 아니라 EF 모델에만 존재 합니다. 관계에 대 한 FK가 변경 되지 않았기 때문에 이러한 종류의 모델 변경에는 데이터베이스를 업데이트할 필요가 없습니다. 변경을 수행한 후 마이그레이션을 추가 하 여이를 확인할 수 있습니다. `Up`및 `Down` 메서드는 비어 있습니다.

### <a name="add-all-user-navigation-properties"></a>모든 사용자 탐색 속성 추가

위의 섹션을 지침으로 사용 하 여 다음 예제에서는 사용자의 모든 관계에 대 한 단방향 탐색 속성을 구성 합니다.

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

### <a name="add-user-and-role-navigation-properties"></a>사용자 및 역할 탐색 속성 추가

위의 섹션을 지침으로 사용 하 여 다음 예제에서는 사용자 및 역할에 대 한 모든 관계에 대 한 탐색 속성을 구성 합니다.

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

참고:

* 또한이 예제에는 `UserRole` 사용자에서 역할로 다 대 다 관계를 탐색 하는 데 필요한 조인 엔터티만 포함 되어 있습니다.
* `Application{...}`현재 형식이 형식 대신 사용 되는 것을 반영 하도록 탐색 속성의 형식을 변경 해야 합니다 `Identity{...}` .
* `Application{...}`제네릭 정의에서를 사용 해야 합니다 `ApplicationContext` .

### <a name="add-all-navigation-properties"></a>모든 탐색 속성 추가

위의 섹션을 지침으로 사용 하 여 다음 예제에서는 모든 엔터티 형식의 모든 관계에 대 한 탐색 속성을 구성 합니다.

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

### <a name="use-composite-keys"></a>복합 키 사용

이전 섹션에서는 모델에 사용 된 키의 유형을 변경 하는 방법을 보여 줍니다 Identity . Identity복합 키를 사용 하도록 키 모델을 변경 하는 것은 지원 되지 않거나 권장 되지 않습니다. 에서 복합 키를 사용 하는 경우 Identity Identity 관리자 코드가 모델과 상호 작용 하는 방식을 변경 해야 합니다. 이 사용자 지정은이 문서의 범위를 벗어났습니다.

### <a name="change-tablecolumn-names-and-facets"></a>테이블/열 이름 및 패싯 변경

테이블과 열의 이름을 변경 하려면를 호출 `base.OnModelCreating` 합니다. 그런 다음 구성을 추가 하 여 기본값을 재정의 합니다. 예를 들어 모든 테이블의 이름을 변경 하려면 Identity 다음을 수행 합니다.

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

이러한 예제에서는 기본 Identity 형식을 사용 합니다. 과 같은 앱 유형을 사용 하는 경우 `ApplicationUser` 기본 유형 대신 해당 유형을 구성 합니다.

다음 예에서는 일부 열 이름을 변경 합니다.

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

특정 *패싯을* 사용 하 여 일부 유형의 데이터베이스 열을 구성할 수 있습니다 (예: `string` 허용 되는 최대 길이). 다음 예에서는 모델의 여러 속성에 대해 열 최대 길이를 설정 합니다 `string` .

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

### <a name="map-to-a-different-schema"></a>다른 스키마에 매핑

스키마는 데이터베이스 공급자에서 다르게 동작할 수 있습니다. SQL Server의 경우 기본값은 *dbo* 스키마에 모든 테이블을 만드는 것입니다. 다른 스키마에서 테이블을 만들 수 있습니다. 다음은 그 예입니다.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>지연 로드

이 섹션에서는 모델의 지연 로드 프록시에 대 한 지원이 Identity 추가 되었습니다. 지연 로드는 탐색 속성을 먼저 로드 하지 않고 사용할 수 있기 때문에 유용 합니다.

엔터티 형식은 [EF Core 설명서](/ef/core/querying/related-data#lazy-loading)에 설명 된 대로 여러 가지 방법으로 지연 로드에 적합 하 게 만들 수 있습니다. 간단한 설명을 위해 다음이 필요한 지연 로드 프록시를 사용 합니다.

* [Microsoft.entityframeworkcore.tools.dotnet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) 패키지를 설치 합니다.
* Services.adddbcontext 내에서에 대 한 호출 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> 입니다. [ \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)
* 탐색 속성이 있는 공용 엔터티 형식 `public virtual` 입니다.

다음 예제에서는에서를 호출 하는 방법을 보여 줍니다 `UseLazyLoadingProxies` `Startup.ConfigureServices` .

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

엔터티 형식에 탐색 속성을 추가 하는 방법에 대 한 지침은 앞의 예제를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:security/authentication/scaffold-identity>

::: moniker-end
