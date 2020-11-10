---
title: '자습서: 고급 시나리오에 대해 알아보기 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 Entity Framework Core를 사용하는 ASP.NET Core 웹앱 개발의 기본 개념을 넘어 유용한 토픽을 소개합니다.
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054387"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>자습서: 고급 시나리오에 대해 알아보기 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서는 계층당 하나의 테이블 상속을 구현했습니다. 이 자습서에서는 Entity Framework Core를 사용하는 ASP.NET Core 웹 애플리케이션을 개발하는 기본 개념을 넘어 알아 두면 유용한 여러 가지 항목을 소개합니다.

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 원시 SQL 쿼리 수행
> * 엔터티를 반환하는 쿼리 호출
> * 기타 형식을 반환하는 쿼리 호출
> * 업데이트 쿼리 호출
> * SQL 쿼리 검사
> * 추상화 계층 만들기
> * 자동 변경 검색에 대해 알아보기
> * EF Core 소스 코드 및 개발 계획에 대해 알아보기
> * 동적 LINQ를 사용하여 코드를 단순화하는 방법 알아보기

## <a name="prerequisites"></a>사전 요구 사항

* [상속 구현](inheritance.md)

## <a name="perform-raw-sql-queries"></a>원시 SQL 쿼리 수행

Entity Framework를 사용할 때 장점 중 하나는 코드가 데이터를 저장하는 특정 메서드에 너무 얽매이지 않아도 된다는 점입니다. 이것은 사용자를 위한 SQL 쿼리와 명령이 생성되므로 가능하며 사용자는 코드를 직접 작성할 필요가 없습니다. 하지만 사용자가 직접 만든 특정 SQL 쿼리를 실행해야 할 때는 예외적인 시나리오입니다. 이러한 시나리오에서는 Entity Framework Code First API에 SQL 명령을 데이터베이스에 직접 전달할 수 있는 메서드가 포함됩니다. EF Core 1.0에서 다음과 같은 옵션을 선택할 수 있습니다.

