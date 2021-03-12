---
title: '자습서: 동시성 처리 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에는 여러 사용자가 동시에 같은 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: cc93069899953d04d1df4e79a282c349a163b06e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586776"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>자습서: 동시성 처리 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서 데이터를 업데이트하는 방법을 배웠습니다. 이 자습서에는 여러 사용자가 동시에 같은 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.

부서 엔터티를 사용하는 웹 페이지를 만들고 동시성 오류를 처리합니다. 다음 그림은 동시성 충돌이 발생하는 경우 표시되는 일부 메시지를 포함하여 편집 및 삭제 페이지를 보여 줍니다.

![부서 편집 페이지](concurrency/_static/edit-error.png)

![부서 삭제 페이지](concurrency/_static/delete-error.png)

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 동시성 충돌에 대해 알아보기
> * 추적 속성 추가
> * 부서 컨트롤러 및 뷰 만들기
> * 인덱스 뷰 업데이트
> * Edit 메서드 업데이트
> * 편집 뷰 업데이트
> * 동시성 충돌 테스트
> * 삭제 페이지 업데이트
> * 세부 정보 및 만들기 보기 업데이트

## <a name="prerequisites"></a>사전 요구 사항

* [관련 데이터 업데이트](update-related-data.md)

## <a name="concurrency-conflicts"></a>동시성 충돌

한 명의 사용자가 편집하기 위해 엔터티의 데이터를 표시한 다음, 다른 사용자가 첫 번째 사용자의 변경 내용이 데이터베이스에 기록되기 전에 동일한 엔터티의 데이터를 업데이트하는 경우 동시성 충돌이 발생합니다. 이러한 충돌의 감지를 활성화하지 않는 경우 누구든지 데이터베이스를 마지막으로 업데이트하면 다른 사용자의 변경 내용을 덮어씁니다. 많은 애플리케이션에서 이 위험은 허용 가능합니다. 적은 수의 사용자 또는 적은 업데이트가 있거나 일부 변경 내용이 덮어쓰여지는지 여부가 실제로 중요하지 않은 경우 동시성에 대한 프로그래밍의 비용은 이점보다 클 수 있습니다. 이 경우 동시성 충돌을 처리하도록 애플리케이션을 구성할 필요가 없습니다.

### <a name="pessimistic-concurrency-locking"></a>비관적 동시성(잠금)

애플리케이션에서 동시성 시나리오에서 실수로 인한 데이터 손실을 방지할 필요가 있는 경우 해당 작업을 수행하는 한 가지 방법은 데이터베이스 잠금을 사용하는 것입니다. 이를 비관적 동시성이라고 합니다. 예를 들어 데이터베이스에서 행을 읽기 전에 읽기 전용 또는 업데이트 액세스에 대한 잠금을 요청합니다. 업데이트 액세스에 대한 행을 잠그는 경우 변경 중인 데이터의 복사본을 가져오기 때문에 다른 사용자는 읽기 전용 또는 업데이트 액세스에 대한 행을 잠그도록 허용되지 않습니다. 읽기 전용 액세스에 대한 행을 잠그는 경우 다른 사용자도 읽기 전용에 대해 잠글 수 있지만 업데이트에 대해서는 잠글 수 없습니다.

잠금 관리에는 단점이 있습니다. 프로그램을 설정하는 데 복잡할 수 있습니다. 상당한 데이터베이스 관리 리소스가 필요하며 애플리케이션의 사용자 수가 증가할 수록 성능 문제가 발생할 수 있습니다. 이러한 이유로 모든 데이터베이스 관리 시스템은 비관적 동시성을 지원하지 않습니다. Entity Framework Core는 이에 대한 기본 제공 지원을 제공하지 않으며 이 자습서에서는 구현하는 방법을 보여 주지 않습니다.

### <a name="optimistic-concurrency"></a>낙관적 동시성

비관적 동시성에 대한 대안은 낙관적 동시성입니다. 낙관적 동시성은 동시성 충돌 발생을 허용한 다음, 그럴 경우 적절하게 반응하는 것을 의미합니다. 예를 들어 Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.

![예산을 0으로 변경](concurrency/_static/change-budget.png)

Jane이 **저장** 을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.

![시작 날짜를 2013으로 변경](concurrency/_static/change-date.png)

