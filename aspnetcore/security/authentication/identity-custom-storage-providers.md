---
title: 사용자 지정 저장소 공급자 ASP.NET Core Identity
author: ardalis
description: 에 대 한 사용자 지정 저장소 공급자를 구성 하는 방법을 알아봅니다 ASP.NET Core Identity .
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
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
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: f1c4366e4e4afa3dd86a816a649ad0a8b2ce817b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588629"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>사용자 지정 저장소 공급자 ASP.NET Core Identity

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET Core Identity 는 사용자 지정 저장소 공급자를 만들어 앱에 연결 하는 데 사용할 수 있는 확장 가능한 시스템입니다. 이 항목에서는에 대 한 사용자 지정 저장소 공급자를 만드는 방법에 대해 설명 합니다 ASP.NET Core Identity . 사용자 고유의 저장소 공급자를 만드는 데 중요 한 개념을 설명 하지만 단계별 연습은 아닙니다.

[GitHub에서 샘플 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample)

## <a name="introduction"></a>소개

기본적으로 시스템은 ASP.NET Core Identity Entity Framework Core를 사용 하 여 SQL Server 데이터베이스에 사용자 정보를 저장 합니다. 이 접근 방식은 많은 앱에서 잘 작동 합니다. 그러나 다른 지 속성 메커니즘 또는 데이터 스키마를 사용 하는 것이 좋습니다. 예를 들면 다음과 같습니다.

