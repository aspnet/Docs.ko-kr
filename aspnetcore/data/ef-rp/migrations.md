---
title: 4부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 마이그레이션
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 4부입니다.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061108"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>4부. ASP.NET Core에서 EF Core 마이그레이션을 사용한 Razor Pages

작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

이 자습서에서는 데이터 모델 변경 관리를 위한 EF Core 마이그레이션 기능을 도입합니다.

새 앱이 개발되면 데이터 모델이 자주 변경됩니다. 모델이 변경될 때마다 모델이 데이터베이스와 동기화 해제됩니다. 이 자습서 시리즈는 존재하지 않는 경우 데이터베이스를 만들도록 Entity Framework를 구성하여 시작되었습니다. 데이터 모델이 변경될 때마다 데이터베이스를 삭제해야 합니다. 다음에 앱이 실행되면 `EnsureCreated`가 호출되어 새 데이터 모델과 일치하도록 데이터베이스를 다시 만듭니다. 그런 다음, `DbInitializer` 클래스가 실행되어 새 데이터베이스를 시드합니다.

데이터베이스를 데이터 모델과 동기화된 상태로 유지하는 이 접근 방식은 앱을 프로덕션 환경에 배포할 때까지 잘 작동합니다. 앱이 프로덕션 환경에서 실행 중인 경우 일반적으로 유지 관리해야 하는 데이터를 저장합니다. 앱은 변경(예: 새 열 추가)될 때마다 테스트 데이터베이스로 시작될 수 없습니다. EF Core 마이그레이션 기능은 EF Core가 새 데이터베이스를 만드는 대신 데이터베이스 스키마를 업데이트하도록 설정하여 이 문제를 해결합니다.

데이터 모델이 변경될 때 데이터베이스를 삭제하고 다시 작성하는 대신 마이그레이션은 스키마를 업데이트하고 기존 데이터를 유지합니다.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>데이터베이스 삭제

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

SSOX(**SQL Server 개체 탐색기**)를 사용하여 데이터베이스를 삭제하거나, PMC(**패키지 관리자 콘솔**)에서 다음 명령을 실행합니다.

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 명령 프롬프트에서 다음 명령을 실행하여 EF CLI를 설치합니다.

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* 명령 프롬프트에서 프로젝트 폴더로 이동합니다. 프로젝트 폴더에는 *ContosoUniversity.csproj* 파일이 포함되어 있습니다.

* *CU.db* 파일을 삭제하거나 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>초기 마이그레이션 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC에서 다음 명령을 실행합니다.

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

명령 프롬프트가 프로젝트 폴더에 있는지 확인하고 다음 명령을 실행합니다.

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Up 및 Down 메서드

EF Core `migrations add` 명령은 데이터베이스를 만드는 코드를 생성했습니다. 이 마이그레이션 코드는 *Migrations\<timestamp>_InitialCreate.cs* 파일에 있습니다. `InitialCreate` 클래스의 `Up` 메서드는 데이터 모델 엔터티 집합에 해당하는 데이터베이스 테이블을 만듭니다. `Down` 메서드는 다음 예제처럼 테이블을 삭제합니다.

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

위의 코드는 초기 마이그레이션에 대한 코드입니다. 코드:

* `migrations add InitialCreate` 명령에서 생성되었습니다. 
* `database update` 명령에서 실행됩니다.
* 데이터베이스 컨텍스트 클래스에 지정된 데이터 모델의 데이터베이스를 만듭니다.

파일 이름에는 마이그레이션 이름 매개 변수(이 예제에서 "InitialCreate")가 사용됩니다. 마이그레이션 이름은 임의의 유효한 파일 이름이 될 수 있습니다. 마이그레이션에서 수행 중인 작업을 요약한 단어 또는 구를 선택하는 것이 가장 좋습니다. 예를 들어 부서 테이블을 추가한 마이그레이션은 "AddDepartmentTable"이라고 할 수 있습니다.

## <a name="the-migrations-history-table"></a>마이그레이션 기록 테이블

* SSOX 또는 SQLite 도구를 사용하여 데이터베이스를 검사합니다.
* `__EFMigrationsHistory` 테이블이 추가된 것을 볼 수 있습니다. `__EFMigrationsHistory` 테이블은 데이터베이스에 적용된 마이그레이션을 추적합니다.
* `__EFMigrationsHistory` 테이블의 데이터를 봅니다. 첫 번째 마이그레이션에 대해 한 행을 표시합니다.

## <a name="the-data-model-snapshot"></a>데이터 모델 스냅샷

마이그레이션에서는 현재 데이터 모델의 ‘스냅샷’을 *Migrations/SchoolContextModelSnapshot.cs* 에 만듭니다. 마이그레이션을 추가하면 EF가 현재 데이터 모델을 스냅샷 파일과 비교하여 변경 내용을 확인합니다.

스냅샷 파일이 데이터 모델의 상태를 추적하므로 `<timestamp>_<migrationname>.cs` 파일을 삭제하여 마이그레이션을 삭제할 수 없습니다. 가장 최근 마이그레이션을 백업하려면 `migrations remove` 명령을 사용해야 합니다. 이 명령은 마이그레이션을 삭제하고 스냅샷이 올바르게 다시 설정되도록 합니다. 자세한 내용은 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)를 참조하세요.