Jane이 먼저 **저장** 을 클릭하여 브라우저가 인덱스 페이지로 반환될 때 변경 사항을 확인합니다.

![0으로 변경된 예산](concurrency/_static/budget-zero.png)

그런 다음, John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장** 을 클릭합니다. 다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.

몇 가지 옵션에는 다음이 포함됩니다.

* 사용자가 수정한 속성의 추적을 유지하고 데이터베이스에서 해당하는 열만 업데이트할 수 있습니다.

     예제 시나리오에서 서로 다른 속성이 두 사용자에 의해 업데이트되었기 때문에 데이터가 손실되지 않습니다. 다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다. - 2013년 9월 1일의 시작 날짜 및 0달러의 예산 이 업데이트의 메서드는 데이터 손실이 발생할 수 있는 충돌 수를 줄일 수 있지만 경쟁하는 변경 내용이 동일한 엔터티의 속성에 만들어지는 경우 데이터 손실을 방지할 수 없습니다. Entity Framework가 이 방식으로 작동하는지 여부는 업데이트 코드를 구현하는 방법에 따라 달라집니다. 엔터티에 대한 모든 기존 속성 값 뿐만 아니라 새 값의 추적을 유지하기 위해 많은 양의 상태를 유지 관리해야 하므로 웹 애플리케이션에서는 종종 실용적이지 않습니다. 서버 리소스가 필요하거나 웹 페이지 자체(예: 숨겨진 필드에) 또는 cookie에 포함되어야 하기 때문에 많은 양의 상태를 유지 관리하는 것은 애플리케이션 성능에 영향을 줄 수 있습니다.

* Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.

     다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 복원된 $350,000.00 값을 볼 수 있습니다. 이를 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다. (클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 이 섹션의 소개에서 언급했듯이 동시성 처리에 대해 아무런 코딩을 수행하지 않는 경우 이는 자동으로 발생합니다.

* John의 변경 내용이 데이터베이스에서 업데이트되지 않도록 할 수 있습니다.

     일반적으로 오류 메시지를 표시하고, 데이터의 현재 상태를 보여 주고, 여전히 변경하고자 하는 경우 변경 내용을 다시 적용하도록 허용합니다. 이를 *저장소 우선* 시나리오라고 합니다. (데이터 저장소 값은 클라이언트에서 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다. 이 메서드는 상황에 대한 경고를 받는 사용자 없이 변경 내용을 덮어쓰지 않도록 합니다.

### <a name="detecting-concurrency-conflicts"></a>동시성 충돌 검색

Entity Framework에서 throw하는 `DbConcurrencyException` 예외를 처리하여 충돌을 해결할 수 있습니다. 이러한 예외를 throw하는 시기를 확인하기 위해 Entity Framework에서 충돌을 검색할 수 있어야 합니다. 따라서 데이터베이스와 데이터 모델을 적절하게 구성해야 합니다. 충돌 검색을 활성화하기 위한 몇 가지 옵션은 다음과 같습니다.

* 데이터베이스 테이블에서 행이 변경된 시기를 확인하는 데 사용될 수 있는 추적 열을 포함합니다. 그런 다음, SQL Update의 Where 절 또는 Delete 명령에 해당 열을 포함하도록 Entity Framework를 구성할 수 있습니다.

     추적 열의 데이터 형식은 일반적으로 `rowversion`입니다. `rowversion` 값은 행이 업데이트될 때마다 증가되는 순차적 번호입니다. Update 또는 Delete 명령에서 Where 절은 추적 열의 원래 값을 포함합니다(원래 행 버전). 업데이트되는 행이 다른 사용자에 의해 변경된 경우 `rowversion` 열의 값은 원래 값과 다르므로 Update 또는 Delete 문은 Where 절 때문에 업데이트할 행을 찾을 수 없습니다. Entity Framework에서 Update 또는 Delete 명령에 의해 업데이트된 행이 없다는 것을 찾은 경우(즉, 영향을 받은 행의 수가 0인 경우) 동시성 충돌로 해석합니다.

* Update 및 Delete 명령의 Where 절에서 테이블의 모든 열의 원래 값을 포함하도록 Entity Framework를 구성합니다.

     첫 번째 옵션에서 행이 먼저 읽혔기 때문에 행의 일부가 변경된 경우 Where 절은 업데이트할 열을 반환하지 않습니다. Entity Framework는 동시성 충돌로 해석합니다. 많은 열이 있는 데이터베이스 테이블의 경우 이 방법으로 많은 Where 절이 발생할 수 있으며 많은 양의 상태를 유지 관리해야 할 수 있습니다. 앞에서 설명한 것처럼 많은 양의 상태를 유지 관리하는 것은 애플리케이션 성능에 영향을 미칠 수 있습니다. 따라서 이 방법은 일반적으로 권장되지 않으며 이 자습서에서 사용되는 방법이 아닙니다.

     동시성에 대해 이 방법을 구현하려는 경우 `ConcurrencyCheck` 특성을 추가하여 동시성을 추적하려는 엔터티의 모든 비 기본 키 속성을 표시해야 합니다. 해당 변경 내용을 통해 Entity Framework는 Update 및 Delete 문의 SQL Where 절에 모든 열을 포함할 수 있습니다.

이 자습서의 나머지 부분에서는 부서 엔터티에 `rowversion` 추적 속성을 추가하고, 컨트롤러와 보기를 만들고, 모든 항목이 올바르게 작동하는지 확인하도록 테스트합니다.

## <a name="add-a-tracking-property"></a>추적 속성 추가

*Models/Department.cs* 에서 RowVersion으로 명명된 추적 속성을 추가합니다.

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

`Timestamp` 특성은 데이터베이스에 전송된 Update 및 Delete 명령의 Where 절에 이 열이 포함되도록 지정합니다. SQL `rowversion`이 대체하기 전에 이전 버전의 SQL Server가 SQL `timestamp` 데이터 형식을 사용했으므로 특성을 `Timestamp`라고 합니다. `rowversion`에 대한 .NET 유형은 바이트 배열입니다.

Fluent API를 사용하는 것을 선호하는 경우 `IsConcurrencyToken` 메서드(*Data/SchoolContext.cs* 에서)를 사용하여 다음 예제와 같이 추적 속성을 지정할 수 있습니다.

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

속성을 추가하여 데이터베이스 모델을 변경했으므로 다른 마이그레이션을 수행해야 합니다.

변경 내용을 저장하고 프로젝트를 빌드한 다음, 명령 창에 다음 명령을 입력합니다.

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>부서 컨트롤러 및 뷰 만들기

학생, 강좌 및 강사에 대해 이전에 수행한 것처럼 부서 컨트롤러와 보기를 스캐폴드합니다.

![부서 스캐폴드](concurrency/_static/add-departments-controller.png)

*DepartmentsController.cs* 파일에서 부서 관리자 드롭다운 목록이 강사의 성 보다는 전체 이름을 포함하도록 "FirstMidName"에서 "FullName"으로 네 개의 모든 항목을 변경합니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>인덱스 뷰 업데이트

스캐폴딩 엔진은 인덱스 보기에 RowVersion 열을 만들었지만 해당 필드는 표시되지 않아야 합니다.

*Views/Departments/Index.cshtml* 의 코드를 다음 코드로 바꿉니다.

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

이는 제목을 "부서"로 변경하고, RowVersion 열을 삭제하고, 관리자의 이름 대신 전체 이름을 표시합니다.

## <a name="update-edit-methods"></a>Edit 메서드 업데이트

HttpGet `Edit` 메서드 및 `Details` 메서드 모두에 `AsNoTracking`을 추가합니다. HttpGet `Edit` 메서드에서 관리자에 대해 즉시 로드를 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

HttpPost `Edit` 메서드에 대한 기존 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

코드는 업데이트될 부서 읽기를 시도하여 시작합니다. `FirstOrDefaultAsync` 메서드가 Null을 반환하는 경우 부서가 다른 사용자에 의해 삭제되었습니다. 이 경우 코드는 편집 페이지가 오류 메시지와 함께 다시 표시될 수 있도록 게시된 양식 값을 사용하여 부서 엔터티를 만듭니다. 대신 부서 필드를 다시 표시하지 않고 오류 메시지만을 표시하는 경우 부서 엔터티를 다시 만들 필요가 없습니다.

보기는 숨겨진 필드에 원래 `RowVersion` 값을 저장하고, 이 메서드는 `rowVersion` 매개 변수에서 해당 값을 받습니다. `SaveChanges`를 호출하기 전에 엔터티에 대한 `OriginalValues` 컬렉션에 해당 원래 `RowVersion` 속성 값을 넣어야 합니다.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

그런 다음, Entity Framework에서 SQL UPDATE 명령을 만들 때 해당 명령은 원래 `RowVersion` 값이 있는 행을 찾는 WHERE 절을 포함합니다. UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) Entity Framework는 `DbUpdateConcurrencyException` 예외를 throw합니다.

해당 예외에 대한 catch 블록의 코드에서 예외 개체의 `Entries` 속성에서 업데이트된 값이 있는 영향을 받은 부서 엔터티를 가져옵니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

`Entries` 컬렉션은 하나의 `EntityEntry` 개체만 갖습니다.  해당 개체를 사용하여 사용자가 입력한 새 값 및 현재 데이터베이스 값을 가져올 수 있습니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

코드는 사용자가 편집 페이지에서 입력한 것과 다른 데이터베이스 값이 있는 각 열에 대해 사용자 지정 오류 메시지를 추가합니다(여기에서는 간단히 하기 위해 하나의 필드만 표시됨).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

마지막으로 코드는 `departmentToUpdate`의 `RowVersion` 값을 데이터베이스에서 검색된 새 값으로 설정합니다. 이 새로운 `RowVersion` 값은 편집 페이지가 다시 표시되고, 다음 번에 사용자가 **저장** 을 클릭할 때 숨겨진 필드에 저장되고, 편집 페이지의 다시 표시로 인해 발생하는 동시성 오류만 catch됩니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다. 보기에서 필드에 대한 `ModelState` 값은 모델 속성 값에 우선합니다(둘 다 있는 경우).

## <a name="update-edit-view"></a>편집 뷰 업데이트

*Views/Departments/Edit.cshtml* 에서 다음과 같이 변경합니다.

* 숨겨진 필드를 추가하여 `DepartmentID` 속성에 대한 숨겨진 필드 바로 다음에 `RowVersion` 속성 값을 저장합니다.

* 드롭다운 목록에 "관리자 선택" 옵션을 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>동시성 충돌 테스트

앱을 실행하고 부서 인덱스 페이지로 이동합니다. 영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택한 다음, 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다. 이제 두 개의 브라우저 탭에 동일한 정보가 표시됩니다.

첫 번째 브라우저 탭의 필드를 변경하고 **저장** 을 클릭합니다.

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-1.png)

