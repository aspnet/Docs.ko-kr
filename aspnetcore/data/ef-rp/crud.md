---
title: 2부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - CRUD
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 2부입니다.
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
uid: data/ef-rp/crud
ms.openlocfilehash: c5b9be64ea30cce7a3178bfbb244ef893e9639d2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053867"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a>2부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - CRUD

작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.

## <a name="no-repository"></a>리포지토리 없음

일부 개발자는 서비스 계층 또는 리포지토리 패턴을 사용하여 UI(Razor Pages) 및 데이터 액세스 계층 간에 추상화 계층을 만듭니다. 이 자습서에서는 이 작업을 수행하지 않습니다. 복잡성을 최소화하고 자습서의 초점을 EF Core로 유지하기 위해 EF Core 코드가 페이지 모델 클래스에 직접 추가됩니다. 

## <a name="update-the-details-page"></a>세부 정보 페이지 업데이트

학생 페이지의 스캐폴드된 코드에는 등록 데이터가 포함되지 않습니다. 이 섹션에서는 세부 정보 페이지에 등록을 추가합니다.

### <a name="read-enrollments"></a>등록 읽기

학생의 등록 데이터를 페이지에 표시하려면 해당 데이터를 읽어야 합니다. *Pages/Students/Details.cshtml.cs* 의 스캐폴드된 코드는 등록 데이터 없이 학생 데이터만 읽습니다.

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

`OnGetAsync` 메서드를 다음 코드로 바꿔서 선택한 학생에 대한 등록 데이터를 읽습니다. 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다. 이 메서드는 [읽기 관련 데이터](xref:data/ef-rp/read-related-data) 자습서에서 자세히 검토합니다.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다. `AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.

### <a name="display-enrollments"></a>등록 표시

*Pages/Students/Details.cshtml* 의 코드를 다음 코드로 바꿔서 등록 목록을 표시합니다. 변경 내용은 강조 표시되어 있습니다.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다. 각 등록의 경우 강좌 제목과 등급을 표시합니다. 강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.

앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다. 선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.

### <a name="ways-to-read-one-entity"></a>단일 엔터티를 읽는 방법

생성된 코드는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용하여 하나의 엔터티를 읽습니다. 이 메서드는 검색된 항목이 없는 경우 Null을 반환합니다. 그렇지 않으면 쿼리 필터 조건을 충족하는 첫 번째 행을 반환합니다. `FirstOrDefaultAsync`는 일반적으로 다음 대안보다 더 적합한 선택입니다.

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 쿼리 필터를 충족하는 엔터티가 둘 이상인 경우 예외를 throw합니다. 쿼리에서 두 개 이상의 행이 반환될 수 있는지 확인하기 위해 `SingleOrDefaultAsync`는 여러 행을 페치하려고 시도합니다. 쿼리가 고유 키를 검색하는 경우처럼 하나의 엔터티만 반환할 수 있는 경우에는 이 추가 작업이 필요하지 않습니다.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - PK(기본 키)가 있는 엔터티를 찾습니다. PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 데이터베이스에 반환됩니다. 이 메서드는 단일 엔터티를 조회하는 데 최적화되어 있지만 `FindAsync`를 사용하여 `Include`를 호출할 수 없습니다.  따라서 관련 데이터가 필요한 경우 `FirstOrDefaultAsync`를 선택하는 것이 좋습니다.

### <a name="route-data-vs-query-string"></a>경로 데이터 및 쿼리 문자열

세부 정보 페이지의 URL은 `https://localhost:<port>/Students/Details?id=1`입니다. 엔터티의 기본 키 값은 쿼리 문자열에 있습니다. 일부 개발자는 키 값을 경로 데이터로 전달하는 것을 선호합니다. `https://localhost:<port>/Students/Details/1` 자세한 내용은 [생성된 코드 업데이트](xref:tutorials/razor-pages/da1#update-the-generated-code)를 참조하세요.

## <a name="update-the-create-page"></a>만들기 페이지 업데이트

