---
title: '자습서: 상속 구현 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 ASP.NET Core 애플리케이션에서 Entity Framework Core를 사용하여 데이터 모델에서 상속을 구현하는 방법을 보여 줍니다.
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 299e00b223d287c4e2ece3d1e250581e2a7565e5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586373"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>자습서: 상속 구현 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서는 동시성 예외를 처리했습니다. 이 자습서에서는 데이터 모델에서 상속을 구현하는 방법을 보여 줍니다.

개체 지향 프로그래밍에서는 쉽게 코드를 재사용하기 위해 상속을 사용할 수 있습니다. 이 자습서에서는 강사와 학생 모두에게 공통적인 속성(예: `LastName`)이 포함된 `Person` 기본 클래스에서 클래스가 파생되도록 `Instructor` 및 `Student` 클래스를 변경합니다. 웹 페이지를 추가하거나 변경하지는 않지만 일부 코드를 변경하고 이러한 변경 내용이 데이터베이스에 자동으로 반영됩니다.

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 데이터베이스에 상속 매핑
> * Person 클래스 만들기
> * 강사 및 학생 업데이트
> * 모델에 개인 추가
> * 마이그레이션 만들기 및 업데이트
> * 구현 테스트

## <a name="prerequisites"></a>사전 요구 사항

* [동시성 처리](concurrency.md)

## <a name="map-inheritance-to-database"></a>데이터베이스에 상속 매핑

School 데이터 모델의 `Instructor` 및 `Student` 클래스는 동일한 여러 속성을 가지고 있습니다.

![Student 및 Instructor 클래스](inheritance/_static/no-inheritance.png)

`Instructor` 및 `Student` 엔터티에서 공유하는 속성에 대해 중복 코드를 제거하려고 한다고 가정해 보겠습니다. 또는 강사 또는 학생의 이름인지 여부를 신경쓰지 않고 이름의 형식을 지정할 수 있는 서비스를 작성하려고 합니다. 공유 속성만 포함하는 `Person` 기본 클래스를 만든 후 다음 그림과 같이 해당 기본 클래스에서 `Instructor` 및 `Student` 클래스를 상속할 수 있습니다.

![Person 클래스에서 파생된 Student 및 Instructor 클래스](inheritance/_static/inheritance.png)

데이터베이스에 이 상속 구조를 여러 가지 방법으로 나타낼 수 있습니다. 학생과 강사에 관한 정보를 하나의 테이블에 포함하는 Person 테이블을 포함할 수 있습니다. 일부 열은 강사(HireDate)에게만, 일부는 학생(EnrollmentDate)에게만, 일부는 둘 다(LastName, FirstName)에 적용할 수 있습니다. 일반적으로 각 행의 형식을 나타내는 판별자 열이 있습니다. 예를 들어, 판별자 열은 강사에 대해 "Instructor"를, 학생에 대해 "Student"를 포함할 수 있습니다.

![계층당 하나의 테이블 예제](inheritance/_static/tph.png)

단일 데이터베이스 테이블에서 엔터티 상속 구조를 생성하는 이 패턴을 TPH(계층당 하나의 테이블) 상속이라고 합니다.

다른 방법은 데이터베이스를 상속 구조와 유사하게 만드는 것입니다. 예를 들어 Person 테이블에 이름 필드만 있고 날짜 필드가 있는 별도의 Instructor 및 Student 테이블을 포함할 수 있습니다.

> [!WARNING]
> TPT(형식당 테이블)는 EF Core 3.x에서 지원되지 않지만 [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan)에서 구현되었습니다.

![형식당 하나의 테이블 상속](inheritance/_static/tpt.png)

각 엔터티 클래스에 대한 데이터베이스 테이블을 만드는 이 패턴을 TPT(형식당 하나의 테이블) 상속이라고 합니다.

또 다른 옵션은 모든 비추상 형식을 개별 테이블에 매핑하는 것입니다. 상속된 속성을 포함한 모든 클래스 속성을 해당 테이블의 열에 매핑합니다. 이 패턴을 TPC(구체적 클래스당 하나의 테이블) 상속이라고 합니다. 앞에서 표시된 것처럼 Person, Student 및 Instructor 클래스에 대한 TPC 상속을 구현한 경우, Student 및 Instructor 테이블은 상속을 구현한 후에도 이전과 다르지 않습니다.

TPT 패턴이 복잡한 조인 쿼리를 초래할 수 있기 때문에 TPC 및 TPH 상속 패턴은 일반적으로 TPT 상속 패턴보다 우수한 성능을 제공합니다.

이 자습서에서는 TPH 상속을 구현하는 방법을 보여 줍니다. TPH는 Entity Framework Core에서 지원하는 유일한 상속 패턴입니다.  사용자는 `Person` 클래스를 만들고, `Person`에서 파생되도록 `Instructor` 및 `Student` 클래스를 변경하며 `DbContext`에 새 클래스를 추가하고, 마이그레이션을 생성합니다.

> [!TIP]
> 다음 내용을 변경하기 전에 프로젝트 사본을 저장하는 것이 좋습니다.  그러면 문제가 발생하여 처음부터 다시 시작해야 하는 경우, 이 자습서의 단계를 역순으로 하거나 전체 시리즈의 처음으로 돌아가는 대신 저장된 프로젝트에서 쉽게 시작할 수 있습니다.