브라우저에 변경된 값과 인덱스 페이지가 표시됩니다.

두 번째 브라우저 탭에서 필드를 변경합니다.

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-2.png)

**저장** 을 클릭합니다. 오류 메시지가 표시됩니다.

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error.png)

**저장** 을 다시 클릭합니다. 두 번째 브라우저 탭에 입력한 값이 저장됩니다. 인덱스 페이지가 나타날 때 저장된 값이 표시됩니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

삭제 페이지의 경우 Entity Framework는 비슷한 방식으로 부서를 편집하는 사용자에 의해 발생한 동시성 충돌을 감지합니다. HttpGet `Delete` 메서드가 확인 보기를 표시하는 경우 보기는 숨겨진 필드에 원래 `RowVersion` 값을 포함합니다. 그러면 해당 값을 사용자가 삭제를 확인할 때 호출된 HttpPost `Delete` 메서드에 사용할 수 있습니다. Entity Framework가 SQL DELETE 명령을 만들 때 원래 `RowVersion` 값과 함께 WHERE 절을 포함합니다. 명령으로 인해 영향을 받은 0개의 행이 발생하는 경우(삭제 확인 페이지가 표시된 후 행이 변경되었음을 의미함) 동시성 예외가 throw되고, HttpGet `Delete` 메서드가 오류 메시지와 함께 확인 페이지를 다시 표시하기 위해 true로 설정된 오류 플래그와 함께 호출됩니다. 행이 다른 사용자에 의해 삭제되었기 때문에 0개의 행이 영향을 받았을 수도 있습니다. 따라서 이 경우 오류 메시지가 표시되지 않습니다.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>부서 컨트롤러의 Delete 메서드 업데이트

*DepartmentsController.cs* 에서 HttpGet `Delete` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

