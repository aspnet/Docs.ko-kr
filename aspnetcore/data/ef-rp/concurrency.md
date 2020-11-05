---
title: 8부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 동시성
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 8부입니다.
ms.author: riande
ms.custom: mvc
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053997"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a>8부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 동시성

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) 및 [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

이 자습서에는 여러 사용자가 동시에(같은 시간에) 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.

## <a name="concurrency-conflicts"></a>동시성 충돌

동시성 충돌이 발생한 경우:

* 사용자는 엔터티에 대한 편집 페이지를 탐색합니다.
* 첫 번째 사용자가 데이터베이스에 변경 내용을 기록하기 전에 다른 사용자가 동일한 엔터티를 업데이트합니다.

동시성 검색이 사용하도록 설정되지 않으면 누구든지 데이터베이스를 마지막으로 업데이트하면 다른 사용자의 변경 내용을 덮어씁니다. 이 위험이 허용 가능한 경우 동시성에 대한 프로그래밍의 비용은 이점보다 클 수 있습니다.

### <a name="pessimistic-concurrency-locking"></a>비관적 동시성(잠금)

동시성 충돌을 방지하는 한 가지 방법은 데이터베이스 잠금을 사용하는 것입니다. 이를 비관적 동시성이라고 합니다. 앱은 업데이트하려는 데이터베이스 행을 읽기 전에 잠금을 요청합니다. 업데이트 액세스를 위해 행이 잠긴 후에는 첫 번째 잠금이 해제될 때까지 다른 사용자가 행을 잠글 수 없습니다.

잠금 관리에는 단점이 있습니다. 프로그래밍하기에 복잡할 수 있으며 사용자 수가 증가하면 성능 문제가 발생할 수 있습니다. Entity Framework Core는 이에 대한 기본 제공 지원을 제공하지 않으며 이 자습서에서는 구현하는 방법을 보여 주지 않습니다.

### <a name="optimistic-concurrency"></a>낙관적 동시성

낙관적 동시성은 동시성 충돌 발생을 허용하고, 이에 적절하게 반응합니다. 예를 들어, Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.

![예산을 0으로 변경](concurrency/_static/change-budget30.png)

Jane이 **저장** 을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.

![시작 날짜를 2013으로 변경](concurrency/_static/change-date30.png)

Jane이 먼저 **저장** 을 클릭하고 변경 내용이 적용되는 것을 확인합니다. 브라우저에는 예산 금액이 0인 인덱스 페이지가 표시되기 때문입니다.

John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장** 을 클릭합니다. 다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.

* 사용자가 수정한 속성의 추적을 유지하고 데이터베이스에서 해당하는 열만 업데이트할 수 있습니다.

  이 시나리오에서는 데이터가 손실되지 않습니다. 다른 속성이 두 사용자에 의해 업데이트되었습니다. 다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다. 이 업데이트 메서드는 데이터 손실로 이어질 수 있는 충돌 횟수를 줄일 수 있습니다. 이 방법이 제공하는 몇 가지 단점은 다음과 같습니다.
 
  * 같은 속성에 변경 사항이 적용된 경우 데이터 손실을 방지할 수 없습니다.
  * 일반적으로 웹앱에서는 실현할 수 없습니다. 페치된 값과 새 값을 모두 추적하기 위해 유효한 상태를 유지해야 합니다. 많은 양의 상태를 유지하는 것은 응용 프로그램 성능에 영향을 미칠 수 있습니다.
  * 엔터티에 대한 동시성 감지보다 앱 복잡성이 증가할 수 있습니다.

* Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.

  다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 페치된 $350,000.00 값을 볼 수 있습니다. 이 방법을 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다. (클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 동시성 처리에 대한 코딩을 수행하지 않은 경우 클라이언트 우선이 자동으로 발생합니다.

* John의 변경 내용이 데이터베이스에서 업데이트되지 않도록 할 수 있습니다. 일반적으로 앱은:

  * 오류 메시지를 표시합니다.
  * 데이터의 현재 상태를 표시합니다.
  * 사용자가 변경 내용을 다시 적용하도록 허용합니다.

  이를 *저장소 우선* 시나리오라고 합니다. (데이터 저장소 값은 클라이언트가 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다. 이 메서드는 사용자 알림 없이 덮어쓴 변경 내용이 없는지 확인합니다.

## <a name="conflict-detection-in-ef-core"></a>EF Core의 충돌 검색

EF Core는 충돌을 검색할 때 `DbConcurrencyException` 예외를 throw합니다. 데이터 모델은 충돌 검색을 사용하도록 구성해야 합니다. 충돌 검색을 사용하도록 설정하기 위한 몇 가지 옵션은 다음과 같습니다.

* Update 및 Delete 명령의 Where 절에서 [동시성 토큰](/ef/core/modeling/concurrency)으로 구성된 열의 원래 값을 포함하도록 EF Core를 구성합니다.

  `SaveChanges`가 호출되면 Where 절은 <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 특성으로 주석 지정된 속성의 원래 값을 찾습니다. Update 문은 행을 처음 읽은 후 동시성 토큰 속성이 변경된 경우 업데이트할 행을 찾지 못합니다. EF Core는 이 상황을 동시성 충돌로 해석합니다. 많은 열이 있는 데이터베이스 테이블의 경우 이 방법으로 많은 Where 절이 발생할 수 있으며 많은 양의 상태가 필요할 수 있습니다. 따라서 이 방법은 일반적으로 권장되지 않으며 이 자습서에서 사용되는 방법이 아닙니다.

* 데이터베이스 테이블에서 행이 변경된 시기를 확인하는 데 사용될 수 있는 추적 열을 포함합니다.

  SQL Server 데이터베이스에서 추적 열의 데이터 형식은 `rowversion`입니다. `rowversion` 값은 행이 업데이트될 때마다 증가되는 순차적 번호입니다. Update 또는 Delete 명령에서 Where 절은 추적 열의 원래 값을 포함합니다(원래 행 버전 번호). 업데이트되는 행이 다른 사용자에 의해 변경된 경우 `rowversion` 열의 값은 원래 값과 다릅니다. 이 경우 Update 또는 Delete 문은 Where 절 때문에 업데이트할 행을 찾을 수 없습니다. Update 또는 Delete 명령의 영향을 받는 행이 없는 경우 EF Core는 동시성 예외를 throw합니다.

## <a name="add-a-tracking-property"></a>추적 속성 추가

*Models/Department.cs* 에서 RowVersion으로 명명된 추적 속성을 추가합니다.

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 특성은 열을 동시성 추적 열로 식별합니다. 흐름 API는 추적 속성을 지정하는 대체 방법입니다.

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

SQL Server 데이터베이스의 경우 엔터티 속성의 `[Timestamp]` 특성은 바이트 배열로 정의됩니다.

* DELETE 및 UPDATE WHERE 절에 열이 포함됩니다.
* 데이터베이스의 열 형식을 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)으로 설정합니다.

데이터베이스는 행이 업데이트될 때마다 증가하는 순차적 행 버전 번호를 생성합니다. `Update` 또는 `Delete` 명령에서 `Where` 절은 페치된 행 버전 값을 포함합니다. 페치된 후 업데이트되는 행이 변경된 경우:

* 현재 행 버전 값이 페치된 값과 일치하지 않습니다.
* `Where` 절이 페치된 행 버전 값을 검색하므로 `Update` 또는 `Delete` 명령은 행을 찾지 않습니다.
* `DbUpdateConcurrencyException`이 throw됩니다.

다음 코드는 부서 이름이 업데이트될 때 EF Core에서 생성된 T-SQL의 일부를 보여 줍니다.

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

위에 강조 표시된 코드는 `RowVersion`을 포함하는 `WHERE` 절을 보여 줍니다. 데이터베이스 `RowVersion`이 `RowVersion` 매개 변수(`@p2`)와 다를 경우 행은 업데이트되지 않습니다.

다음 강조 표시된 코드는 정확히 한 개의 행이 업데이트되었음을 확인하는 T-SQL을 보여 줍니다.

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql)는 마지막 명령문의 영향을 받는 행 수를 반환합니다. 행이 업데이트되지 않으면 EF Core는 `DbUpdateConcurrencyException`을 throw합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite 데이터베이스의 경우 엔터티 속성의 `[Timestamp]` 특성은 바이트 배열로 정의됩니다.

* DELETE 및 UPDATE WHERE 절에 열이 포함됩니다.
* BLOB 열 형식에 매핑됩니다.

데이터베이스 트리거는 행이 업데이트될 때마다 새 임의 바이트 배열을 사용하여 RowVersion 열을 업데이트합니다. `Update` 또는 `Delete` 명령에서 `Where` 절은 RowVersion 열의 페치된 값을 포함합니다. 페치된 후 업데이트되는 행이 변경된 경우:

* 현재 행 버전 값이 페치된 값과 일치하지 않습니다.
* `Where` 절이 원래 행 버전 값을 검색하므로 `Update` 또는 `Delete` 명령은 행을 찾지 않습니다.
* `DbUpdateConcurrencyException`이 throw됩니다.

---

### <a name="update-the-database"></a>데이터베이스 업데이트

`RowVersion` 속성을 추가하면 마이그레이션이 필요한 데이터 모델이 변경됩니다.

프로젝트를 빌드합니다. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC에서 다음 명령을 실행합니다.

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 터미널에서 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

이 명령은 다음을 수행합니다.

* *Migrations/{time stamp}_RowVersion.cs* 마이그레이션 파일을 만듭니다.
* *Migrations/SchoolContextModelSnapshot.cs* 파일을 업데이트합니다. 업데이트는 다음 강조 표시된 코드를 `BuildModel` 메서드에 추가합니다.

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC에서 다음 명령을 실행합니다.

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* `Migrations/<timestamp>_RowVersion.cs` 파일을 열고 강조 표시된 코드를 추가합니다.

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  위의 코드는

  * 임의의 BLOB 값을 사용하여 기존 행을 업데이트합니다.
  * 행이 업데이트될 때마다 RowVersion 열을 임의 BLOB 값으로 설정하는 데이터베이스 트리거를 추가합니다.

* 터미널에서 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>부서 페이지 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 다음 예외가 포함된 [학생 페이지 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따릅니다.

* *Pages/Departments* 폴더를 만듭니다.  
* 모델 클래스에 `Department`를 사용합니다.
  * 새 컨텍스트 클래스를 만드는 대신 기존 컨텍스트 클래스를 사용합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Pages/Departments* 폴더를 만듭니다.

* 다음 명령을 실행하여 부서 페이지를 스캐폴드합니다.

  **Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **Linux 또는 macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

프로젝트를 빌드합니다.

## <a name="update-the-index-page"></a>인덱스 페이지 업데이트

스캐폴딩 도구는 인덱스 페이지에 대한 `RowVersion` 열을 만들지만, 해당 필드는 프로덕션 앱에 표시되지 않습니다. 이 자습서에서는 동시성 처리의 작동 방식을 보여 줄 수 있는 `RowVersion`의 마지막 바이트가 표시됩니다. 마지막 바이트는 자체적으로 고유하게 보장되지 않습니다.

*Pages\Departments\Index.cshtml* 페이지를 업데이트합니다.

* 인덱스를 부서로 바꿉니다.
* 바이트 배열의 마지막 바이트만 표시하도록 `RowVersion`을 포함하는 코드를 변경합니다.
* FirstMidName을 FullName으로 바꿉니다.

다음 코드에서는 업데이트된 페이지를 보여 줍니다.

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>편집 페이지 모델 업데이트

*Pages\Departments\Edit.cshtml.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue>는 `OnGet` 메서드에서 페치될 때 엔터티의 `rowVersion` 값을 사용하여 업데이트됩니다. EF Core는 원본 `RowVersion` 값을 포함하는 WHERE 절과 함께 SQL UPDATE 명령을 생성합니다. UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) `DbUpdateConcurrencyException` 예외가 throw됩니다.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

앞의 강조 표시된 코드에서 다음을 수행합니다.

* `Department.RowVersion`의 값은 편집 페이지에 대한 Get 요청에서 원래 페치되었을 때 엔터티에 있었던 값입니다. 이 값은 편집할 엔터티를 표시하는 Razor 페이지의 숨겨진 필드를 통해 `OnPost` 메서드에 제공됩니다. 숨겨진 필드 값은 모델 바인더를 통해 `Department.RowVersion`에 복사됩니다.
* `OriginalValue`는 EF Core가 Where 절에서 사용할 값입니다. 강조 표시된 코드 줄이 실행되기 전에 `OriginalValue`에는 `FirstOrDefaultAsync`가 이 메서드에서 호출될 때 데이터베이스에 있었던 값이 포함되며, 이 값은 편집 페이지에 표시된 값과 다를 수 있습니다.
* 강조 표시된 코드는 EF Core가 SQL UPDATE 문의 Where 절에 표시된 `Department` 엔터티의 원래 `RowVersion` 값을 사용하는지 확인합니다.

동시성 오류가 발생하면 다음 강조 표시된 코드는 클라이언트 값(이 메서드에 게시된 값)과 데이터베이스 값을 가져옵니다.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

다음 코드는 데이터베이스 값이 `OnPostAsync`에 게시된 값과 다른 각 열에 대한 사용자 오류 메시지를 추가합니다.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

다음 강조 표시된 코드는 `RowVersion` 값을 데이터베이스에서 검색된 새 값으로 설정합니다. 다음에 사용자가 **저장** 을 클릭하면, 편집 페이지의 마지막 표시 이후 발생한 동시성 오류만 catch됩니다.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다. Razor 페이지에서 필드의 `ModelState` 값은 모델 속성 값에 우선합니다(둘 다 있는 경우).

### <a name="update-the-edit-page"></a>편집 페이지 업데이트

다음 코드로 *Pages/Departments/Edit.cshtml* 을 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

위의 코드는

* `page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.
* 숨겨진 행 버전을 추가합니다. 포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.
* 디버깅을 위해 `RowVersion`의 마지막 바이트를 표시합니다.
* `ViewData`를 강력한 형식의 `InstructorNameSL`로 바꿉니다.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>편집 페이지로 동시성 충돌 테스트

영어 부서에 있는 편집의 두 브라우저 인스턴스를 엽니다.

* 앱을 실행하고 부서를 선택합니다.
* 영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.
* 첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.

두 개의 브라우저 탭에 동일한 정보가 표시됩니다.

첫 번째 브라우저 탭의 이름을 변경하고 **저장** 을 클릭합니다.

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-130.png)

브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다. 업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.

두 번째 브라우저 탭에서 다른 필드를 변경합니다.

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-230.png)

**저장** 을 클릭합니다. 데이터베이스 값과 일치하지 않는 모든 필드에 대한 오류 메시지가 표시됩니다.

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error30.png)

이 브라우저 창은 Name 필드 변경용으로 의도되지 않았습니다. 현재 값(Languages)을 복사하여 Name 필드에 붙여넣습니다. 필드 밖을 탭합니다. 클라이언트 쪽 유효성 검사가 오류 메시지를 제거합니다.

**저장** 을 다시 클릭합니다. 두 번째 브라우저 탭에 입력한 값이 저장됩니다. 인덱스 페이지에 저장된 값이 표시됩니다.

## <a name="update-the-delete-page-model"></a>삭제 페이지 업데이트 모델

다음 코드로 *Pages/Departments/Delete.cshtml.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

페이지 삭제는 엔터티가 페치된 후 변경될 때 동시성 충돌을 감지합니다. `Department.RowVersion`은 엔터티가 페치될 때 행 버전입니다. EF Core가 SQL DELETE 명령을 만들 때 `RowVersion`과 함께 WHERE 절이 포함됩니다. SQL DELETE 명령의 영향을 받는 행이 없는 경우:

* SQL DELETE 명령의 `RowVersion`이 데이터베이스의 `RowVersion`과 일치하지 않습니다.
* DbUpdateConcurrencyException 예외가 throw됩니다.
* `OnGetAsync`가 `concurrencyError`를 사용하여 호출됩니다.

### <a name="update-the-delete-page"></a>삭제 페이지 업데이트

*Pages/Departments/Delete.cshtml* 을 다음 코드로 업데이트합니다.

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

위의 코드로 다음이 변경됩니다.

* `page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.
* 오류 메시지를 추가합니다.
* **Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.
* 마지막 바이트를 표시하도록 `RowVersion`을 변경합니다.
* 숨겨진 행 버전을 추가합니다. 포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.

### <a name="test-concurrency-conflicts"></a>동시성 충돌 테스트

테스트 부서를 만듭니다.

테스트 부서에 있는 삭제의 두 브라우저 인스턴스를 엽니다.

* 앱을 실행하고 부서를 선택합니다.
* 테스트 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.
* 테스트 부서에 대한 **편집** 하이퍼링크를 클릭합니다.

두 개의 브라우저 탭에 동일한 정보가 표시됩니다.

첫 번째 브라우저 탭의 예산을 변경하고 **저장** 을 클릭합니다.

브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다. 업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.

두 번째 탭에서 테스트 부서를 삭제합니다. 동시성 오류는 데이터베이스의 현재 값으로 표시됩니다. `RowVersion`이 업데이트되지 않았다면 **삭제** 를 클릭하면 엔터티가 삭제됩니다.

## <a name="additional-resources"></a>추가 자료

* [EF Core의 동시성 토큰](/ef/core/modeling/concurrency)
* [EF Core의 동시성 처리](/ef/core/saving/concurrency)
* [ASP.NET Core 2.x 소스 디버깅](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>다음 단계

이 자습서는 시리즈의 마지막 자습서입니다. 추가 항목은 [이 자습서 시리즈의 MVC 버전](xref:data/ef-mvc/index)에서 다룹니다.

> [!div class="step-by-step"]
> [이전 자습서](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에는 여러 사용자가 동시에(같은 시간에) 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다. 해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>동시성 충돌

동시성 충돌이 발생한 경우:

* 사용자는 엔터티에 대한 편집 페이지를 탐색합니다.
* 첫 번째 사용자가 DB에 변경 내용을 기록하기 전에 다른 사용자가 동일한 엔터티를 업데이트합니다.

동시성 감지가 활성화되지 않으면 동시 업데이트 시 다음이 발생합니다.

* 마지막 업데이트가 적용됩니다. 즉, 마지막 업데이트 값이 DB에 저장됩니다.
* 현재 업데이트 중 첫 번째 작업이 손실됩니다.

### <a name="optimistic-concurrency"></a>낙관적 동시성

낙관적 동시성은 동시성 충돌 발생을 허용하고, 이에 적절하게 반응합니다. 예를 들어, Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.

![예산을 0으로 변경](concurrency/_static/change-budget.png)

Jane이 **저장** 을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.

![시작 날짜를 2013으로 변경](concurrency/_static/change-date.png)

Jane이 먼저 **저장** 을 클릭하여 브라우저에 인덱스 페이지가 표시될 때 변경 사항을 확인합니다.

![0으로 변경된 예산](concurrency/_static/budget-zero.png)

John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장** 을 클릭합니다. 다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.

낙관적 동시성에는 다음과 같은 옵션이 포함됩니다.

* 사용자가 수정한 속성을 추적하고 DB에서 해당하는 열만 업데이트할 수 있습니다.

  이 시나리오에서는 데이터가 손실되지 않습니다. 다른 속성이 두 사용자에 의해 업데이트되었습니다. 다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다. 이 업데이트 메서드는 데이터 손실로 이어질 수 있는 충돌 횟수를 줄일 수 있습니다. 이 방법은:
 
  * 같은 속성에 변경 사항이 적용된 경우 데이터 손실을 방지할 수 없습니다.
  * 일반적으로 웹앱에서는 실현할 수 없습니다. 페치된 값과 새 값을 모두 추적하기 위해 유효한 상태를 유지해야 합니다. 많은 양의 상태를 유지하는 것은 응용 프로그램 성능에 영향을 미칠 수 있습니다.
  * 엔터티에 대한 동시성 감지보다 앱 복잡성이 증가할 수 있습니다.

* Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.

  다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 페치된 $350,000.00 값을 볼 수 있습니다. 이 방법을 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다. (클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 동시성 처리에 대한 코딩을 수행하지 않은 경우 클라이언트 우선이 자동으로 발생합니다.

* John의 변경 내용이 DB에서 업데이트되지 않도록 할 수 있습니다. 일반적으로 앱은:

  * 오류 메시지를 표시합니다.
  * 데이터의 현재 상태를 표시합니다.
  * 사용자가 변경 내용을 다시 적용하도록 허용합니다.

  이를 *저장소 우선* 시나리오라고 합니다. (데이터 저장소 값은 클라이언트가 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다. 이 메서드는 사용자 알림 없이 덮어쓴 변경 내용이 없는지 확인합니다.

## <a name="handling-concurrency"></a>동시성 처리 

속성이 [동시성 토큰](/ef/core/modeling/concurrency)으로 구성되는 경우:

* EF Core는 속성이 페치된 후 수정되지 않았는지 확인합니다. 확인 작업은 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 또는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 발생합니다.
* 속성이 페치된 후 변경된 경우 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)이 throw됩니다. 

`DbUpdateConcurrencyException`의 throw를 지원하도록 DB 및 데이터 모델을 구성해야 합니다.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>속성에서 동시성 충돌 감지

동시성 충돌은 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 특성을 사용하여 속성 수준에서 감지될 수 있습니다. 특성은 모델에서 여러 속성에 적용할 수 있습니다. 자세한 내용은 [데이터 주석 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)를 참조하세요.

`[ConcurrencyCheck]` 특성은 이 자습서에서 사용되지 않습니다.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>행에서 동시성 충돌 감지

동시성 충돌을 감지하기 위해 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 추적 열이 모델에 추가됩니다.  `rowversion` :

* SQL Server 한정적입니다. 다른 데이터베이스는 유사한 기능을 제공하지 않을 수 있습니다.
* DB에서 페치된 이후로 엔터티가 변경되지 않았는지를 확인하는 데 사용됩니다. 

DB는 행이 업데이트될 때마다 증가되는 순차적 `rowversion` 번호를 생성합니다. `Update` 또는 `Delete` 명령에서 `Where` 절은 `rowversion`의 페치된 값을 포함합니다. 업데이트되는 행이 변경되는 경우:

* `rowversion`은 페치된 값에 일치하지 않습니다.
* `Update` 또는 `Delete` 명령은 `Where` 절이 페치된 `rowversion`을 포함하므로 행을 찾지 않습니다.
* `DbUpdateConcurrencyException`이 throw됩니다.

EF Core에서 `Update` 또는 `Delete` 명령에 의해 업데이트된 행이 없는 경우 동시성 예외가 throw됩니다.

### <a name="add-a-tracking-property-to-the-department-entity"></a>추적 속성을 부서 엔터티에 추가

*Models/Department.cs* 에서 RowVersion으로 명명된 추적 속성을 추가합니다.

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[타임스탬프](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 특성은 이 열이 `Update` 및 `Delete` 명령의 `Where` 절에 포함되어 있음을 지정합니다. SQL `rowversion` 형식이 대체하기 전에 이전 버전의 SQL Server가 SQL `timestamp` 데이터 형식을 사용했으므로 특성은 `Timestamp`라고 합니다.

또한 흐름 API가 추적 속성을 지정할 수 있습니다.

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

다음 코드는 부서 이름이 업데이트될 때 EF Core에서 생성된 T-SQL의 일부를 보여 줍니다.

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

위에 강조 표시된 코드는 `RowVersion`을 포함하는 `WHERE` 절을 보여 줍니다. DB `RowVersion`이 `RowVersion` 매개 변수(`@p2`)와 다를 경우 행은 업데이트되지 않습니다.

다음 강조 표시된 코드는 정확히 한 개의 행이 업데이트되었음을 확인하는 T-SQL을 보여 줍니다.

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql)는 마지막 명령문의 영향을 받는 행 수를 반환합니다. 행이 업데이트되지 않은 경우 EF Core는 `DbUpdateConcurrencyException`을 throw합니다.

Visual Studio의 출력 창에서 T-SQL EF Core 생성을 확인할 수 있습니다.

### <a name="update-the-db"></a>DB 업데이트

`RowVersion` 속성을 추가하면 마이그레이션이 필요한 DB 모델이 변경됩니다.

프로젝트를 빌드합니다. 명령 창에서 다음을 입력합니다.

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

이전 명령은:

* *Migrations/{time stamp}_RowVersion.cs* 마이그레이션 파일을 추가합니다.
* *Migrations/SchoolContextModelSnapshot.cs* 파일을 업데이트합니다. 업데이트는 다음 강조 표시된 코드를 `BuildModel` 메서드에 추가합니다.

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* 마이그레이션을 실행하여 DB를 업데이트합니다.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>부서 모델 스캐폴드

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-student-pages)의 지침을 따르고 `Department`를 모델 클래스로 사용합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

위의 명령은 `Department` 모델을 스캐폴드합니다. Visual Studio에서 프로젝트를 엽니다.

프로젝트를 빌드합니다.

### <a name="update-the-departments-index-page"></a>부서 인덱스 페이지 업데이트

스캐폴딩 엔진은 인덱스 페이지에 대한 `RowVersion` 열을 만들지만, 해당 필드는 표시되지 않아야 합니다. 이 자습서에서는 동시성을 이해하는 데 도움을 주기 위해 `RowVersion`의 마지막 바이트가 표시됩니다. 마지막 바이트는 고유하게 보장되지 않습니다. 실제 앱에서는 `RowVersion` 또는 `RowVersion`의 마지막 바이트가 표시되지 않습니다.

인덱스 페이지를 업데이트합니다.

* 인덱스를 부서로 바꿉니다.
* `RowVersion`을 포함하는 표시를 `RowVersion`의 마지막 바이트로 바꿉니다.
* FirstMidName을 FullName으로 바꿉니다.

다음 표시는 업데이트된 페이지를 보여 줍니다.

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>편집 페이지 모델 업데이트

*Pages\Departments\Edit.cshtml.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

동시성 문제를 감지하기 위해 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)가 페치된 엔터티의 `rowVersion` 값으로 업데이트됩니다. EF Core는 원본 `RowVersion` 값을 포함하는 WHERE 절과 함께 SQL UPDATE 명령을 생성합니다. UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) `DbUpdateConcurrencyException` 예외가 throw됩니다.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

위의 코드에서 `Department.RowVersion`은 엔터티가 페치될 때 값입니다. `OriginalValue`는 `FirstOrDefaultAsync`가 이 메서드에서 호출될 때 DB의 값입니다.

다음 코드는 클라이언트 값(이 메서드에 게시된 값) 및 DB 값을 가져옵니다.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

다음 코드는 DB 값이 `OnPostAsync`에 게시된 값과 다른 각 열에 대한 사용자 오류 메시지를 추가합니다.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

다음 강조 표시된 코드는 `RowVersion` 값을 DB에서 검색된 새 값으로 설정합니다. 다음에 사용자가 **저장** 을 클릭하면, 편집 페이지의 마지막 표시 이후 발생한 동시성 오류만 catch됩니다.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다. Razor 페이지에서 필드의 `ModelState` 값은 모델 속성 값에 우선합니다(둘 다 있는 경우).

## <a name="update-the-edit-page"></a>편집 페이지 업데이트

*Pages/Departments/Edit.cshtml* 을 다음 표시로 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

위의 표시는:

* `page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.
* 숨겨진 행 버전을 추가합니다. 포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.
* 디버깅을 위해 `RowVersion`의 마지막 바이트를 표시합니다.
* `ViewData`를 강력한 형식의 `InstructorNameSL`로 바꿉니다.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>편집 페이지로 동시성 충돌 테스트

영어 부서에 있는 편집의 두 브라우저 인스턴스를 엽니다.

* 앱을 실행하고 부서를 선택합니다.
* 영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.
* 첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.

두 개의 브라우저 탭에 동일한 정보가 표시됩니다.

첫 번째 브라우저 탭의 이름을 변경하고 **저장** 을 클릭합니다.

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-1.png)

브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다. 업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.

두 번째 브라우저 탭에서 다른 필드를 변경합니다.

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-2.png)

**저장** 을 클릭합니다. DB 값과 일치하지 않는 모든 필드에 대한 오류 메시지가 표시됩니다.

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error.png)

이 브라우저 창은 Name 필드 변경용으로 의도되지 않았습니다. 현재 값(Languages)을 복사하여 Name 필드에 붙여넣습니다. 필드 밖을 탭합니다. 클라이언트 쪽 유효성 검사가 오류 메시지를 제거합니다.

![부서 편집 페이지 오류 메시지](concurrency/_static/cv.png)

**저장** 을 다시 클릭합니다. 두 번째 브라우저 탭에 입력한 값이 저장됩니다. 인덱스 페이지에 저장된 값이 표시됩니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

다음 코드로 삭제 페이지 모델을 업데이트합니다.

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

페이지 삭제는 엔터티가 페치된 후 변경될 때 동시성 충돌을 감지합니다. `Department.RowVersion`은 엔터티가 페치될 때 행 버전입니다. EF Core가 SQL DELETE 명령을 만들 때 `RowVersion`과 함께 WHERE 절이 포함됩니다. SQL DELETE 명령의 영향을 받는 행이 없는 경우:

* SQL DELETE 명령의 `RowVersion`이 DB의 `RowVersion`과 일치하지 않습니다.
* DbUpdateConcurrencyException 예외가 throw됩니다.
* `OnGetAsync`가 `concurrencyError`를 사용하여 호출됩니다.

### <a name="update-the-delete-page"></a>삭제 페이지 업데이트

*Pages/Departments/Delete.cshtml* 을 다음 코드로 업데이트합니다.

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

위의 코드로 다음이 변경됩니다.

* `page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.
* 오류 메시지를 추가합니다.
* **Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.
* 마지막 바이트를 표시하도록 `RowVersion`을 변경합니다.
* 숨겨진 행 버전을 추가합니다. 포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>삭제 페이지로 동시성 충돌 테스트

테스트 부서를 만듭니다.

테스트 부서에 있는 삭제의 두 브라우저 인스턴스를 엽니다.

* 앱을 실행하고 부서를 선택합니다.
* 테스트 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택합니다.
* 테스트 부서에 대한 **편집** 하이퍼링크를 클릭합니다.

두 개의 브라우저 탭에 동일한 정보가 표시됩니다.

첫 번째 브라우저 탭의 예산을 변경하고 **저장** 을 클릭합니다.

브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다. 업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.

두 번째 탭에서 테스트 부서를 삭제합니다. 동시성 오류는 DB의 현재 값으로 표시됩니다. `RowVersion`이 업데이트되지 않았다면 **삭제** 를 클릭하면 엔터티가 삭제됩니다.

데이터 모델을 상속하는 방법은 [상속](xref:data/ef-mvc/inheritance)을 참조하세요.

### <a name="additional-resources"></a>추가 자료

* [EF Core의 동시성 토큰](/ef/core/modeling/concurrency)
* [EF Core의 동시성 처리](/ef/core/saving/concurrency)
* [이 자습서의 YouTube 버전(동시성 충돌 처리)](https://youtu.be/EosxHTFgYps)
* [이 자습서의 YouTube 버전(2부)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [이 자습서의 YouTube 버전(3부)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [이전](xref:data/ef-rp/update-related-data)

::: moniker-end