만들기 페이지의 스캐폴드된 `OnPostAsync` 코드는 [초과 게시](#overposting)에 취약합니다. *Pages/Students/Create.cshtml.cs* 의 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

앞의 코드는 Student 개체를 만든 다음, 게시된 양식 필드를 사용하여 Student 개체의 속성을 업데이트합니다. [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 메서드:

* [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)에서 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 속성의 게시된 양식 값을 사용합니다.
* 나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* “Student” 접두사가 있는 양식 필드를 찾습니다. 예: `Student.FirstMidName`. 대/소문자를 구분하지 않습니다.
* [모델 바인딩](xref:mvc/models/model-binding) 시스템을 사용하여 양식 값을 문자열에서 `Student` 모델의 형식으로 변환합니다. 예를 들어 `EnrollmentDate`가 `DateTime`으로 변환됩니다.

앱을 실행하고 학생 엔터티를 만들어 만들기 페이지를 테스트합니다.

## <a name="overposting"></a>초과 게시

게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다. 예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

앱에 만들기 또는 업데이트 Razor 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다. 해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다. 원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.

해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 데이터베이스에서 업데이트됩니다. 다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다. 앱 디자이너가 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않더라도 추가됩니다.

### <a name="view-model"></a>뷰 모델

뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다.

애플리케이션 모델은 흔히 도메인 모델이라고 합니다. 도메인 모델은 일반적으로 데이터베이스의 해당 엔터티에 필요한 모든 속성을 포함합니다. 뷰 모델은 UI 페이지(예: 만들기 페이지)에 필요한 속성만 포함합니다.

뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 Razor Pages 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다. 

다음 `StudentVM` 뷰 모델을 살펴보세요.

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다. `SetValues`는 속성 이름 일치를 사용합니다. 뷰 모델 형식:

* 모델 유형과 관련될 필요는 없습니다.
* 일치하는 속성이 있어야 합니다.

`StudentVM`을 사용하려면 만들기 페이지가 `Student` 대신 `StudentVM`을 사용해야 합니다.

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a>편집 페이지 업데이트

*Pages/Students/Edit.cshtml.cs* 에서 `OnGetAsync` 및 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.

* `FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다. 관련 데이터를 포함할 필요가 없는 경우에는 `FindAsync`가 더 효율적입니다.
* `OnPostAsync`에는 `id` 매개 변수가 있습니다.
* 현재 학생은 빈 학생을 만드는 대신 데이터베이스에서 페치합니다.

앱을 실행한 후 학생을 만들고 편집하여 테스트합니다.

## <a name="entity-states"></a>엔터티 상태

데이터베이스 컨텍스트는 메모리의 엔터티가 해당하는 데이터베이스의 행과 동기화하는지 여부를 추적합니다. 이 추적 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다. 예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다. `SaveChangesAsync`가 호출되면 데이터베이스 컨텍스트가 SQL `INSERT` 명령을 발급합니다.

엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.

* `Added`: 엔터티가 아직 데이터베이스에 존재하지 않습니다. `SaveChanges` 메서드는 `INSERT` 문을 발급합니다.

* `Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다. 엔터티는 데이터베이스에서 읽을 때 이 상태입니다.

* `Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다. `SaveChanges` 메서드는 `UPDATE` 문을 발급합니다.

* `Deleted`: 엔터티가 삭제되도록 표시되었습니다. `SaveChanges` 메서드는 `DELETE` 문을 발급합니다.

* `Detached`: 엔터티가 데이터베이스 컨텍스트에 의해 추적되지 않습니다.

데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다. 엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다. `SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL `UPDATE` 문이 생성됩니다.

웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다. 페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다. 새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리가 시뮬레이트됩니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

이 섹션에서는 `SaveChanges` 호출이 실패하는 경우의 사용자 지정 오류 메시지를 구현합니다.

*Pages/Students/Delete.cshtml.cs* 의 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

앞의 코드는 선택적 매개 변수 `saveChangesError`를 `OnGetAsync` 메서드 시그니처에 추가합니다. `saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다. 일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다. 일시적인 네트워크 오류는 데이터베이스가 클라우드에 있을 때 발생 가능성이 더 큽니다. `saveChangesError` 매개 변수는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 `false`입니다. `OnGetAsync`가 `OnPostAsync`에 의해 호출되면 삭제 작업이 실패했으므로 `saveChangesError` 매개 변수는 `true`입니다.

`OnPostAsync` 메서드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다. `SaveChanges`가 호출되면 SQL `DELETE` 명령이 생성됩니다. `Remove`가 실패하는 경우:

* 데이터베이스 예외가 catch되었습니다.
* 삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.

*Pages/Students/Delete.cshtml* 에 오류 메시지를 추가합니다.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

앱을 실행하고 학생을 삭제하여 삭제 페이지를 테스트합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [이전 자습서](xref:data/ef-rp/intro)
> [다음 자습서](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.

## <a name="no-repository"></a>리포지토리 없음

일부 개발자는 서비스 계층 또는 리포지토리 패턴을 사용하여 UI(Razor Pages) 및 데이터 액세스 계층 간에 추상화 계층을 만듭니다. 이 자습서에서는 이 작업을 수행하지 않습니다. 복잡성을 최소화하고 자습서의 초점을 EF Core로 유지하기 위해 EF Core 코드가 페이지 모델 클래스에 직접 추가됩니다. 

## <a name="update-the-details-page"></a>세부 정보 페이지 업데이트

학생 페이지의 스캐폴드된 코드에는 등록 데이터가 포함되지 않습니다. 이 섹션에서는 세부 정보 페이지에 등록을 추가합니다.

### <a name="read-enrollments"></a>등록 읽기

페이지에 학생의 등록 데이터를 표시하려면 등록 데이터를 읽어야 합니다. *Pages/Students/Details.cshtml.cs* 의 스캐폴드된 코드는 등록 데이터 없이 학생 데이터만 읽습니다.

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

`OnGetAsync` 메서드를 다음 코드로 바꿔서 선택한 학생에 대한 등록 데이터를 읽습니다. 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다. 이 메서드는 [읽기 관련 데이터](xref:data/ef-rp/read-related-data) 자습서에서 자세히 검토합니다.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다. `AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.

### <a name="display-enrollments"></a>등록 표시

*Pages/Students/Details.cshtml* 의 코드를 다음 코드로 바꿔서 등록 목록을 표시합니다. 변경 내용은 강조 표시되어 있습니다.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다. 각 등록의 경우 강좌 제목과 등급을 표시합니다. 강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.

앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다. 선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.

### <a name="ways-to-read-one-entity"></a>단일 엔터티를 읽는 방법

생성된 코드는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용하여 하나의 엔터티를 읽습니다. 이 메서드는 검색된 항목이 없는 경우 Null을 반환합니다. 그렇지 않으면 쿼리 필터 조건을 충족하는 첫 번째 행을 반환합니다. `FirstOrDefaultAsync`는 일반적으로 다음 대안보다 더 적합한 선택입니다.

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 쿼리 필터를 충족하는 엔터티가 둘 이상인 경우 예외를 throw합니다. 쿼리에서 두 개 이상의 행이 반환될 수 있는지 확인하기 위해 `SingleOrDefaultAsync`는 여러 행을 페치하려고 시도합니다. 쿼리가 고유 키를 검색하는 경우처럼 하나의 엔터티만 반환할 수 있는 경우에는 이 추가 작업이 필요하지 않습니다.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - PK(기본 키)가 있는 엔터티를 찾습니다. PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 데이터베이스에 반환됩니다. 이 메서드는 단일 엔터티를 조회하는 데 최적화되어 있지만 `FindAsync`를 사용하여 `Include`를 호출할 수 없습니다.  따라서 관련 데이터가 필요한 경우 `FirstOrDefaultAsync`를 선택하는 것이 좋습니다.

### <a name="route-data-vs-query-string"></a>경로 데이터 및 쿼리 문자열

세부 정보 페이지의 URL은 `https://localhost:<port>/Students/Details?id=1`입니다. 엔터티의 기본 키 값은 쿼리 문자열에 있습니다. 일부 개발자는 키 값을 경로 데이터로 전달하는 것을 선호합니다. `https://localhost:<port>/Students/Details/1` 자세한 내용은 [생성된 코드 업데이트](xref:tutorials/razor-pages/da1#update-the-generated-code)를 참조하세요.

## <a name="update-the-create-page"></a>만들기 페이지 업데이트

만들기 페이지의 스캐폴드된 `OnPostAsync` 코드는 [초과 게시](#overposting)에 취약합니다. *Pages/Students/Create.cshtml.cs* 의 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

앞의 코드는 Student 개체를 만든 다음, 게시된 양식 필드를 사용하여 Student 개체의 속성을 업데이트합니다. [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 메서드:

* [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)에서 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 속성의 게시된 양식 값을 사용합니다.
* 나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* “Student” 접두사가 있는 양식 필드를 찾습니다. 예: `Student.FirstMidName`. 대/소문자를 구분하지 않습니다.
* [모델 바인딩](xref:mvc/models/model-binding) 시스템을 사용하여 양식 값을 문자열에서 `Student` 모델의 형식으로 변환합니다. 예를 들어 `EnrollmentDate`는 DateTime으로 변환해야 합니다.

앱을 실행하고 학생 엔터티를 만들어 만들기 페이지를 테스트합니다.

## <a name="overposting"></a>초과 게시

게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다. 예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

앱에 만들기 또는 업데이트 Razor 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다. 해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다. 원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.

해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 데이터베이스에서 업데이트됩니다. 다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다. 앱 디자이너가 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않더라도 추가됩니다.

### <a name="view-model"></a>뷰 모델

뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다.

애플리케이션 모델은 흔히 도메인 모델이라고 합니다. 도메인 모델은 일반적으로 데이터베이스의 해당 엔터티에 필요한 모든 속성을 포함합니다. 보기 모델은 사용되는 UI에 필요한 속성만 포함합니다(예: 만들기 페이지).

뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 Razor Pages 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다. 

다음 `Student` 뷰 모델을 살펴보세요.

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다. `SetValues`는 속성 이름 일치를 사용합니다. 뷰 모델 형식은 모델 형식과 연결될 필요는 없으며 일치하는 속성만 있으면 됩니다.

`StudentVM`을 사용하려면 `Student` 대신 `StudentVM`을 사용하도록 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml)을 업데이트해야 합니다.

## <a name="update-the-edit-page"></a>편집 페이지 업데이트

*Pages/Students/Edit.cshtml.cs* 에서 `OnGetAsync` 및 `OnPostAsync` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.

* `FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다. 포함된 관련 데이터가 필요하지 않은 경우 `FindAsync`가 더 효율적입니다.
* `OnPostAsync`에는 `id` 매개 변수가 있습니다.
* 현재 학생은 빈 학생을 만드는 대신 데이터베이스에서 페치합니다.

앱을 실행한 후 학생을 만들고 편집하여 테스트합니다.

## <a name="entity-states"></a>엔터티 상태

데이터베이스 컨텍스트는 메모리의 엔터티가 해당하는 데이터베이스의 행과 동기화하는지 여부를 추적합니다. 이 추적 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다. 예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다. `SaveChangesAsync`가 호출되면 데이터베이스 컨텍스트가 SQL INSERT 명령을 실행합니다.

엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.

* `Added`: 엔터티가 아직 데이터베이스에 존재하지 않습니다. `SaveChanges` 메서드가 INSERT 문을 발급합니다.

* `Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다. 엔터티는 데이터베이스에서 읽을 때 이 상태입니다.

* `Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다. `SaveChanges` 메서드는 UPDATE 문을 발급합니다.

* `Deleted`: 엔터티가 삭제되도록 표시되었습니다. `SaveChanges` 메서드는 DELETE 문을 발급합니다.

* `Detached`: 엔터티가 데이터베이스 컨텍스트에 의해 추적되지 않습니다.

데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다. 엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다. `SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL UPDATE 문이 생성됩니다.

웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다. 페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다. 새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리가 시뮬레이트됩니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

이 섹션에서는 `SaveChanges` 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현합니다.

*Pages/Students/Delete.cshtml.cs* 의 코드를 다음 코드로 바꿉니다. 변경 내용이 강조 표시됩니다(`using` 문도 정리됨).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

앞의 코드는 선택적 매개 변수 `saveChangesError`를 `OnGetAsync` 메서드 시그니처에 추가합니다. `saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다. 일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다. 일시적인 네트워크 오류는 데이터베이스가 클라우드에 있을 때 발생 가능성이 더 큽니다. `saveChangesError` 매개 변수는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 false입니다. `OnGetAsync`가 `OnPostAsync`에 의해 호출되면(삭제 작업이 실패했으므로) `saveChangesError` 매개 변수는 true입니다.

`OnPostAsync` 메서드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다. `SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다. `Remove`가 실패하는 경우:

* 데이터베이스 예외가 catch되었습니다.
* 삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.

삭제 Razor 페이지( *Pages/Students/Delete.cshtml* )에 오류 메시지를 추가합니다.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

앱을 실행하고 학생을 삭제하여 삭제 페이지를 테스트합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="step-by-step"]
> [이전 자습서](xref:data/ef-rp/intro)
> [다음 자습서](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.

복잡성을 최소화하고 이러한 자습서의 초점을 EF Core로 유지하기 위해 페이지 모델에 EF Core 코드를 사용합니다. 일부 개발자는 UI(Razor Pages) 및 데이터 액세스 계층 간에 추상화 계층을 생성하도록 서비스 계층 또는 리포지토리 패턴을 사용합니다.

이 자습서에서는 *Students* 폴더에서 만들기, 편집, 삭제 및 세부 정보 Razor Pages가 검사됩니다.

스캐폴드된 코드는 만들기, 편집 및 삭제 페이지에 대해 다음과 같은 패턴을 사용합니다.

* HTTP GET 메서드 `OnGetAsync`로 요청된 데이터를 가져오고 표시합니다.
* HTTP POST 메서드 `OnPostAsync`로 데이터의 변경 내용을 저장합니다.

인덱스 및 세부 정보 페이지는 HTTP GET 메서드 `OnGetAsync`로 요청된 데이터를 가져오고 표시합니다.

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync 및 FirstOrDefaultAsync

생성된 코드는 [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)보다 일반적으로 선호되는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용합니다.

 하나의 엔터티를 페치할 때 `SingleOrDefaultAsync`보다 `FirstOrDefaultAsync`가 더 효율적입니다.

* 쿼리에서 반환된 엔터티가 두 개 이상이 아닌지 코드에서 확인해야 하는 경우 제외.
* `SingleOrDefaultAsync`는 더 많은 데이터를 페치하고 불필요한 작업을 수행합니다.
* `SingleOrDefaultAsync`는 필터 파트에 맞는 엔터티가 둘 이상인 경우 예외를 throw합니다.
* `FirstOrDefaultAsync`는 필터 파트에 맞는 엔터티가 둘 이상인 경우 예외를 throw하지 않습니다.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

스캐폴드된 코드의 대부분에서 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___)를 `FirstOrDefaultAsync` 대신 사용할 수 있습니다.

`FindAsync`:

* PK(기본 키)가 있는 엔터티를 찾습니다. PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 DB에 반환됩니다.
* 단순하고 간결합니다.
* 단일 엔터티를 조회하도록 최적화되었습니다.
* 일부 상황에서는 성능 이점을 가질 수 있지만 일반적인 웹앱에서는 거의 발생하지 않습니다.
* [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 대신 [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)를 암시적으로 사용합니다.

하지만 다른 엔터티를 `Include`하려는 경우 `FindAsync`는 더 이상 적합하지 않습니다. 즉, `FindAsync`를 취소하고 앱 진행에 따라 쿼리로 이동해야 할 수 있습니다.

## <a name="customize-the-details-page"></a>세부 정보 사용자 지정 페이지

`Pages/Students` 페이지로 이동합니다. **편집** , **세부 정보** 및 **삭제** 링크는 *Pages/Students/Index.cshtml* 파일에서 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)에 의해 생성됩니다.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

앱을 실행하고 **세부 정보** 링크를 선택합니다. URL은 `http://localhost:5000/Students/Details?id=2` 양식입니다. 학생 ID는 쿼리 문자열(`?id=2`)을 사용하여 전달됩니다.

`"{id:int}"` 경로 템플릿을 사용하도록 편집, 세부 정보 및 삭제 Razor Pages를 업데이트합니다. 이러한 각 페이지에 대한 page 지시문을 `@page`에서 `@page "{id:int}"`로 변경합니다.

정수 경로 값을 포함하지 **않는** “{id:int}” 경로 템플릿이 있는 페이지에 대한 요청은 HTTP 404(찾을 수 없음) 오류를 반환합니다. 예를 들어 `http://localhost:5000/Students/Details`는 404 오류를 반환합니다. ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.

 ```cshtml
@page "{id:int?}"
```

앱을 실행하고, 세부 정보 링크를 클릭하고, URL이 경로 데이터로 ID를 전달하는지 확인합니다(`http://localhost:5000/Students/Details/2`).

전체적으로 `@page`를 `@page "{id:int}"`로 변경하지 마십시오. 변경하면 홈 및 만들기 페이지에 대한 링크가 끊어집니다.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>관련 데이터 추가

학생 인덱스 페이지에 대한 스캐폴드된 코드는 `Enrollments` 속성을 포함하지 않습니다. 이 섹션에서 `Enrollments` 컬렉션의 콘텐츠는 세부 정보 페이지에 표시됩니다.

*Pages/Students/Details.cshtml.cs* 의 `OnGetAsync` 메서드는 `FirstOrDefaultAsync` 메서드를 사용하여 단일 `Student` 엔터티를 검색합니다. 다음 강조 표시된 코드를 추가합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다. 이러한 메서드는 읽기 관련 데이터 자습서에서 자세히 검토합니다.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다. `AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.

### <a name="display-related-enrollments-on-the-details-page"></a>세부 정보 페이지에 관련된 등록 표시

*Pages/Students/Details.cshtml* 을 엽니다. 등록의 목록을 표시하려면 다음 강조 표시된 코드를 추가합니다.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

코드를 붙여넣은 후 코드 들여쓰기가 잘못된 경우 CTRL-K-D를 눌러 수정합니다.

위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다. 각 등록의 경우 강좌 제목과 등급을 표시합니다. 강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.

앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다. 선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.

## <a name="update-the-create-page"></a>만들기 페이지 업데이트

다음 코드로 *Pages/Students/Create.cshtml.cs* 의 `OnPostAsync` 메서드를 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 코드를 검사합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

위의 코드에서 `TryUpdateModelAsync<Student>`는 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)의 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 속성에서 게시된 양식 값을 사용하여 `emptyStudent` 개체를 업데이트하려고 시도합니다. `TryUpdateModelAsync`는 나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

앞의 예제에서:

* 두 번째 인수(`"student", // Prefix`)는 값 조회에 사용하는 접두사입니다. 대/소문자를 구분하지 않습니다.
* 게시된 양식 값은 [모델 바인딩](xref:mvc/models/model-binding)을 사용한 `Student` 모델의 형식으로 변환됩니다.

<a id="overpost"></a>

### <a name="overposting"></a>초과 게시

게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다. 예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

앱에 만들기/업데이트 Razor 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다. 해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다. 원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.

해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 DB에서 업데이트됩니다. 다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다. 앱 디자이너는 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않았습니다.

<a name="vm"></a>

### <a name="view-model"></a>뷰 모델

뷰 모델은 일반적으로 애플리케이션에서 사용되는 모델에 포함된 속성의 하위 집합을 포함합니다. 애플리케이션 모델은 흔히 도메인 모델이라고 합니다. 도메인 모델은 일반적으로 DB의 해당 엔터티에 필요한 모든 속성을 포함합니다. 뷰 모델은 UI 계층에 필요한 속성만 포함합니다(예: 만들기 페이지). 뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 Razor Pages 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다. 다음 `Student` 뷰 모델을 살펴보세요.

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다. 뷰 모델은 확인(표시)하거나 업데이트하는 속성만 포함합니다.

다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다. `SetValues`는 속성 이름 일치를 사용합니다. 뷰 모델 형식은 모델 형식과 연결될 필요는 없으며 일치하는 속성만 있으면 됩니다.

`StudentVM`을 사용하려면 `Student` 대신 `StudentVM`을 사용하도록 [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml)을 업데이트해야 합니다.

Razor Pages에서 `PageModel` 파생 클래스는 뷰 모델입니다.

## <a name="update-the-edit-page"></a>편집 페이지 업데이트

편집 페이지를 위한 페이지 모델을 업데이트합니다. 주요 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.

* `OnPostAsync`에는 선택적 `id` 매개 변수가 있습니다.
* 현재 학생은 빈 학생을 만드는 대신 DB에서 페치됩니다.
* `FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다. `FindAsync`는 기본 키의 엔터티를 선택하는 경우 적합합니다. 자세한 내용은 [FindAsync](#FindAsync)를 참조하세요.

### <a name="test-the-edit-and-create-pages"></a>편집 및 만들기 페이지 테스트

몇 가지 학생 엔터티를 만들고 편집합니다.

## <a name="entity-states"></a>엔터티 상태

DB 컨텍스트는 메모리의 엔터티가 해당하는 DB의 행과 동기화하는지 여부를 추적합니다. DB 컨텍스트 동기화 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다. 예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다. `SaveChangesAsync`가 호출되면 DB 컨텍스트가 SQL INSERT 명령을 발급합니다.

엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.

* `Added`: 엔터티가 아직 DB에 없습니다. `SaveChanges` 메서드가 INSERT 문을 발급합니다.

* `Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다. 엔터티는 DB에서 읽을 때 이 상태입니다.

* `Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다. `SaveChanges` 메서드는 UPDATE 문을 발급합니다.

* `Deleted`: 엔터티가 삭제되도록 표시되었습니다. `SaveChanges` 메서드는 DELETE 문을 발급합니다.

* `Detached`: 엔터티가 DB 컨텍스트에서 추적되지 않습니다.

데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다. 엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다. `SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL UPDATE 문이 생성됩니다.

웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다. 페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다. 새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리를 시뮬레이트됩니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

이 섹션에서는 `SaveChanges`에 대한 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현하도록 코드가 추가됩니다. 가능한 오류 메시지를 포함하는 문자열을 추가합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

`OnGetAsync` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

위의 코드는 선택적 매개 변수 `saveChangesError`를 포함합니다. `saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다. 일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다. 일시적인 네트워크 오류는 클라우드에서 발생 가능성이 더 큽니다. `saveChangesError`는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 false입니다. `OnGetAsync`가 `OnPostAsync`에 의해 호출되면(삭제 작업이 실패했으므로) `saveChangesError` 매개 변수는 true입니다.

### <a name="the-delete-pages-onpostasync-method"></a>삭제 페이지 OnPostAsync 메서드

`OnPostAsync`를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

앞의 코드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다. `SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다. `Remove`가 실패하는 경우:

* DB 예외가 catch되었습니다.
* 삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.

### <a name="update-the-delete-no-locrazor-page"></a>삭제 Razor 페이지 업데이트

다음 강조 표시된 오류 메시지를 삭제 Razor 페이지에 추가합니다.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

삭제를 테스트합니다.

## <a name="common-errors"></a>일반적인 오류

Students/Index 또는 다른 링크가 작동하지 않습니다.

Razor 페이지에 올바른 `@page` 지시문이 포함되어 있는지 확인합니다. 예를 들어 Students/Index Razor 페이지는 경로 템플릿을 포함해서는 **안 됩니다**.

```cshtml
@page "{id:int}"
```

각 Razor 페이지는 `@page` 지시문을 포함해야 합니다.



## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [이전](xref:data/ef-rp/intro)
> [다음](xref:data/ef-rp/sort-filter-page)

::: moniker-end