* [Azure Table Storage](/azure/storage/) 또는 다른 데이터 저장소를 사용 합니다.
* 데이터베이스 테이블의 구조가 다릅니다. 
* [Dapper](https://github.com/StackExchange/Dapper)와 같은 다른 데이터 액세스 방법을 사용할 수 있습니다. 

이러한 각 경우에서 저장소 메커니즘에 대 한 사용자 지정 공급자를 작성 하 고 해당 공급자를 앱에 연결할 수 있습니다.

ASP.NET Core Identity 는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio의 프로젝트 템플릿에 포함 되어 있습니다.

.NET Core CLI 사용 하는 경우 다음을 추가 합니다 `-au Individual` .

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core Identity아키텍처

ASP.NET Core Identity 는 관리자 및 저장소 라는 클래스로 구성 됩니다. *관리자* 는 앱 개발자가 사용자 만들기와 같은 작업을 수행 하는 데 사용 하는 상위 수준 클래스입니다 Identity . *저장소* 는 사용자 및 역할과 같은 엔터티를 유지 하는 방법을 지정 하는 하위 수준 클래스입니다. 저장소는 리포지토리 패턴을 따르고 지 속성 메커니즘과 긴밀 하 게 결합 됩니다. 관리자는 저장소에서 분리 됩니다. 즉, 구성을 제외 하 고 응용 프로그램 코드를 변경 하지 않고 지 속성 메커니즘을 바꿀 수 있습니다.

다음 다이어그램은 웹 앱이 관리자와 상호 작용 하는 방법을 보여 주며, 저장소는 데이터 액세스 계층과 상호 작용 합니다.

![ASP.NET Core 앱은 관리자와 함께 작동 합니다 (예: ' UserManager ', ' RoleManager '). 관리자는 Entity Framework Core와 같은 라이브러리를 사용 하 여 데이터 원본과 통신 하는 상점 (예: ' UserStore ')으로 작업 합니다.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

사용자 지정 저장소 공급자를 만들려면 데이터 원본, 데이터 액세스 계층 및이 데이터 액세스 계층과 상호 작용 하는 저장소 클래스 (위 다이어그램의 녹색 및 회색 상자)를 만듭니다. 관리자 또는 관리자와 상호 작용 하는 앱 코드 (위의 파란색 상자)를 사용자 지정할 필요가 없습니다.

의 새 인스턴스를 만들거나 `UserManager` `RoleManager` 사용자 클래스의 형식을 제공 하 고 저장소 클래스의 인스턴스를 인수로 전달 합니다. 이 방법을 사용 하면 사용자 지정 클래스를 ASP.NET Core에 연결할 수 있습니다. 

[새 저장소 공급자를 사용 하도록 앱 다시 구성](#reconfigure-app-to-use-a-new-storage-provider) `UserManager` `RoleManager` 사용자 지정 저장소로 및를 인스턴스화하는 방법을 보여 줍니다.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity 데이터 형식을 저장 합니다.

[ASP.NET Core Identity](https://github.com/aspnet/identity) 데이터 형식은 다음 섹션에 자세히 설명 되어 있습니다.

### <a name="users"></a>사용자

웹 사이트의 등록 된 사용자입니다. [ Identity 사용자](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) 형식을 사용자 지정 형식에 대 한 예로 사용 하거나 확장할 수 있습니다. 사용자 고유의 사용자 지정 id 저장소 솔루션을 구현 하기 위해 특정 형식에서 상속할 필요는 없습니다.

### <a name="user-claims"></a>사용자 클레임

사용자의 id를 나타내는 사용자에 대 한 문 (또는 [클레임](/dotnet/api/system.security.claims.claim)) 집합입니다. 에서는 역할을 통해 달성할 수 있는 것 보다 더 큰 사용자 id 식을 사용할 수 있습니다.

### <a name="user-logins"></a>사용자 로그인

사용자에 게 로그인 할 때 사용할 외부 인증 공급자 (예: Facebook 또는 Microsoft 계정)에 대 한 정보입니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>역할

사이트에 대 한 권한 부여 그룹. 역할 Id와 역할 이름 (예: "Admin" 또는 "Employee")을 포함 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>데이터 액세스 계층

이 항목에서는 사용자가 사용 하려는 지 속성 메커니즘 및 해당 메커니즘에 대 한 엔터티를 만드는 방법을 잘 알고 있다고 가정 합니다. 이 항목에서는 리포지토리 또는 데이터 액세스 클래스를 만드는 방법에 대 한 세부 정보를 제공 하지 않습니다. 에서 작업할 때 디자인 결정에 대 한 몇 가지 제안을 제공 ASP.NET Core Identity 합니다.

사용자 지정 저장소 공급자에 대 한 데이터 액세스 계층을 디자인할 때 자유롭게 자유롭게 사용할 수 있습니다. 앱에서 사용 하려는 기능에 대 한 지 속성 메커니즘도 만들어야 합니다. 예를 들어 앱에서 역할을 사용 하지 않는 경우 역할 또는 사용자 역할 연결에 대 한 저장소를 만들 필요가 없습니다. 기술 및 기존 인프라에는의 기본 구현과 매우 다른 구조가 필요할 수 있습니다 ASP.NET Core Identity . 데이터 액세스 계층에서 저장소 구현의 구조를 사용 하는 논리를 제공 합니다.

데이터 액세스 계층은 데이터 원본에 데이터를 저장 하는 논리를 제공 합니다 ASP.NET Core Identity . 사용자 지정 저장소 공급자에 대 한 데이터 액세스 계층에는 사용자 및 역할 정보를 저장 하는 다음과 같은 클래스가 포함 될 수 있습니다.

### <a name="context-class"></a>Context 클래스

정보를 캡슐화 하 여 지 속성 메커니즘에 연결 하 고 쿼리를 실행 합니다. 여러 데이터 클래스에는이 클래스의 인스턴스가 필요 합니다. 일반적으로 종속성 주입을 통해 제공 됩니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>사용자 저장소

사용자 정보 (예: 사용자 이름 및 암호 해시)를 저장 하 고 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>역할 저장소

역할 이름 등의 역할 정보를 저장 하 고 검색 합니다. [예제](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>UserClaims 저장소

사용자 클레임 정보 (예: 클레임 유형 및 값)를 저장 하 고 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins 저장소

사용자 로그인 정보 (예: 외부 인증 공급자)를 저장 하 고 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole 저장소

사용자에 게 할당 된 역할을 저장 하 고 검색 합니다. [예제](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**팁:** 앱에서 사용 하려는 클래스만 구현 합니다.

데이터 액세스 클래스에서 지 속성 메커니즘에 대 한 데이터 작업을 수행 하는 코드를 제공 합니다. 예를 들어 사용자 지정 공급자 내에서 *store* 클래스에 새 사용자를 만드는 다음 코드를 사용할 수 있습니다.

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

사용자를 만들기 위한 구현 논리는 `_usersTable.CreateAsync` 아래에 표시 된 메서드에 있습니다.

## <a name="customize-the-user-class"></a>사용자 클래스 사용자 지정

저장소 공급자를 구현 하는 경우 [ Identity 사용자 클래스](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)와 동일한 사용자 클래스를 만듭니다.

사용자 클래스에는 최소한 및 속성이 포함 되어야 합니다 `Id` `UserName` .

`IdentityUser`클래스는에서 요청 된 작업을 `UserManager` 수행할 때 호출 하는 속성을 정의 합니다. 속성의 기본 형식은 `Id` 문자열 이지만에서 상속 하 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 고 다른 형식을 지정할 수 있습니다. 프레임 워크는 저장소 구현이 데이터 형식 변환을 처리 하는 것으로 예상 합니다.

## <a name="customize-the-user-store"></a>사용자 저장소 사용자 지정

`UserStore`사용자에 대 한 모든 데이터 작업에 대해 메서드를 제공 하는 클래스를 만듭니다. 이 클래스는 [Userstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) 클래스와 동일 합니다. 클래스에서 `UserStore` `IUserStore<TUser>` 및 필요한 선택적 인터페이스를 구현 합니다. 앱에서 제공 하는 기능에 따라 구현할 선택적 인터페이스를 선택 합니다.

### <a name="optional-interfaces"></a>선택적 인터페이스

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

선택적 인터페이스는에서 상속 `IUserStore<TUser>` 합니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)에서 부분적으로 구현 된 샘플 사용자 저장소를 볼 수 있습니다.

클래스 내에서 `UserStore` 작업을 수행 하기 위해 만든 데이터 액세스 클래스를 사용 합니다. 이러한는 종속성 주입을 사용 하 여 전달 됩니다. 예를 들어, Dapper 구현이 포함 된 SQL Server 클래스에는 `UserStore` `CreateAsync` 의 인스턴스를 사용 하 여 `DapperUsersTable` 새 레코드를 삽입 하는 메서드가 있습니다.

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>사용자 저장소를 사용자 지정할 때 구현할 인터페이스

* **IUserStore**  
 [Iuserstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) 인터페이스는 사용자 저장소에서 구현 해야 하는 유일한 인터페이스입니다. 사용자를 만들고, 업데이트 하 고, 삭제 하 고, 검색 하는 메서드를 정의 합니다.
* **IUserClaimStore**  
 [Iuserclaimstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) 인터페이스는 사용자 클레임을 사용 하기 위해 구현 하는 메서드를 정의 합니다. 여기에는 사용자 클레임을 추가, 제거 및 검색 하는 메서드가 포함 됩니다.
* **IUserLoginStore**  
 [IUserLoginStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) 는 외부 인증 공급자를 사용 하도록 구현 하는 메서드를 정의 합니다. 사용자 로그인을 추가, 제거 및 검색 하는 메서드와 로그인 정보를 기반으로 사용자를 검색 하는 방법이 포함 되어 있습니다.
* **IUserRoleStore**  
 [Iuserrolestore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) 인터페이스는 사용자를 역할에 매핑하기 위해 구현 하는 메서드를 정의 합니다. 사용자의 역할을 추가, 제거 및 검색 하는 메서드와 사용자가 역할에 할당 되었는지 여부를 확인 하는 메서드가 포함 되어 있습니다.
* **IUserPasswordStore**  
 [Iuserpasswordstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) 인터페이스는 해시 된 암호를 유지 하기 위해 구현 하는 메서드를 정의 합니다. 해시 된 암호를 가져오고 설정 하는 메서드와 사용자가 암호를 설정 했는지 여부를 나타내는 메서드를 포함 합니다.
* **IUserSecurityStampStore**  
 [IUserSecurityStampStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) 인터페이스는 사용자의 계정 정보가 변경 되었는지 여부를 나타내는 보안 스탬프를 사용 하기 위해 구현 하는 메서드를 정의 합니다. 이 스탬프는 사용자가 암호를 변경 하거나 로그인을 추가 하거나 제거 하는 경우 업데이트 됩니다. 보안 스탬프를 가져오고 설정 하기 위한 메서드를 포함 합니다.
* **IUserTwoFactorStore**  
 [IUserTwoFactorStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) 인터페이스는 2 단계 인증을 지원 하기 위해 구현 하는 메서드를 정의 합니다. 사용자에 대해 2 단계 인증을 사용할 수 있는지 여부를 가져오고 설정 하는 메서드를 포함 합니다.
* **IUserPhoneNumberStore**  
 [IUserPhoneNumberStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) 인터페이스는 사용자 전화 번호를 저장 하기 위해 구현 하는 메서드를 정의 합니다. 전화 번호를 가져오고 설정 하는 방법 및 전화 번호가 확인 되었는지 여부를 나타내는 메서드가 포함 되어 있습니다.
* **IUserEmailStore**  
 [Iuseremailstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) 인터페이스는 사용자 전자 메일 주소를 저장 하기 위해 구현 하는 메서드를 정의 합니다. 전자 메일 주소를 가져오고 설정 하는 방법과 전자 메일이 확인 되었는지 여부를 나타내는 메서드가 포함 되어 있습니다.
* **IUserLockoutStore**  
 [IUserLockoutStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) 인터페이스는 계정 잠금에 대 한 정보를 저장 하기 위해 구현 하는 메서드를 정의 합니다. 실패 한 액세스 시도 및 잠금을 추적 하는 메서드가 포함 되어 있습니다.
* **IQueryableUserStore**  
 [Iqueryableuserstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) 인터페이스는 쿼리 가능한 사용자 저장소를 제공 하기 위해 구현 하는 멤버를 정의 합니다.

앱에 필요한 인터페이스만 구현 합니다. 예를 들면 다음과 같습니다.

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, Identity userclaim 및 Identity UserRole

`Microsoft.AspNet.Identity.EntityFramework`네임 스페이스에는 [ Identity userclaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [ Identity userclaim](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)및 [ Identity UserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) 클래스의 구현이 포함 되어 있습니다. 이러한 기능을 사용 하는 경우 이러한 클래스의 고유한 버전을 만들고 앱에 대 한 속성을 정의 하는 것이 좋습니다. 그러나 기본 작업 (예: 사용자의 클레임 추가 또는 제거)을 수행할 때 이러한 엔터티를 메모리로 로드 하지 않는 것이 더 효율적일 수 있습니다. 대신 백엔드 저장소 클래스는 데이터 소스에서 이러한 작업을 직접 실행할 수 있습니다. 예를 들어 `UserStore.GetClaimsAsync` 메서드를 호출 하 여 `userClaimTable.FindByUserId(user.Id)` 해당 테이블에 대 한 쿼리를 직접 실행 하 고 클레임 목록을 반환할 수 있습니다.

## <a name="customize-the-role-class"></a>Role 클래스 사용자 지정

역할 저장소 공급자를 구현 하는 경우 사용자 지정 역할 유형을 만들 수 있습니다. 특정 인터페이스를 구현할 필요가 없지만가 있어야 `Id` 하며 일반적으로 속성을 갖게 됩니다 `Name` .

예제 역할 클래스는 다음과 같습니다.

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>역할 저장소 사용자 지정

`RoleStore`역할에 대 한 모든 데이터 작업에 대해 메서드를 제공 하는 클래스를 만들 수 있습니다. 이 클래스는 [Rolestore &lt; trole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) 클래스와 동일 합니다. 클래스에서를 `RoleStore` 구현 하 `IRoleStore<TRole>` 고 선택적으로 인터페이스를 구현 합니다 `IQueryableRoleStore<TRole>` .

* **IRoleStore &lt; trole&gt;**  
 [Irolestore &lt; trole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) 인터페이스는 역할 저장소 클래스에서 구현 하는 메서드를 정의 합니다. 역할을 만들고, 업데이트 하 고, 삭제 하 고, 검색 하는 메서드가 포함 되어 있습니다.
* **RoleStore &lt; trole&gt;**  
 사용자 지정 하려면 `RoleStore` 인터페이스를 구현 하는 클래스를 만듭니다 `IRoleStore<TRole>` . 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>새 저장소 공급자를 사용 하도록 앱 다시 구성

저장소 공급자를 구현한 후에는 해당 공급자를 사용 하도록 앱을 구성 합니다. 앱이 기본 공급자를 사용 하는 경우 사용자 지정 공급자로 바꿉니다.

1. `Microsoft.AspNetCore.EntityFramework.Identity`NuGet 패키지를 제거 합니다.
1. 저장소 공급자가 별도의 프로젝트 또는 패키지에 있는 경우이에 대 한 참조를 추가 합니다.
1. 에 대 한 모든 참조를 `Microsoft.AspNetCore.EntityFramework.Identity` 저장소 공급자의 네임 스페이스에 대 한 using 문으로 바꿉니다.
1. `ConfigureServices`메서드에서 `AddIdentity` 사용자 지정 형식을 사용 하도록 메서드를 변경 합니다. 이러한 목적을 위해 사용자 고유의 확장 메서드를 만들 수 있습니다. 예는 [ Identity ServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 을 참조 하세요.
1. 역할을 사용 하는 경우 `RoleManager` 클래스를 사용 하도록를 업데이트 합니다 `RoleStore` .
1. 응용 프로그램의 구성에 대 한 연결 문자열 및 자격 증명을 업데이트 합니다.

예제:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>참조

* [ASP.NET 4.x의 사용자 지정 저장소 공급자 Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/main/src/Identity):이 리포지토리에는 커뮤니티에서 유지 관리 하는 저장소 공급자에 대 한 링크가 포함 되어 있습니다.