* 엔터티 형식을 반환하는 쿼리에 `DbSet.FromSql` 메서드를 사용합니다. 반환된 개체는 `DbSet` 개체에 예상되는 형식이어야 하며 [추적을 해제](crud.md#no-tracking-queries)하지 않는 한 데이터베이스 컨텍스트에 의해 자동으로 추적됩니다.

* 쿼리가 아닌 명령에는 `Database.ExecuteSqlCommand`를 사용합니다.

엔터티가 아닌 형식을 반환하는 쿼리를 실행해야 하는 경우 EF에서 제공하는 데이터베이스 연결로 ADO.NET을 사용할 수 있습니다. 이 메서드를 사용하여 엔터티 형식을 검색하더라도 반환된 데이터는 데이터베이스 컨텍스트에 의해 추적되지 않습니다.

웹 애플리케이션에서 SQL 명령을 실행할 때 항상 그렇듯이 SQL 삽입 공격으로부터 사이트를 보호하기 위한 예방 조치를 취해야 합니다. 이를 수행하는 한 가지 방법은 매개 변수가 있는 쿼리를 사용하여 웹 페이지에서 제출한 문자열을 SQL 명령으로 해석할 수 없도록 하는 것입니다. 이 자습서에서는 사용자 입력을 쿼리에 통합할 때 매개 변수가 있는 쿼리를 사용합니다.

## <a name="call-a-query-to-return-entities"></a>엔터티를 반환하는 쿼리 호출

`DbSet<TEntity>` 클래스는 `TEntity` 형식의 엔터티를 반환하는 쿼리를 실행하는 데 사용할 수 있는 메서드를 제공합니다. 어떻게 작동하는지 보기 위해 Department 컨트롤러의 `Details` 메서드에서 코드를 변경합니다.

다음 강조 표시된 코드에서처럼 *DepartmentsController.cs* 의 `Details` 메서드에서 `FromSql` 메서드 호출로 부서를 검색하는 코드를 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

새 코드가 올바르게 작동하는지 확인하려면 **부서** 탭을 선택한 후 부서 중 하나에 대해 **세부 정보** 를 선택합니다.

![부서 세부 정보](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>기타 형식을 반환하는 쿼리 호출

이전에 등록 날짜별로 학생 수를 보여주는 [정보] 페이지의 학생 통계 표를 만들었습니다. 학생 엔터티 집합(`_context.Students`)에서 데이터를 가져와 LINQ를 사용하여 결과를 `EnrollmentDateGroup` 뷰 모델 개체 목록에 프로젝션했습니다. LINQ를 사용하지 않고 SQL을 직접 작성한다고 가정해 보겠습니다. 이를 수행하려면 엔터티 개체가 아닌 다른 것을 리턴하는 SQL 쿼리를 실행해야 합니다. EF Core 1.0에서 이를 수행하는 한 가지 방법은 ADO.NET 코드를 작성하고 EF에서 데이터베이스 연결을 얻는 것입니다.

*HomeController.cs* 에서 `About` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

using 문을 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

앱을 실행하고 [정보] 페이지로 이동합니다. 이전과 동일한 데이터가 표시됩니다.

![정보 페이지](advanced/_static/about.png)

## <a name="call-an-update-query"></a>업데이트 쿼리 호출

Contoso University 관리자가 모든 과정의 학점 수를 변경하는 등 데이터베이스의 전체적인 변경을 수행하려고 한다고 가정해 보겠습니다. 대학에 과목이 많은 경우 엔터티로 모두 검색하여 개별적으로 변경하는 것은 비효율적입니다. 이 섹션에서는 사용자가 모든 과정에 대한 학점 수를 변경하는 요소를 지정할 수 있는 웹 페이지를 구현하고 SQL UPDATE 문을 실행하여 학점 수를 변경합니다. 그러면 웹 페이지가 다음 그림과 같이 표시됩니다.

![[Course Credits(과정 학점)] 페이지 업데이트](advanced/_static/update-credits.png)

*CoursesController.cs* 에서 HttpGet 및 HttpPost의 UpdateCourseCredits 메서드를 추가합니다.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

컨트롤러가 HttpGet 요청을 처리할 때 `ViewData["RowsAffected"]`에는 아무 것도 반환되지 않으며 앞의 그림과 같이 뷰에 빈 텍스트 상자와 제출 단추가 표시됩니다.

**업데이트** 단추를 클릭하면 HttpPost 메서드가 호출되고 승수는 텍스트 상자에 입력된 값을 포함합니다. 그런 다음, 코드는 과정을 업데이트하는 SQL을 실행하고 영향을 받은 행 수를 `ViewData`의 뷰에 반환합니다. 뷰에 `RowsAffected` 값이 있으면 업데이트된 행 수가 표시됩니다.

**솔루션 탐색기** 에서 *Views/Courses* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 항목** 을 클릭합니다.

**새 항목 추가** 대화 상자에서 왼쪽 창의 **설치됨** 아래에서 **ASP.NET** 을 클릭하고, **Razor 뷰** 를 클릭한 후, 새 뷰의 이름을 *UpdateCourseCredits.cshtml* 로 지정합니다.

*Views/Courses/UpdateCourseCredits.cshtml* 에서 템플릿 코드를 다음 코드로 바꿉니다.

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

**Courses(과정)** 탭을 선택하여 `UpdateCourseCredits` 메서드를 실행한 후 브라우저의 주소 표시줄에서 URL 끝에 "/UpdateCourseCredits"를 추가합니다(예: `http://localhost:5813/Courses/UpdateCourseCredits`). 텍스트 상자에 숫자를 입력합니다.

![[Course Credits(과정 학점)] 페이지 업데이트](advanced/_static/update-credits.png)

**업데이트** 를 클릭합니다. 영향을 받은 행 수가 표시됩니다.

![영향을 받는 [Course Credits(과정 학점)] 페이지 행 업데이트](advanced/_static/update-credits-rows-affected.png)

학점 수가 수정된 과정 목록을 보려면 **목록으로 돌아가기** 를 클릭합니다.

프로덕션 코드는 항상 업데이트로 인해 항상 유효한 데이터가 생기도록 보장해 줍니다. 여기에 표시된 간소화된 코드에서는 5보다 큰 숫자가 생길 만큼 충분한 학점 수를 곱할 수 있습니다. (`Credits` 속성에는 `[Range(0, 5)]` 특성이 포함됩니다.) 업데이트 쿼리가 작동하지만 잘못된 데이터로 인해 학점이 5 이하라고 가정하는 시스템의 다른 부분에 예기치 않은 결과가 발생할 수 있습니다.

원시 SQL 쿼리에 대한 자세한 내용은 [원시 SQL 쿼리](/ef/core/querying/raw-sql)를 참조하세요.

## <a name="examine-sql-queries"></a>SQL 쿼리 검사

때로는 데이터베이스로 전송된 실제 SQL 쿼리를 볼 수 있는 것이 도움이 됩니다. ASP.NET Core의 기본 로깅 기능은 EF Core에서 쿼리 및 업데이트용 SQL이 포함된 로그를 작성하기 위해 EF Core에 의해 자동으로 사용됩니다. 이 섹션에서는 SQL 로깅의 몇 가지 예를 살펴봅니다.

*StudentsController.cs* 를 열고 `Details` 메서드에서 `if (student == null)` 문에 중단점을 설정합니다.

디버그 모드에서 앱을 실행하고 학생에 대한 [세부 정보] 페이지로 이동합니다.

디버그 출력을 보여 주는 **출력** 창으로 이동하면 쿼리를 확인할 수 있습니다.

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

여기서 놀라운 사실을 알 수 있는데, SQL이 Person 테이블에서 최대 2개 행을 선택한다는 것입니다(`TOP(2)`). `SingleOrDefaultAsync` 메서드는 서버의 1개 행으로 확인되지 않습니다. 이유는 다음과 같습니다.

* 쿼리에서 여러 행을 반환하는 경우 메서드는 Null을 반환합니다.
* 쿼리에서 여러 행을 반환하는지 여부를 확인하려면 EF에서 2 이상을 반환하는지 확인해야 합니다

디버그 모드를 사용하지 않고 **출력** 창에서 로깅 출력을 얻기 위해 중단점에서 중단할 필요가 없습니다. 출력을 확인하려는 지점에서 로깅을 중지하는 편리한 방법일 뿐입니다. 그렇게 하지 않으면 로깅이 계속되고 관심있는 부분을 찾기 위해 뒤로 스크롤해야 합니다.

## <a name="create-an-abstraction-layer"></a>추상화 계층 만들기

대부분의 개발자는 리포지토리 및 작업 패턴 단위를 구현하기 위한 코드를 Entity Framework에서 작동하는 코드를 둘러싼 래퍼로 작성합니다. 이러한 패턴은 애플리케이션의 데이터 액세스 계층 및 비즈니스 논리 계층 간에 추상화 계층을 만드는 데 사용됩니다. 이러한 패턴을 구현하면 데이터 저장소의 변경 내용으로부터 애플리케이션을 격리할 수 있으며 자동화된 단위 테스트 또는 TDD(테스트 중심 개발)를 용이하게 수행할 수 있습니다. 그러나 EF를 사용하는 애플리케이션에 대해 이러한 패턴을 구현하는 추가 코드를 작성하는 것이 항상 좋은 것만은 아닙니다. 다음과 같은 몇 가지 이유 때문입니다.

* EF 컨텍스트 클래스 자체에서 데이터 저장소별 코드로부터 사용자 코드를 격리합니다.

* EF 컨텍스트 클래스는 EF를 사용하여 수행하는 데이터베이스 업데이트에 대해 작업 단위 클래스로 사용될 수 있습니다.

* EF에는 리포지토리 코드를 작성하지 않고도 TDD를 구현하는 기능이 포함되어 있습니다.

리포지토리 및 작업 패턴 단위를 구현하는 방법에 대한 자세한 내용은 [이 자습서 시리즈의 Entity Framework 5 버전](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)을 참조하세요.

Entity Framework Core는 테스트에 사용할 수 있는 메모리 내 데이터베이스 공급자를 구현합니다. 자세한 내용은 [InMemory를 사용한 테스트](/ef/core/miscellaneous/testing/in-memory)를 참조하세요.

## <a name="automatic-change-detection"></a>자동 변경 내용 검색

Entity Framework는 엔터티의 현재 값을 원래 값과 비교하여 엔터티가 변경된 방법(즉, 데이터베이스에 전송해야 하는 업데이트)을 결정합니다. 원래 값은 엔터티가 쿼리 또는 연결될 때 저장됩니다. 자동 변경 내용 검색을 발생시키는 일부 메서드는 다음과 같습니다.

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

대량의 엔터티를 추적하고 루프에서 이러한 메서드 중 하나를 여러 번 호출하는 경우 `ChangeTracker.AutoDetectChangesEnabled` 속성을 사용하여 자동 변경 내용 검색을 일시적으로 해제하면 성능이 크게 향상될 수 있습니다. 예를 들어:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core 소스 코드 및 개발 계획

Entity Framework Core 소스는 [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)에 있습니다. EF Core 리포지토리에는 야간 빌드, 문제 추적, 기능 사양, 디자인 회의 노트 및 [향후 개발을 위한 로드맵](https://github.com/dotnet/efcore/wiki/Roadmap)이 포함됩니다. 버그를 보고하거나 찾고 참가할 수 있습니다.

소스 코드가 오픈 소스이기는 하지만 Entity Framework Core는 완전히 Microsoft 제품으로 지원됩니다. Microsoft Entity Framework 팀이 어떤 참가자를 수락할지 관리하고 각 릴리스의 품질을 보장하기 위해 모든 코드 변경 내용을 테스트합니다.

## <a name="reverse-engineer-from-existing-database"></a>기존 데이터베이스에서 리버스 엔지니어링

기존 데이터베이스에서 엔터티 클래스를 포함한 데이터 모델을 리버스 엔지니어링하려면 [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) 명령을 사용합니다. [시작 자습서](/ef/core/get-started/aspnetcore/existing-db)를 참조하세요.

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>동적 LINQ를 사용하여 코드 단순화

[이 시리즈의 세 번째 자습서](sort-filter-page.md)에서는 `switch` 문에 열 이름을 하드 코딩하여 LINQ 코드를 작성하는 방법을 보여 줍니다. 선택할 수 있는 열이 두 개인 경우 잘 작동하지만 열 수가 많은 경우 코드가 길어질 수 있습니다. 이러한 문제를 해결하기 위해 `EF.Property` 메서드를 사용하여 속성 이름을 문자열로 지정할 수 있습니다. 이 방법을 사용해 보려면 `StudentsController`에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>감사의 글

Tom Dykstra 및 Rick Anderson(twitter @RickAndMSFT)이 본 자습서를 작성했습니다. Rowan Miller, Diego Vega 및 기타 Entity Framework 팀원은 코드를 검토해 주었고 자습서에 사용할 코드를 작성하는 동안 발생하는 디버그 문제를 도와주었습니다. John Parente 및 Paul Goldman은 ASP.NET Core 2.2 자습서를 업데이트하는 작업을 수행했습니다.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>일반적인 오류 문제 해결

### <a name="contosouniversitydll-used-by-another-process"></a>다른 프로세스에 ContosoUniversity.dll 사용됨

오류 메시지:

> '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll'을 쓰기용으로 열 수 없습니다 -- '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' 파일은 다른 프로세스에서 사용 중이므로 프로세스에서 액세스할 수 없습니다.

해결책:

IIS Express에서 사이트를 중지합니다. Windows 시스템 트레이로 이동하고 IIS Express를 찾아 해당 아이콘을 마우스 오른쪽 단추로 클릭하고 Contoso University 사이트를 선택한 다음, **사이트 중지** 를 클릭합니다.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Up 및 Down 메서드에서 코드 없이 스캐폴드된 마이그레이션

가능한 원인:

EF CLI 명령은 코드 파일을 자동으로 닫고 저장하지 않습니다. `migrations add` 명령을 실행할 때 저장되지 않은 변경 내용이 있는 경우 EF가 변경 내용을 찾을 수 없습니다.

해결책:

`migrations remove` 명령을 실행하고 코드 변경 내용을 저장한 후 `migrations add` 명령을 다시 실행합니다.

### <a name="errors-while-running-database-update"></a>데이터베이스 업데이트를 실행하는 동안 오류 발생

기존 데이터가 있는 데이터베이스에서 스키마를 변경할 때 다른 오류가 발생할 수 있습니다. 해결할 수 없는 마이그레이션 오류가 발생하면 연결 문자열에서 데이터베이스 이름을 변경하거나 데이터베이스를 삭제할 수 있습니다. 새 데이터베이스에는 마이그레이션할 데이터가 없으므로 update-database 명령은 오류없이 완료될 가능성이 훨씬 큽니다.

가장 간단한 방법은 *appsettings.json* 에서 데이터베이스 이름을 바꾸는 것입니다. 다음에 `database update`를 실행할 때 새 데이터베이스가 생성됩니다.

SSOX에서 데이터베이스를 삭제하려면 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 클릭한 후 **데이터베이스 삭제** 대화 상자에서 **기존 연결 닫기** 를 선택하고 **확인** 을 클릭합니다.

CLI를 사용하여 데이터베이스를 삭제하려면 `database drop` CLI 명령을 실행합니다.

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>SQL Server 인스턴스 찾기 오류

오류 메시지:

> SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다. (공급자: SQL 네트워크 인터페이스, 오류: 26 - 지정된 서버/인스턴스 찾기 오류)

해결책:

연결 문자열을 확인합니다. 데이터베이스 파일을 수동으로 삭제한 경우 새 데이터베이스로 시작하도록 생성 문자열에서 데이터베이스 이름을 변경합니다.

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>추가 자료

EF Core에 대한 자세한 내용은 [Entity Framework Core 설명서](/ef/core)를 참조하세요. 책에도 사용할 수 있습니다. [작업 중인 Entity Framework Core](https://www.manning.com/books/entity-framework-core-in-action).

웹앱을 배포하는 방법에 대한 정보는 <xref:host-and-deploy/index>를 참조하세요.

인증 및 권한 부여와 같은 ASP.NET Core MVC와 관련된 다른 항목에 대한 정보는 <xref:index>를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 원시 SQL 쿼리 수행
> * 엔터티를 반환하는 쿼리 호출
> * 기타 형식을 반환하는 쿼리 호출
> * 업데이트 쿼리 호출
> * SQL 쿼리 검사
> * 추상화 계층 만들기
> * 자동 변경 검색에 대해 알아보기
> * EF Core 소스 코드 및 개발 계획에 대해 알아보기
> * 동적 LINQ를 사용하여 코드를 단순화하는 방법 알아보기

이것으로 ASP.NET Core MVC 애플리케이션에서 Entity Framework Core 사용에 대한 자습서 시리즈를 마칩니다. 이 시리즈는 새 데이터베이스로 작업했습니다. 대안은 기존 데이터베이스에서 모델을 리버스 엔지니어링하는 것입니다.

> [!div class="nextstepaction"]
> [자습서: MVC가 있는 EF Core, 기존 데이터베이스](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