메서드는 동시성 오류가 발생한 후 페이지가 다시 표시되고 있는지 여부를 나타내는 선택적 매개 변수를 허용합니다. 이 플래그가 true이고 지정된 부서가 더 이상 존재하지 않는 경우 다른 사용자에 의해 삭제되었습니다. 이 경우 코드는 인덱스 페이지로 리디렉션합니다.  이 플래그가 true이고 부서가 존재하는 경우 다른 사용자에 의해 변경되었습니다. 이 경우 코드는 `ViewData`를 사용하여 보기에 오류 메시지를 보냅니다.

HttpPost `Delete` 메서드의 코드(`DeleteConfirmed`라는)를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

방금 바꾼 스캐폴드된 코드에서 이 메서드는 레코드 ID만 허용했습니다.

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

이 매개 변수를 모델 바인더로 만든 부서 엔터티 인스턴스로 변경했습니다. 이는 레코드 키 뿐만 아니라 RowVersion 속성 값에 대한 EF 액세스를 제공합니다.

```csharp
public async Task<IActionResult> Delete(Department department)
```

또한 `DeleteConfirmed`에서 `Delete`로 작업 메서드 이름을 변경했습니다. 스캐폴드된 코드는 HttpPost 메서드에 고유한 서명을 제공하기 위해 이름 `DeleteConfirmed`를 사용했습니다. (CLR은 다른 메서드 매개 변수를 갖기 위해 오버로드된 메서드가 필요합니다.) 이제 서명이 고유하므로 MVC 규칙을 유지하고 HttpPost 및 HttpGet delete 메서드에 대해 동일한 이름을 사용할 수 있습니다.

부서가 이미 삭제된 경우 `AnyAsync` 메서드에서 false를 반환하고 애플리케이션은 인덱스 메서드로 다시 이동합니다.

동시성 오류가 catch되는 경우 코드는 삭제 확인 페이지를 다시 표시하고 동시성 오류 메시지를 표시해야 함을 나타내는 플래그를 제공합니다.

### <a name="update-the-delete-view"></a>삭제 보기 업데이트

*Views/Departments/Delete.cshtml* 에서 스캐폴드된 코드를 DepartmentID 및 RowVersion 속성에 대해 오류 메시지 필드와 숨겨진 필드를 추가하는 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

이렇게 하면 다음이 변경됩니다.

* `h2` 및 `h3` 제목 사이에 오류 메시지를 추가합니다.

* **Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.

* RowVersion 필드를 제거합니다.

* `RowVersion` 속성에 대해 숨겨진 필드를 추가합니다.

앱을 실행하고 부서 인덱스 페이지로 이동합니다. 영어 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기** 를 선택한 다음, 첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.

첫 번째 창에서 값 중 하나를 변경하고, **저장** 을 클릭합니다.

![삭제 전 변경 후 부서 편집 페이지](concurrency/_static/edit-after-change-for-delete.png)

두 번째 탭에서 **삭제** 를 클릭합니다. 동시성 오류 메시지가 표시되며 부서 값은 데이터베이스의 현재 값으로 새로 고쳐집니다.

![동시성 오류가 있는 부서 삭제 확인 페이지](concurrency/_static/delete-error.png)

**삭제** 를 다시 클릭하면 부서가 삭제되었음을 보여 주는 인덱스 페이지로 리디렉션됩니다.

## <a name="update-details-and-create-views"></a>세부 정보 및 만들기 보기 업데이트

세부 정보 및 만들기 보기에서 스캐폴드된 코드를 필요에 따라 정리할 수 있습니다.

*Views/Departments/Details.cshtml* 의 코드를 바꿔 RowVersion 열을 삭제하고 관리자의 전체 이름을 표시합니다.

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

*Views/Departments/Create.cshtml* 의 코드를 바꿔 드롭다운 목록에 선택 옵션을 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>추가 자료

 EF Core에서 동시성을 처리하는 방법에 대한 자세한 내용은 [동시성 충돌](/ef/core/saving/concurrency)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 동시성 충돌에 대해 알아보기
> * 추적 속성 추가
> * 부서 컨트롤러 및 뷰 만들기
> * 인덱스 뷰 업데이트
> * Edit 메서드 업데이트
> * 편집 뷰 업데이트
> * 동시성 충돌 테스트
> * 삭제 페이지 업데이트
> * 세부 정보 및 만들기 뷰 업데이트

강사 및 학생 엔터티에 대한 계층당 테이블 상속을 구현하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [다음: 계층당 하나의 테이블 상속 구현](inheritance.md)