## <a name="remove-ensurecreated"></a>EnsureCreated 제거

이 자습서 시리즈는 `EnsureCreated`를 사용하여 시작되었습니다. `EnsureCreated`는 마이그레이션 기록 테이블을 만들지 않으므로 마이그레이션과 함께 사용할 수 없습니다. 데이터베이스를 삭제하고 자주 다시 생성하는 테스트 또는 신속한 프로토타입 만들기를 위해 디자인되었습니다.

이 지점부터 자습서에서는 마이그레이션을 사용합니다.

*Data/DBInitializer.cs* 에서 다음 줄을 주석으로 처리합니다.

```csharp
context.Database.EnsureCreated();
```
앱을 실행하고 데이터베이스가 시드되는지 확인합니다.

## <a name="applying-migrations-in-production"></a>프로덕션 환경에서 마이그레이션 적용

프로덕션 앱은 애플리케이션 시작 시 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)를 호출하지 **않는 것이** 좋습니다. `Migrate`는 서버 팜에 배포된 앱에서 호출하면 안 됩니다. 앱이 여러 서버 인스턴스로 확장되는 경우 데이터베이스 스키마 업데이트가 여러 서버에서 발생하거나 읽기/쓰기 권한과 충돌하지 않도록 하는 것이 어렵습니다.

데이터베이스 마이그레이션은 배포의 일부로 제어된 방식으로 수행되어야 합니다. 프로덕션 데이터베이스 마이그레이션 방법은 다음과 같습니다.

* 마이그레이션을 사용하여 SQL 스크립트를 작성하고 배포 시 SQL 스크립트 사용
* 제어된 환경에서 `dotnet ef database update` 실행

## <a name="troubleshooting"></a>문제 해결

앱에서 SQL Server LocalDB를 사용하고 다음 예외를 표시하는 경우:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

솔루션은 명령 프롬프트에서 `dotnet ef database update`를 실행해야 할 수 있습니다.

### <a name="additional-resources"></a>추가 자료