## <a name="create-the-person-class"></a>Person 클래스 만들기

Models 폴더에서 Person.cs 만들고 템플릿 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>강사 및 학생 업데이트

*Instructor.cs* 에서 Person 클래스에서 Instructor 클래스를 파생시키고 키 및 이름 필드를 제거합니다. 해당 코드는 다음 예제와 같이 나타납니다.

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

*Student.cs* 에서 동일하게 변경합니다.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>모델에 개인 추가

Person 엔터티 형식을 *SchoolContext.cs* 에 추가합니다. 새 줄이 강조 표시됩니다.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

계층당 하나의 테이블 상속을 구성하기 위해 Entity Framework에 필요한 모든 작업입니다. 보다시피, 데이터베이스가 업데이트되면 Student 테이블과 Instructor 테이블 대신 Person 테이블이 생깁니다.

## <a name="create-and-update-migrations"></a>마이그레이션 만들기 및 업데이트

변경 내용을 저장하고 프로젝트를 빌드합니다. 그런 다음, 프로젝트 폴더에서 명령 창을 열고 다음 명령을 입력합니다.

```dotnetcli
dotnet ef migrations add Inheritance
```

`database update` 명령을 아직 실행하지 마세요. 이 명령은 Instructor 테이블을 삭제하고 Student 테이블 이름을 Person으로 변경하므로 데이터가 손실됩니다. 기존 데이터를 유지하려면 사용자 지정 코드를 제공해야 합니다.

*Migrations/\<timestamp>_Inheritance.cs* 를 열고 `Up` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

이 코드는 다음과 같은 데이터베이스 업데이트 작업을 처리합니다.

* Student 테이블을 가리키는 외래 키 제약 조건 및 인덱스를 제거합니다.

* Instructor 테이블 이름을 Person으로 바꾸고 Student 데이터를 저장하기 위해 필요한 변경을 수행합니다.

* 학생에 대한 Nullable EnrollmentDate를 추가합니다.

* 행이 학생용인지, 강사용인지 나타내는 판별자 열을 추가합니다.

* 학생 행은 고용 날짜를 포함하지 않으므로 HireDate를 Nullable로 설정합니다.

* 학생을 가리키는 외래 키를 업데이트하는 데 사용할 임시 필드를 추가합니다. 학생을 Person 테이블에 복사하면 새로운 기본 키 값이 생깁니다.

* Student 테이블에서 Person 테이블로 데이터를 복사합니다. 그러면 학생에게 새 기본 키 값이 할당됩니다.

* 학생을 가리키는 외래 키 값을 수정합니다.

* 이제 Person 테이블을 가리키도록 외래 키 제약 조건 및 인덱스를 다시 만듭니다.

(기본 키 형식으로 정수 대신 GUID를 사용한 경우, 학생 기본 키 값을 변경할 필요가 없으며 이 단계 중 일부가 생략되었을 수 있습니다.)

`database update` 명령을 실행합니다.

```dotnetcli
dotnet ef database update
```

(프로덕션 시스템에서는 이전 데이터베이스 버전으로 돌아가기 위해 사용해야 할 경우를 대비해서 `Down` 메서드에 해당 변경 내용을 적용합니다. 이 자습서에서는 `Down` 메서드를 사용하지 않습니다.)

> [!NOTE]
> 기존 데이터가 있는 데이터베이스에서 스키마를 변경할 때 다른 오류가 발생할 수 있습니다. 해결할 수 없는 마이그레이션 오류가 발생하면 연결 문자열에서 데이터베이스 이름을 변경하거나 데이터베이스를 삭제할 수 있습니다. 새 데이터베이스에는 마이그레이션할 데이터가 없으므로 update-database 명령은 오류없이 완료될 가능성이 큽니다. 데이터베이스를 삭제하려면 SSOX를 사용하거나 `database drop` CLI 명령을 실행합니다.

## <a name="test-the-implementation"></a>구현 테스트

앱을 실행하고 다양한 페이지를 시도해 봅니다. 모든 항목이 이전과 같이 작동합니다.

**SQL Server 개체 탐색기** 에서 **데이터 연결/SchoolContext**, **테이블** 을 차례로 확장하면 Student 및 Instructor 테이블이 Person 테이블로 바뀐 것을 확인할 수 있습니다. Person 테이블 디자이너를 열면 Student 및 Instructor 테이블에 있던 모든 열이 나타나는 것을 알 수 있습니다.

![SSOX에서 Person 테이블](inheritance/_static/ssox-person-table.png)

Person 테이블을 마우스 오른쪽 단추로 클릭한 후 **테이블 데이터 표시** 를 클릭하여 판별자 열을 표시합니다.

![SSOX에서 Person 테이블 - 테이블 데이터](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>추가 자료

Entity Framework Core의 상속에 대한 자세한 내용은 [상속](/ef/core/modeling/inheritance)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 데이터베이스에 상속 매핑
> * Person 클래스 만들기
> * 강사 및 학생 업데이트
> * 모델에 개인 추가
> * 마이그레이션 만들기 및 업데이트
> * 구현 테스트

다양한 고급 Entity Framework 시나리오를 처리하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [다음: 고급 항목](advanced.md)