* [EF Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [패키지 관리자 콘솔(Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>다음 단계

다음 자습서에서는 데이터 모델을 빌드하여 엔터티 속성 및 새 엔터티를 추가합니다.

> [!div class="step-by-step"]
> [이전 자습서](xref:data/ef-rp/sort-filter-page)
> [다음 자습서](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에서는 데이터 모델 변경 관리를 위한 EF Core 마이그레이션 기능이 사용됩니다.

해결할 수 없는 문제가 발생한 경우 [완성된 앱](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.

새 앱이 개발되면 데이터 모델이 자주 변경됩니다. 모델이 변경될 때마다 모델이 데이터베이스와 동기화 해제됩니다. 이 자습서는 존재하지 않는 경우 데이터베이스를 만들도록 Entity Framework를 구성하여 시작되었습니다. 데이터 모델이 변경될 때마다 다음이 수행됩니다.

* DB가 삭제됩니다.
* EF는 모델과 일치하는 새 항목을 만듭니다.
* 앱은 테스트 데이터로 DB를 시드합니다.

DB를 데이터 모델과 동기화된 상태로 유지하는 이 접근 방식은 앱을 프로덕션 환경에 배포할 때까지 잘 작동합니다. 앱이 프로덕션 환경에서 실행 중인 경우 일반적으로 유지 관리해야 하는 데이터를 저장합니다. 앱은 변경(예: 새 열 추가)될 때마다 테스트 DB로 시작될 수 없습니다. EF Core 마이그레이션 기능은 EF Core에서 새 DB를 만드는 대신 DB 스키마를 업데이트하도록 하여 이 문제를 해결합니다.

데이터 모델이 변경될 때 DB를 삭제하고 다시 작성하는 대신 마이그레이션은 스키마를 업데이트하고 기존 데이터를 유지합니다.

## <a name="drop-the-database"></a>데이터베이스 삭제

SSOX(**SQL Server 개체 탐색기**) 또는 `database drop` 명령을 사용합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.

```powershell
Drop-Database
```

PMC에서 `Get-Help about_EntityFrameworkCore`를 실행하여 도움말 정보를 가져옵니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

명령 창을 열고 프로젝트 폴더로 이동합니다. 프로젝트 폴더에는 *Startup.cs* 파일이 포함되어 있습니다.

명령 창에서 다음을 입력합니다.

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>초기 마이그레이션 만들기 및 DB 업데이트

프로젝트를 빌드하고 첫 번째 마이그레이션을 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Up 및 Down 메서드 검사

EF Core `migrations add` 명령은 DB를 생성하는 코드를 생성했습니다. 이 마이그레이션 코드는 *Migrations\<timestamp>_InitialCreate.cs* 파일에 있습니다. `InitialCreate` 클래스dml `Up` 메서드는 데이터 모델 엔터티 집합에 해당하는 DB 테이블을 만듭니다. `Down` 메서드는 다음 예제처럼 테이블을 삭제합니다.

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

마이그레이션에서는 마이그레이션을 위한 데이터 모델 변경을 구현하기 위해 `Up` 메서드를 호출합니다. 업데이트를 롤백하는 명령을 입력하면 마이그레이션에서 `Down` 메서드를 호출합니다.

위의 코드는 초기 마이그레이션에 대한 코드입니다. 이 코드는 `migrations add InitialCreate` 명령을 실행할 때 만들어집니다. 파일 이름에는 마이그레이션 이름 매개 변수(이 예제에서 "InitialCreate")가 사용됩니다. 마이그레이션 이름은 임의의 유효한 파일 이름이 될 수 있습니다. 마이그레이션에서 수행 중인 작업을 요약한 단어 또는 구를 선택하는 것이 가장 좋습니다. 예를 들어 부서 테이블을 추가한 마이그레이션은 "AddDepartmentTable"이라고 할 수 있습니다.

초기 마이그레이션이 생성되었고 DB가 존재하는 경우:

* DB 만들기 코드가 생성됩니다.
* DB는 데이터 모델과 이미 일치하므로 DB 만들기 코드를 실행할 필요가 없습니다. DB 만들기 코드가 실행되면 DB가 데이터 모델과 이미 일치하므로 아무것도 변경되지 않습니다.

앱이 새 환경에 배포되면 DB 만들기 코드를 실행하여 DB를 만들어야 합니다.

이전에 DB는 삭제되었거나 존재하지 않으므로 마이그레이션에서 새 DB를 만듭니다.

### <a name="the-data-model-snapshot"></a>데이터 모델 스냅샷

마이그레이션은 *Migrations/SchoolContextModelSnapshot.cs* 에 현재 데이터베이스 스키마의 *스냅숏* 을 만듭니다. 마이그레이션을 추가하면 EF가 데이터 모델을 스냅샷 파일과 비교하여 변경 내용을 확인합니다.

마이그레이션을 삭제하려면 다음 명령을 사용합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

마이그레이션 제거

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

자세한 내용은 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)를 참조하세요.

---

마이그레이션 제거 명령은 마이그레이션을 삭제하고 스냅샷이 올바르게 다시 설정되도록 합니다.

### <a name="remove-ensurecreated-and-test-the-app"></a>EnsureCreated 제거 및 앱 테스트

초기 개발에는 `EnsureCreated`가 사용되었습니다. 이 자습서에서는 마이그레이션이 사용됩니다. `EnsureCreated`에는 다음과 같은 제한 사항이 적용됩니다.

* 마이그레이션을 무시하고 DB 및 스키마를 만듭니다.
* 마이그레이션 테이블을 만들지 않습니다.
* 마이그레이션에 사용할 수 *없습니다*.
* DB를 삭제하고 자주 다시 생성하는 테스트 또는 신속한 프로토타입 만들기를 위해 설계되었습니다.

`EnsureCreated`를 제거합니다.

```csharp
context.Database.EnsureCreated();
```

앱을 실행하고 DB이 시드되었는지 확인합니다.

### <a name="inspect-the-database"></a>데이터베이스 검사

**SQL Server 개체 탐색기** 를 사용하여 DB를 검사합니다. `__EFMigrationsHistory` 테이블이 추가된 것을 볼 수 있습니다. `__EFMigrationsHistory` 테이블은 DB에 적용된 마이그레이션을 추적합니다. `__EFMigrationsHistory` 테이블의 데이터를 보면 첫 번째 마이그레이션에 대해 한 행이 표시됩니다. 앞의 CLI 출력 예제의 마지막 로그는 이 행을 만드는 INSERT 문을 보여 줍니다.

앱을 실행하고 모든 항목이 작동하는지 확인합니다.

## <a name="applying-migrations-in-production"></a>프로덕션 환경에서 마이그레이션 적용

프로덕션 앱은 애플리케이션 시작 시 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)를 호출하지 **않는 것이** 좋습니다. 서버 팜의 앱에서 `Migrate`를 호출하면 안됩니다. 예를 들어, 앱이 스케일 아웃으로 클라우드에 배포된 경우(앱의 여러 인스턴스가 실행 중임)

데이터베이스 마이그레이션은 배포의 일부로 제어된 방식으로 수행되어야 합니다. 프로덕션 데이터베이스 마이그레이션 방법은 다음과 같습니다.

* 마이그레이션을 사용하여 SQL 스크립트를 작성하고 배포 시 SQL 스크립트 사용
* 제어된 환경에서 `dotnet ef database update` 실행

EF Core는 `__MigrationsHistory` 테이블을 사용하여 마이그레이션을 실행해야 하는지 확인합니다. DB가 최신 상태이면 마이그레이션이 실행되지 않습니다.

## <a name="troubleshooting"></a>문제 해결

[완료된 앱](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)을 다운로드합니다.

앱에서는 다음과 같은 예외가 생성됩니다.

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

해결책: `dotnet ef database update`를 실행합니다.

### <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)
* [패키지 관리자 콘솔(Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [이전](xref:data/ef-rp/sort-filter-page)
> [다음](xref:data/ef-rp/complex-data-model)

::: moniker-end

