---
title: 5부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 데이터 모델
author: rick-anderson
description: Razor Pages 및 Entity Framework 자습서 시리즈의 5부입니다.
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1ac9d6303daac82f3973c5d027fe1f453dc32e02
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054101"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a>5부. ASP.NET Core에서 EF Core를 사용한 Razor Pages - 데이터 모델

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

이전 자습서에서는 세 가지 엔터티로 구성된 기본 데이터 모델을 사용했습니다. 이 자습서에서:

* 더 많은 엔터티 및 관계가 추가됩니다.
* 데이터 모델은 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 사용자 지정됩니다.

다음 그림에서는 완료된 데이터 모델을 보여 줍니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티](complex-data-model/_static/student-entity.png)

*Models/Student.cs* 에 있는 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

앞의 코드는 `FullName` 속성을 추가하고 기존 속성에 다음 특성을 추가합니다.

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>FullName 계산된 속성

`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다. `FullName`은 설정될 수 없으므로 get 접근자만 포함합니다. 데이터베이스에서 `FullName` 열이 생성되지 않습니다.

### <a name="the-datatype-attribute"></a>DataType 특성

```csharp
[DataType(DataType.Date)]
```

학생 등록 날짜의 경우 해당 날짜만 관련이 있지만 모든 페이지에는 현재 해당 날짜와 함께 하루의 시간이 표시됩니다. 데이터 주석 특성을 사용하면 데이터를 표시하는 모든 페이지에서 표시 형식을 해결하는 하나의 코드 변경을 만들 수 있습니다. 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정합니다. 이 경우 날짜 및 시간이 아닌 날짜만 표시되어야 합니다. [DataType 열거형](/dotnet/api/system.componentmodel.dataannotations.datatype)은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 앱에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다. 예를 들어:

* `mailto:` 링크는 `DataType.EmailAddress`에 대해 자동으로 만들어집니다.
* 날짜 선택기는 대부분의 브라우저에서 `DataType.Date`에 대해 제공됩니다.

`DataType` 특성은 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다. `DataType` 특성은 유효성 검사를 제공하지 않습니다.

### <a name="the-displayformat-attribute"></a>DisplayFormat 특성

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다. 기본적으로 날짜 필드는 서버의 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)를 기본으로 하는 기본 형식에 따라 표시됩니다.

`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다. `ApplyFormatInEditMode` 설정은 서식 지정이 편집 UI에도 적용되어야 함을 지정합니다. 일부 필드는 `ApplyFormatInEditMode`를 사용하면 안 됩니다. 예를 들어 통화 기호는 일반적으로 편집 텍스트 상자에 표시되면 안 됩니다.

`DisplayFormat` 특성은 단독으로 사용될 수 있습니다. 일반적으로 `DisplayFormat` 특성과 함께 `DataType` 특성을 사용하는 것이 좋습니다. `DataType` 특성은 화면에서 렌더링하는 방법과 대조적으로 데이터의 의미 체계를 전달합니다. `DataType` 특성은 `DisplayFormat`에서 사용할 수 없는 다음과 같은 이점을 제공합니다.

* 브라우저는 HTML5 기능을 활성화할 수 있습니다. 예를 들어 달력 컨트롤, 로캘에 적합한 통화 기호, 메일 링크, 클라이언트 쪽 입력 유효성 검사를 표시합니다.
* 기본적으로 브라우저는 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.

자세한 내용은 [\<input> 태그 도우미 설명서](xref:mvc/views/working-with-forms#the-input-tag-helper)를 참조하세요.

### <a name="the-stringlength-attribute"></a>StringLength 특성

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지는 특성으로 지정될 수 있습니다. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 특성은 데이터 필드에서 허용되는 최소 및 최대 문자 길이를 지정합니다. 표시된 코드는 이름을 최대 50자로 제한합니다. 최소 문자열 길이를 설정하는 예제는 [뒷부분](#the-required-attribute)에 나와 있습니다.

`StringLength` 특성은 또한 클라이언트 쪽 및 서버 쪽 유효성 검사를 제공합니다. 최소값은 데이터베이스 스키마에 영향을 주지 않습니다.

`StringLength` 특성은 이름에 공백을 입력할 수 있습니다. [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 특성은 입력에 제한을 적용하는 데 사용될 수 있습니다. 예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 알파벳순이어야 합니다.

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**SQL Server 개체 탐색기** (SSOX)에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.

![마이그레이션 전 SSOX의 학생 테이블](complex-data-model/_static/ssox-before-migration.png)

위의 이미지는 `Student` 테이블에 대한 스키마를 보여 줍니다. 이름 필드의 형식은 `nvarchar(MAX)`입니다. 이 자습서의 뒷부분에서 마이그레이션을 만들고 적용하면 문자열 길이 특성의 결과로 이름 필드가 `nvarchar(50)`이 됩니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite 도구에서 `Student` 테이블에 대한 열 정의를 검토합니다. 이름 필드의 형식은 `Text`입니다. 이름 필드를 `FirstMidName`이라고 하는 것을 확인할 수 있습니다. 다음 섹션에서는 해당 열의 이름을 `FirstName`으로 변경합니다.

---

### <a name="the-column-attribute"></a>열 특성

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

특성은 클래스 및 속성이 데이터베이스에 매핑되는 방법을 제어할 수 있습니다. `Student` 모델에서 `Column` 특성은 데이터베이스에서 `FirstMidName` 속성의 이름을 “FirstName”에 매핑하는 데 사용됩니다.

데이터베이스가 만들어질 때 모델의 속성 이름은 열 이름에 사용됩니다(`Column` 특성이 사용되는 경우 제외). 필드에 중간 이름도 포함될 수 있으므로 `Student` 모델은 첫 번째 이름 필드에 대해 `FirstMidName`을 사용합니다.

`[Column]` 특성을 사용하면 데이터 모델의 `Student.FirstMidName`은 `Student` 테이블의 `FirstName` 열에 매핑됩니다. `Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경됩니다. `SchoolContext`를 지원하는 모델은 데이터베이스와 더 이상 일치하지 않습니다. 해당 불일치는 이 자습서의 뒷부분에서 마이그레이션을 추가하여 해결할 수 있습니다.

### <a name="the-required-attribute"></a>필수 특성

```csharp
[Required]
```

`Required` 특성에서 이름 속성은 필수 필드입니다. `Required` 특성은 값 형식(예: `DateTime`, `int` 및 `double`)과 같은 비 nullable 형식에 필요하지 않습니다. Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.

`MinimumLength`가 적용되려면 `Required` 특성이 `MinimumLength`와 함께 사용되어야 합니다.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength`와 `Required`는 유효성 검사 충족에 공백을 허용합니다. 문자열을 완전히 제어할 수 있도록 `RegularExpression` 특성을 사용합니다.

### <a name="the-display-attribute"></a>표시 특성

```csharp
[Display(Name = "Last Name")]
```

`Display` 특성은 텍스트 상자에 대한 캡션이 "First Name", "Last Name", "Full Name" 및 "Enrollment Date"여야 함을 지정합니다. 기본 캡션에는 단어를 분할하는 공백이 없습니다(예: "Lastname)".

### <a name="create-a-migration"></a>마이그레이션 만들기

앱을 실행하고 [학생] 페이지로 이동합니다. 예외가 throw됩니다. `[Column]` 특성을 사용하면 EF가 `FirstName` 열을 찾아야 하지만 데이터베이스의 열 이름은 `FirstMidName`입니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

다음 예제와 유사한 오류 메시지가 표시됩니다.

```
SqlException: Invalid column name 'FirstName'.
```

* PMC에서 다음 명령을 입력하여 새 마이그레이션을 만들고 데이터베이스를 업데이트합니다.

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  이 명령 중 첫 번째는 다음 경고 메시지를 생성합니다.

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  이름 필드는 이제 50자로 제한되기 때문에 경고가 생성됩니다. 데이터베이스의 이름에 50자 이상의 문자가 있는 경우 51자부터 마지막 문자까지 손실됩니다.

* SSOX에서 학생 테이블을 엽니다.

  ![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

  마이그레이션이 적용되기 전에 이름 열의 형식은 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)이었습니다. 이름 열은 이제 `nvarchar(50)`입니다. 열 이름은 `FirstMidName`에서 `FirstName`으로 변경되었습니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

다음 예제와 유사한 오류 메시지가 표시됩니다.

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* 프로젝트 폴더의 명령 창을 엽니다. 다음 명령을 입력하여 새 마이그레이션을 만들고 데이터베이스를 업데이트합니다.

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  데이터베이스 업데이트 명령은 다음 예제와 같은 오류를 표시합니다.

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

이 자습서에서 이 오류를 무시하는 방법은 초기 마이그레이션을 삭제하고 다시 만드는 것입니다. 자세한 내용은 [마이그레이션 자습서](xref:data/ef-rp/migrations)의 맨 위에 있는 SQLite 경고를 참조하세요.

* *Migrations* 폴더를 삭제합니다.
* 다음 명령을 실행하여 데이터베이스를 삭제하고, 새 초기 마이그레이션을 만들고, 마이그레이션을 적용합니다.

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* SQLite 도구에서 Student 테이블을 검토합니다. 이전에 FirstMidName이었던 열은 이제 FirstName입니다.

---

* 앱을 실행하고 [학생] 페이지로 이동합니다.
* 시간이 날짜와 함께 입력되거나 표시되지 않는 것을 알 수 있습니다.
* **새로 만들기** 를 선택하고, 50자보다 긴 이름을 입력해 보세요.

> [!Note]
> 다음 섹션의 일부 단계에서 앱 빌드는 컴파일러 오류를 생성합니다. 지침은 앱을 빌드하는 시기를 지정합니다.

## <a name="the-instructor-entity"></a>Instructor 엔터티

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

다음 코드로 *Models/Instructor.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

한 줄에 여러 특성이 있을 수 있습니다. `HireDate` 특성은 다음과 같이 작성될 수 있습니다.

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>탐색 속성

`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.

강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

강사는 최대 하나의 사무실을 가질 수 있으므로 `OfficeAssignment` 속성은 단일 `OfficeAssignment` 엔터티를 포함합니다. 사무실이 할당되지 않은 경우 `OfficeAssignment`는 Null입니다.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment 엔터티

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

다음 코드로 *Models/OfficeAssignment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>키 특성

`[Key]` 특성은 속성 이름이 classnameID 또는 ID가 아닌 다른 것일 때 PK(기본 키)로 속성을 식별하는 데 사용됩니다.

`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계가 있습니다. 사무실 할당은 할당된 강사와 관련하여 존재합니다. `OfficeAssignment` PK는 `Instructor` 엔터티에 대한 해당 FK(외래 키)이기도 합니다.

`InstructorID`는 ID 또는 classnameID 명명 규칙을 따르지 않으므로 EF Core는 `InstructorID`를 `OfficeAssignment`의 PK로 자동으로 인식할 수 없습니다. 따라서 `Key` 특성은 PK로 `InstructorID`를 식별하는 데 사용됩니다.

```csharp
[Key]
public int InstructorID { get; set; }
```

기본적으로 EF Core는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.

### <a name="the-instructor-navigation-property"></a>강사 탐색 속성

지정된 강사에 대한 `OfficeAssignment` 행이 없을 수 있으므로 `Instructor.OfficeAssignment` 탐색 속성은 Null일 수 있습니다. 강사는 사무실 할당이 없을 수 있습니다.

외래 키 `InstructorID` 형식이 비 nullable 값 형식인 `int`이므로 `OfficeAssignment.Instructor` 탐색 속성은 항상 강사 엔터티를 포함합니다. 사무실 할당은 강사 없이 존재할 수 없습니다.

`Instructor` 엔터티에 관련된 `OfficeAssignment` 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖습니다.

## <a name="the-course-entity"></a>Course 엔터티

![강좌 엔터티](complex-data-model/_static/course-entity.png)

*Models/Course.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

`Course` 엔터티에는 FK(외래 키) 속성 `DepartmentID`가 있습니다. `DepartmentID`는 관련된 `Department` 엔터티를 가리킵니다. `Course` 엔터티에는 `Department` 탐색 속성이 있습니다.

모델에 관련된 엔터티에 대한 탐색 속성이 있는 경우 EF Core는 데이터 모델에 대한 외래 키 속성이 필요하지 않습니다. EF Core는 필요한 어디든지 데이터베이스에 FK를 자동으로 만듭니다. EF Core는 자동으로 만들어진 FK에 대한 [섀도 속성](/ef/core/modeling/shadow-properties)을 만듭니다. 그러나 데이터 모델에 FK를 명시적으로 포함하면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다. 예를 들어 FK 키 속성 `DepartmentID`가 포함되지 *않은* 모델을 가정합니다. 과정 엔터티가 편집을 위해 페치되는 경우:

* `Department` 속성은 명시적으로 로드되지 않은 경우 Null입니다.
* 과정 엔터티를 업데이트하려면 `Department` 엔터티를 먼저 페치해야 합니다.

FK 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 `Department` 엔터티를 페치할 필요가 없습니다.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 특성

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 특성은 PK가 데이터베이스에서 생성되지 않고 애플리케이션에서 제공되는 것을 지정합니다.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

기본적으로 EF Core는 PK 값이 데이터베이스에서 생성되었다고 가정합니다. 일반적으로 데이터베이스에서 생성된 PK 값을 사용하는 것이 가장 좋습니다. `Course` 엔터티의 경우 사용자는 PK를 지정합니다. 예를 들어 수학 부서에 대한 1000 시리즈, 영어 부서에 대한 2000 시리즈와 같은 강좌 번호입니다.

`DatabaseGenerated` 특성은 기본 값을 생성하는 데 사용될 수도 있습니다. 예를 들어 데이터베이스는 행이 만들어지거나 업데이트된 날짜를 기록하기 위해 날짜 필드를 자동으로 생성할 수 있습니다. 자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

`Course` 엔터티의 FK(외래 키) 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.

강좌는 하나의 부서에 할당되었으므로 `DepartmentID` FK 및 `Department` 탐색 속성이 있습니다.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`는 [나중에](#many-to-many-relationships) 설명됩니다.

## <a name="the-department-entity"></a>Department 엔터티

![부서 엔터티](complex-data-model/_static/department-entity.png)

다음 코드로 *Models/Department.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>열 특성

이전에 `Column` 특성은 열 이름 매핑을 변경하는 데 사용되었습니다. `Department` 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용됩니다. `Budget` 열은 데이터베이스에서 SQL Server money 형식을 사용하여 정의됩니다.

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

열 매핑은 일반적으로 필요하지 않습니다. EF Core는 속성에 대한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택합니다. CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다. `Budget`은 통화에 대한 것이고 money 데이터 형식은 통화에 더욱 적합합니다.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

FK 및 탐색 속성은 다음과 같은 관계를 반영합니다.

* 부서는 관리자를 갖거나 갖지 않을 수 있습니다.
* 관리자는 항상 강사입니다. 따라서 `InstructorID` 속성은 `Instructor` 엔터티에 FK로 포함됩니다.

탐색 속성이 `Administrator`로 명명되나, `Instructor` 엔터티를 포함합니다.

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

위의 코드에서 물음표(?)는 속성이 nullable임을 지정합니다.

부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.

```csharp
public ICollection<Course> Courses { get; set; }
```

규칙에 따라 EF Core는 Null을 허용하지 않는 FK 및 다대다 관계에 대한 계단식 삭제를 활성화합니다. 이 기본 동작으로 인해 순환 계단식 삭제 규칙이 발생할 수 있습니다. 순환 계단식 삭제 규칙은 마이그레이션이 추가될 때 예외를 발생시킵니다.

예를 들어 `Department.InstructorID` 속성이 비 nullable로 정의된 경우 EF Core는 계단식 삭제 규칙을 구성합니다. 이 경우 관리자로 할당된 강사가 삭제되면 부서가 삭제됩니다. 이 시나리오에서는 제한 규칙을 사용하는 것이 좋습니다. 다음 [흐름 API](#fluent-api-alternative-to-attributes)는 제한 규칙을 설정하고 계단식 삭제를 사용하지 않도록 설정합니다.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>등록 엔터티

등록 레코드는 한 명의 학생이 수행하는 하나의 강좌에 대한 것입니다.

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

*Models/Enrollment.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

FK 속성 및 탐색 속성은 다음 관계를 반영합니다.

등록 레코드는 하나의 강좌에 해당하므로 `CourseID` FK 속성 및 `Course` 탐색 속성이 있습니다.

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

등록 레코드는 한 명의 학생에 해당하므로 `StudentID` FK 속성 및 `Student` 탐색 속성이 있습니다.

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>다대다 관계

`Student` 및 `Course` 엔터티 사이에 다대다 관계가 있습니다. `Enrollment` 엔터티는 데이터베이스에서 *페이로드를 사용하여* 다대다 조인 테이블로 작동합니다. "페이로드 사용"은 `Enrollment` 테이블이 조인된 테이블에 대한 FK 외에도 추가 데이터를 포함하는 것을 의미합니다(이 경우 PK 및 `Grade`).

다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다. (이 다이어그램은 EF 6.x에 대한 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)를 사용하여 생성되었습니다. 다이어그램 만들기는 자습서의 일부가 아닙니다.)

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(*)가 있으며, 이는 일대다 관계를 나타냅니다.

`Enrollment` 테이블에 등급 정보가 포함되지 않은 경우 두 개의 FK(`CourseID` 및 `StudentID`)를 포함해야 합니다. 페이로드가 없는 다대다 조인 테이블은 PJT(순수 조인 테이블)라고도 합니다.

`Instructor` 및 `Course` 엔터티에는 순수 조인 테이블을 사용하는 다대다 관계가 있습니다.

참고: EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다. 자세한 내용은 [EF Core 2.0에서 다대다 관계](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)를 참조하세요.

## <a name="the-courseassignment-entity"></a>CourseAssignment 엔터티

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

다음 코드로 *Models/CourseAssignment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

강사-과정 다대다 관계에는 조인 테이블이 필요하며, 해당 조인 테이블의 엔터티는 CourseAssignment입니다.

![강사-강좌 m:M](complex-data-model/_static/courseassignment.png)

조인 엔터티 `EntityName1EntityName2`의 이름을 지정하는 데 일반적입니다. 예를 들어 이 패턴을 사용하는 강사-과정 조인 테이블은 `CourseInstructor`입니다. 그러나 관계를 설명하는 이름을 사용하는 것이 좋습니다.

데이터 모델은 단순하게 시작하고 증가합니다. 페이로드 없는 조인 테이블(PJT)은 페이로드를 포함하도록 자주 업데이트됩니다. 설명이 포함된 엔터티 이름으로 시작하여 이름은 조인 테이블이 변경될 때 변경할 필요가 없습니다. 이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다. 예를 들어 Books 및 Customers는 Ratings라는 조인 엔터티를 통해 연결될 수 있습니다. 강사-강좌 다대다 관계의 경우 `CourseAssignment`는 `CourseInstructor`보다 선호됩니다.

### <a name="composite-key"></a>복합 키

`CourseAssignment`에서 두 개의 FK(`InstructorID` 및 `CourseID`)는 함께 `CourseAssignment` 테이블의 각 행을 고유하게 식별합니다. `CourseAssignment`는 전용 PK가 필요하지 않습니다. `InstructorID` 및 `CourseID` 속성은 복합 PK로 작동합니다. 복합 PK를 EF Core로 지정하는 유일한 방법은 *흐름 API* 를 사용하는 것입니다. 다음 섹션에서는 복합 PK를 구성하는 방법을 보여 줍니다.

복합 키를 사용하면 다음이 적용됩니다.

* 하나의 강좌에 대해 여러 행이 허용됩니다.
* 한 명의 강사에 대해 여러 행이 허용됩니다.
* 동일한 강사 및 과정에 대한 여러 행은 허용되지 않습니다.

`Enrollment` 조인 엔터티는 고유한 PK를 정의하므로 이러한 종류의 중복이 가능합니다. 이러한 중복 항목을 방지하려면:

* FK 필드에 고유 인덱스를 추가하거나
* `CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성합니다. 자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.

## <a name="update-the-database-context"></a>데이터베이스 컨텍스트 업데이트

다음 코드로 *Data/SchoolContext.cs* 를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

위의 코드는 새 엔터티를 추가하고 `CourseAssignment` 엔터티의 복합 PK를 구성합니다.

## <a name="fluent-api-alternative-to-attributes"></a>특성에 대한 흐름 API 대안

위의 코드에서 `OnModelCreating` 메서드는 *흐름 API* 를 사용하여 EF Core 동작을 구성합니다. API는 종종 일련의 메서드 호출을 단일 명령문으로 함께 연결하여 사용되기 때문에 “흐름”이라고 부릅니다. [다음 코드](/ef/core/modeling/#use-fluent-api-to-configure-a-model)는 흐름 API의 예제입니다.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

이 자습서에서 흐름 API는 특성으로 수행될 수 없는 데이터베이스 매핑을 위해서만 사용됩니다. 그러나 흐름 API는 특성으로 수행될 수 있는 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정할 수 있습니다.

`MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다. `MinimumLength`는 스키마를 변경하지 않으며 최소 길이 유효성 검사 규칙만 적용합니다.

일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다. 특성 및 흐름 API를 함께 사용할 수 있습니다. 흐름 API로만 수행될 수 있는 일부 구성이 있습니다(복합 PK 지정). 특성으로만 수행될 수 있는 일부 구성이 있습니다(`MinimumLength`). 흐름 API 또는 특성을 사용하기 위한 권장 방법:

* 이 두 가지 방법 중 하나를 선택합니다.
* 가능한 한 계속 선택한 방법을 사용합니다.

이 자습서에서 사용되는 일부 특성은 다음에 사용됩니다.

* 유효성 검사에만(예: `MinimumLength`)
* EF Core 구성에만(예: `HasKey`)
* 유효성 검사 및 EF Core 구성(예: `[StringLength(50)]`)

특성과 흐름 API의 비교에 대한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.

## <a name="entity-diagram"></a>엔터티 다이어그램

다음 그림은 EF Power Tools가 완벽한 School 모델을 만드는 다이어그램을 보여 줍니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

위의 다이어그램은 다음을 보여 줍니다.

* 여러 일대다 관계 줄(1 ~ \*)
* `Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계 줄(1 ~ 0..1)이 있습니다.
* `Instructor` 및 `Department` 엔터티 사이에는 영 또는 일대다 관계 줄(0..1 ~ *)이 있습니다.

## <a name="seed-the-database"></a>데이터베이스 시드

*Data/DbInitializer.cs* 에서 코드를 업데이트합니다.

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

위의 코드는 새 엔터티에 대한 시드 데이터를 제공합니다. 이 코드의 대부분은 새 엔터티 개체를 만들고 샘플 데이터를 로드합니다. 샘플 데이터는 테스트를 위해 사용됩니다. 다 대 다 조인 테이블을 시드할 수 있는 예제는 `Enrollments` 및 `CourseAssignments`를 참조하세요.

## <a name="add-a-migration"></a>마이그레이션 추가

프로젝트를 빌드합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC에서 다음 명령을 실행합니다.

```powershell
Add-Migration ComplexDataModel
```

위의 명령은 가능한 데이터 손실에 대한 경고를 표시합니다.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

`database update` 명령이 실행되는 경우 다음과 같은 오류가 생성됩니다.

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

다음 섹션에서는 이 오류에 대해 수행할 작업을 확인합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

마이그레이션을 추가하고 `database update` 명령을 실행하는 경우 다음 오류가 생성됩니다.

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

다음 섹션에서는 이 오류를 방지하는 방법을 확인합니다.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>마이그레이션 적용 또는 삭제하고 다시 만들기

기존 데이터베이스가 있으므로 변경 내용을 적용하는 방법을 고려해야 합니다. 이 자습서에서는 다음 두 가지 대안을 보여 줍니다.

* [데이터베이스를 삭제하고 다시 만들기](#drop). SQLite를 사용하는 경우 이 섹션을 선택합니다.
* [기존 데이터베이스에 마이그레이션 적용](#applyexisting). 이 섹션의 지침은 SQL Server에만 적용되며 **SQLite에는 적용되지 않습니다**. 

두 방법 모두 SQL Server에서 사용할 수 있습니다. 마이그레이션 적용 방법은 더 복잡하고 시간이 오래 걸리지만 실제 프로덕션 환경에 권장되는 방법입니다. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

SQL Server를 사용하고 다음 섹션의 마이그레이션 적용 방법을 수행하려는 경우 [이 섹션을 건너뜁니다](#apply-the-migration).

EF Core가 새로운 데이터베이스를 만들도록 강제하려면 데이터베이스를 삭제하고 업데이트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC( **패키지 관리자 콘솔** )에서 다음 명령을 입력합니다.

  ```powershell
  Drop-Database
  ```

* *마이그레이션* 폴더를 삭제 한 후 다음 명령을 실행합니다.

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 명령 창을 열고 프로젝트 폴더로 이동합니다. 프로젝트 폴더에는 *ContosoUniversity.csproj* 파일이 포함되어 있습니다.

* 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet ef database drop --force
  ```

* *마이그레이션* 폴더를 삭제 한 후 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

앱을 실행합니다. 앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다. `DbInitializer.Initialize`는 새 데이터베이스를 채웁니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

SSOX에서 데이터베이스를 선택합니다.

* SSOX가 이전에 열려 있던 경우 **새로 고침** 단추를 클릭합니다.
* **테이블** 노드를 확장합니다. 생성된 테이블이 표시됩니다.

  ![SSOX의 테이블](complex-data-model/_static/ssox-tables.png)

* **CourseAssignment** 테이블을 검사합니다.

  * **CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.
  * **CourseAssignment** 테이블에 데이터가 포함되어 있는지 확인합니다.

  ![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite 도구를 사용하여 데이터베이스를 검사합니다.

* 새 테이블 및 열.
* **CourseAssignment** 테이블과 같은 테이블의 시드된 데이터.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>마이그레이션 적용

이 섹션은 선택 사항입니다. 이 단계는 SQL Server LocalDB에서만, 앞의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 건너뛴 경우에만 적용됩니다.

마이그레이션이 기존 데이터로 실행될 때 기존 데이터로 충족되지 않는 FK 제약 조건이 있을 수 있습니다. 프로덕션 데이터와 함께 기존 데이터를 마이그레이션하도록 단계를 수행해야 합니다. 이 섹션에서는 FK 제약 조건 위반을 수정하는 예제를 제공합니다. 백업 없이 이러한 코드 변경을 만들지 마십시오. 앞의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 완료한 경우 이 코드를 변경하지 마세요.

*{timestamp}_ComplexDataModel.cs* 파일은 다음 코드를 포함합니다.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

위의 코드는 Null을 허용하지 않는 `DepartmentID` FK를 `Course` 테이블에 추가합니다. 테이블을 마이그레이션에서 업데이트할 수 없도록 이전 자습서의 데이터베이스는 `Course`에 행을 포함합니다.

기존 데이터를 사용하여 `ComplexDataModel` 마이그레이션 작업을 수행하려면:

* 새 열(`DepartmentID`)에 기본값을 제공하도록 코드를 변경합니다.
* 기본 부서로 작동하도록 "Temp"라는 가짜 부서를 만듭니다.

#### <a name="fix-the-foreign-key-constraints"></a>외래 키 제약 조건 수정

`ComplexDataModel` 마이그레이션 클래스에서 `Up` 메서드를 업데이트합니다.

* *{timestamp}_ComplexDataModel.cs* 파일을 엽니다.
* `DepartmentID` 열을 `Course` 테이블에 추가하는 코드 줄을 주석으로 처리합니다.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

다음 강조 표시된 코드를 추가합니다. 새 코드는 `.CreateTable( name: "Department"` 블록 뒤로 이동합니다.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

앞의 변경 내용으로 `ComplexDataModel.Up` 메서드를 실행한 후에 기존 `Course` 행은 “Temp” 부서에 연결됩니다.

여기에 표시된 상황을 처리하는 방법은 이 자습서에서 사용하도록 간소화되었습니다. 프로덕션 앱은:

* `Department` 행 및 관련 `Course` 행을 새 `Department` 행에 추가하는 코드 또는 스크립트를 포함합니다.
* `Course.DepartmentID`에 대해 "Temp" 부서 또는 기본값을 사용하지 않습니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC( **패키지 관리자 콘솔** )에서 다음 명령을 입력합니다.

  ```powershell
  Update-Database
  ```

`DbInitializer.Initialize` 메서드는 빈 데이터베이스에서만 작동하도록 디자인되었으므로 SSOX를 사용하여 Student 및 Course 테이블의 모든 행을 삭제합니다. (계단식 삭제는 Enrollment 테이블을 처리합니다.)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Visual Studio Code와 함께 SQL Server LocalDB를 사용하는 경우 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet ef database update
  ```

---

앱을 실행합니다. 앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다. `DbInitializer.Initialize`는 새 데이터베이스를 채웁니다.

## <a name="next-steps"></a>다음 단계

다음 두 자습서에서는 관련 데이터를 읽고 업데이트하는 방법을 보여 줍니다.

> [!div class="step-by-step"]
> [이전 자습서](xref:data/ef-rp/migrations)
> [다음 자습서](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이전 자습서에서는 세 가지 엔터티로 구성된 기본 데이터 모델을 사용했습니다. 이 자습서에서:

* 더 많은 엔터티 및 관계가 추가됩니다.
* 데이터 모델은 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 사용자 지정됩니다.

완성된 데이터 모델에 대한 엔터티 클래스는 다음 그림에 표시됩니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

해결할 수 없는 문제가 발생한 경우 [완성된 앱](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.

## <a name="customize-the-data-model-with-attributes"></a>특성을 사용하여 데이터 모델 사용자 지정

이 섹션에서 데이터 모델은 특성을 사용하여 사용자 지정됩니다.

### <a name="the-datatype-attribute"></a>DataType 특성

학생 페이지는 현재 등록 날짜의 시간을 표시합니다. 일반적으로 날짜 필드는 시간이 아닌 날짜만을 표시합니다.

*Models/Student.cs* 를 다음 강조 표시된 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정합니다. 이 경우 날짜 및 시간이 아닌 날짜만 표시되어야 합니다. [DataType 열거형](/dotnet/api/system.componentmodel.dataannotations.datatype)은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 앱에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다. 예를 들어:

* `mailto:` 링크는 `DataType.EmailAddress`에 대해 자동으로 만들어집니다.
* 날짜 선택기는 대부분의 브라우저에서 `DataType.Date`에 대해 제공됩니다.

`DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다. `DataType` 특성은 유효성 검사를 제공하지 않습니다.

`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다. 기본적으로 날짜 필드는 서버의 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)를 기본으로 하는 기본 형식에 따라 표시됩니다.

`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` 설정은 서식 지정이 편집 UI에도 적용되어야 함을 지정합니다. 일부 필드는 `ApplyFormatInEditMode`를 사용하면 안 됩니다. 예를 들어 통화 기호는 일반적으로 편집 텍스트 상자에 표시되면 안 됩니다.

`DisplayFormat` 특성은 단독으로 사용될 수 있습니다. 일반적으로 `DisplayFormat` 특성과 함께 `DataType` 특성을 사용하는 것이 좋습니다. `DataType` 특성은 화면에서 렌더링하는 방법과 대조적으로 데이터의 의미 체계를 전달합니다. `DataType` 특성은 `DisplayFormat`에서 사용할 수 없는 다음과 같은 이점을 제공합니다.

* 브라우저는 HTML5 기능을 활성화할 수 있습니다. 예를 들어 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크, 클라이언트 쪽 입력 유효성 검사 등을 표시합니다.
* 기본적으로 브라우저는 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.

자세한 내용은 [\<input> 태그 도우미 설명서](xref:mvc/views/working-with-forms#the-input-tag-helper)를 참조하세요.

앱을 실행합니다. 학생 인덱스 페이지로 이동합니다. 시간이 더 이상 표시되지 않습니다. `Student` 모델을 사용하는 모든 보기는 시간을 제외한 날짜를 표시합니다.

![시간 없이 날짜를 표시하는 학생 인덱스 페이지](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 특성

데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지는 특성으로 지정될 수 있습니다. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 특성은 데이터 필드에서 허용되는 최소 및 최대 문자 길이를 지정합니다. `StringLength` 특성은 또한 클라이언트 쪽 및 서버 쪽 유효성 검사를 제공합니다. 최소값은 데이터베이스 스키마에 영향을 주지 않습니다.

`Student` 모델을 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

위의 코드는 이름을 최대 50자로 제한합니다. `StringLength` 특성은 이름에 공백을 입력할 수 있습니다. [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 특성은 입력에 제한을 적용하는 데 사용됩니다. 예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 사전순이어야 합니다.

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

앱을 실행합니다.

* 학생 페이지로 이동합니다.
* **새로 만들기** 를 선택하고, 50자보다 긴 이름을 입력합니다.
* **만들기** 를 선택하면 클라이언트 쪽 유효성 검사가 오류 메시지를 표시합니다.

![문자열 길이 오류를 보여 주는 학생 인덱스 페이지](complex-data-model/_static/string-length-errors.png)

**SQL Server 개체 탐색기** (SSOX)에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.

![마이그레이션 전 SSOX의 학생 테이블](complex-data-model/_static/ssox-before-migration.png)

위의 이미지는 `Student` 테이블에 대한 스키마를 보여 줍니다. 마이그레이션은 DB에서 실행되지 않기 때문에 이름 필드에는 `nvarchar(MAX)` 형식이 있습니다. 마이그레이션이 이 자습서의 뒷부분에서 실행될 때 이름 필드는 `nvarchar(50)`가 됩니다.

### <a name="the-column-attribute"></a>열 특성

특성은 클래스 및 속성이 데이터베이스에 매핑되는 방법을 제어할 수 있습니다. 이 섹션에서 `Column` 특성은 DB에서 `FirstMidName` 속성의 이름을 "FirstName"으로 매핑하는 데 사용됩니다.

DB가 만들어질 때 모델의 속성 이름은 열 이름에 사용됩니다(`Column` 특성이 사용되는 경우 제외).

필드에 중간 이름도 포함될 수 있으므로 `Student` 모델은 첫 번째 이름 필드에 대해 `FirstMidName`을 사용합니다.

*Student.cs* 파일을 다음 강조 표시된 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

이전 변경으로 인해 앱의 `Student.FirstMidName`은 `Student` 테이블의 `FirstName` 열로 매핑됩니다.

`Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경됩니다. `SchoolContext`를 지원하는 모델은 데이터베이스와 더 이상 일치하지 않습니다. 마이그레이션을 적용하기 전에 앱이 실행되는 경우 다음과 같은 예외가 생성됩니다.

```
SqlException: Invalid column name 'FirstName'.
```

DB를 업데이트하려면:

* 프로젝트를 빌드합니다.
* 프로젝트 폴더의 명령 창을 엽니다. 다음 명령을 입력하여 새 마이그레이션을 만들고 DB를 업데이트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

`migrations add ColumnFirstName` 명령은 다음과 같은 경고 메시지를 생성합니다.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

이름 필드는 이제 50자로 제한되기 때문에 경고가 생성됩니다. DB의 이름에 50자 이상의 문자가 있는 경우 51자부터 마지막 문자까지가 손실됩니다.

* 앱을 테스트합니다.

SSOX에서 학생 테이블을 엽니다.

![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

마이그레이션이 적용되기 전에 이름 열은 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) 형식이었습니다. 이름 열은 이제 `nvarchar(50)`입니다. 열 이름은 `FirstMidName`에서 `FirstName`으로 변경되었습니다.

> [!Note]
> 다음 섹션의 일부 단계에서 앱 빌드는 컴파일러 오류를 생성합니다. 지침은 앱을 빌드하는 시기를 지정합니다.

## <a name="student-entity-update"></a>학생 엔터티 업데이트

![학생 엔터티](complex-data-model/_static/student-entity.png)

*Models/Student.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>필수 특성

`Required` 특성에서 이름 속성은 필수 필드입니다. `Required` 특성은 값 형식(`DateTime`, `int`, `double` 등)과 같은 비 nullable 형식에 필요하지 않습니다. Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.

`Required` 특성은 `StringLength` 특성에서 최소 길이 매개 변수로 대체될 수 있습니다.

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>표시 특성

`Display` 특성은 텍스트 상자에 대한 캡션이 "First Name", "Last Name", "Full Name" 및 "Enrollment Date"여야 함을 지정합니다. 기본 캡션에는 단어를 분할하는 공백이 없습니다(예: "Lastname)".

### <a name="the-fullname-calculated-property"></a>FullName 계산된 속성

`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다. `FullName`은 설정될 수 없습니다. get 접근자만 있습니다. 데이터베이스에서 `FullName` 열이 생성되지 않습니다.

## <a name="create-the-instructor-entity"></a>강사 엔터티 만들기

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

다음 코드로 *Models/Instructor.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

한 줄에 여러 특성이 있을 수 있습니다. `HireDate` 특성은 다음과 같이 작성될 수 있습니다.

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments 및 OfficeAssignment 탐색 속성

`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.

강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

탐색 속성이 여러 엔터티를 보유하는 경우:

* 항목을 추가, 삭제 및 업데이트할 수 있는 목록 형식이어야 합니다.

탐색 속성 유형은 다음을 포함합니다.

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

`ICollection<T>`가 지정되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.

`CourseAssignment` 엔터티는 다대다 관계의 섹션에 설명되어 있습니다.

Contoso University 비즈니스 규칙은 한 명의 강사가 최대 하나의 사무실을 가질 수 있음을 나타냅니다. `OfficeAssignment` 속성은 단일 `OfficeAssignment` 엔터티를 보유합니다. 사무실이 할당되지 않은 경우 `OfficeAssignment`는 Null입니다.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>OfficeAssignment 엔터티 만들기

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

다음 코드로 *Models/OfficeAssignment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>키 특성

`[Key]` 특성은 속성 이름이 classnameID 또는 ID가 아닌 다른 것일 때 PK(기본 키)로 속성을 식별하는 데 사용됩니다.

`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계가 있습니다. 사무실 할당은 할당된 강사와 관련하여 존재합니다. `OfficeAssignment` PK는 `Instructor` 엔터티에 대한 해당 FK(외래 키)이기도 합니다. EF Core는 다음과 같은 이유로 `OfficeAssignment`의 PK로 `InstructorID`를 자동으로 인식할 수 없습니다.

* `InstructorID`는 ID 또는 classnameID 명명 규칙을 따르지 않습니다.

따라서 `Key` 특성은 PK로 `InstructorID`를 식별하는 데 사용됩니다.

```csharp
[Key]
public int InstructorID { get; set; }
```

기본적으로 EF Core는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.

### <a name="the-instructor-navigation-property"></a>강사 탐색 속성

`Instructor` 엔터티에 대한 `OfficeAssignment` 탐색 속성은 다음과 같은 이유로 nullable입니다.

* 참조 형식(예: 클래스는 nullable)
* 강사는 사무실 할당이 없을 수 있습니다.

`OfficeAssignment` 엔터티는 다음과 같은 이유로 비 nullable `Instructor` 탐색 속성을 갖습니다.

* `InstructorID`은 Null을 허용하지 않습니다.
* 사무실 할당은 강사 없이 존재할 수 없습니다.

`Instructor` 엔터티에 관련된 `OfficeAssignment` 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖습니다.

`[Required]` 특성은 `Instructor` 탐색 속성에 적용될 수 있습니다.

```csharp
[Required]
public Instructor Instructor { get; set; }
```

위의 코드는 관련된 강사가 있어야 함을 지정합니다. `InstructorID` 외래 키(PK이기도 함)는 Null을 허용하지 않으므로 위의 코드는 필요하지 않습니다.

## <a name="modify-the-course-entity"></a>강좌 엔터티 수정

![강좌 엔터티](complex-data-model/_static/course-entity.png)

*Models/Course.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course` 엔터티에는 FK(외래 키) 속성 `DepartmentID`가 있습니다. `DepartmentID`는 관련된 `Department` 엔터티를 가리킵니다. `Course` 엔터티에는 `Department` 탐색 속성이 있습니다.

모델에 관련된 엔터티에 대한 탐색 속성이 있는 경우 EF Core는 데이터 모델에 대한 FK 속성이 필요하지 않습니다.

EF Core는 필요한 어디든지 데이터베이스에 FK를 자동으로 만듭니다. EF Core는 자동으로 만들어진 FK에 대한 [섀도 속성](/ef/core/modeling/shadow-properties)을 만듭니다. 데이터 모델에 FK가 있으면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다. 예를 들어 FK 키 속성 `DepartmentID`가 포함되지 *않은* 모델을 가정합니다. 과정 엔터티가 편집을 위해 페치되는 경우:

* `Department` 엔터티는 명시적으로 로드되지 않은 경우 Null입니다.
* 과정 엔터티를 업데이트하려면 `Department` 엔터티를 먼저 페치해야 합니다.

FK 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 `Department` 엔터티를 페치할 필요가 없습니다.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 특성

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 특성은 PK가 데이터베이스에서 생성되지 않고 애플리케이션에서 제공되는 것을 지정합니다.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

기본적으로 EF Core는 PK 값이 DB에서 생성되었다고 가정합니다. DB에서 생성된 PK 값은 일반적으로 가장 좋은 방법입니다. `Course` 엔터티의 경우 사용자는 PK를 지정합니다. 예를 들어 수학 부서에 대한 1000 시리즈, 영어 부서에 대한 2000 시리즈와 같은 강좌 번호입니다.

`DatabaseGenerated` 특성은 기본 값을 생성하는 데 사용될 수도 있습니다. 예를 들어 DB는 행이 만들어지거나 업데이트된 날짜를 기록하기 위해 날짜 필드를 자동으로 생성할 수 있습니다. 자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

`Course` 엔터티의 FK(외래 키) 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.

강좌는 하나의 부서에 할당되었으므로 `DepartmentID` FK 및 `Department` 탐색 속성이 있습니다.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`는 [나중에](#many-to-many-relationships) 설명됩니다.

## <a name="create-the-department-entity"></a>부서 엔터티 만들기

![부서 엔터티](complex-data-model/_static/department-entity.png)

다음 코드로 *Models/Department.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>열 특성

이전에 `Column` 특성은 열 이름 매핑을 변경하는 데 사용되었습니다. `Department` 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용됩니다. `Budget` 열은 DB에서 SQL Server money 형식을 사용하여 정의됩니다.

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

열 매핑은 일반적으로 필요하지 않습니다. EF Core는 일반적으로 속성에 대한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택합니다. CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다. `Budget`은 통화에 대한 것이고 money 데이터 형식은 통화에 더욱 적합합니다.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

FK 및 탐색 속성은 다음과 같은 관계를 반영합니다.

* 부서는 관리자를 갖거나 갖지 않을 수 있습니다.
* 관리자는 항상 강사입니다. 따라서 `InstructorID` 속성은 `Instructor` 엔터티에 FK로 포함됩니다.

탐색 속성이 `Administrator`로 명명되나, `Instructor` 엔터티를 포함합니다.

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

위의 코드에서 물음표(?)는 속성이 nullable임을 지정합니다.

부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.

```csharp
public ICollection<Course> Courses { get; set; }
```

참고: 규칙에 따라 EF Core는 Null을 허용하지 않는 FK 및 다대다 관계에 대한 계단식 삭제를 활성화합니다. 계단식 삭제로 인해 순환 계단식 삭제 규칙이 발생할 수 있습니다. 순환 계단식 삭제 규칙은 마이그레이션이 추가될 때 예외를 발생시킵니다.

예를 들어 `Department.InstructorID` 속성이 null 허용 안 함으로 정의된 경우:

* EF Core는 강사가 삭제될 때 부서를 삭제하도록 계단식 삭제 규칙을 구성합니다.
* 강사가 삭제될 때 부서 삭제는 의도된 동작이 아닙니다.
* 다음 [흐름 API](#fluent-api-alternative-to-attributes)는 계단식 대신 제한 규칙을 설정합니다.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

위의 코드는 부서 강사 관계에서 계단식 삭제를 비활성화합니다.

## <a name="update-the-enrollment-entity"></a>등록 엔터티 업데이트

등록 레코드는 한 명의 학생이 수행하는 하나의 강좌에 대한 것입니다.

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

*Models/Enrollment.cs* 를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

FK 속성 및 탐색 속성은 다음 관계를 반영합니다.

등록 레코드는 하나의 강좌에 해당하므로 `CourseID` FK 속성 및 `Course` 탐색 속성이 있습니다.

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

등록 레코드는 한 명의 학생에 해당하므로 `StudentID` FK 속성 및 `Student` 탐색 속성이 있습니다.

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>다대다 관계

`Student` 및 `Course` 엔터티 사이에 다대다 관계가 있습니다. `Enrollment` 엔터티는 데이터베이스에서 *페이로드를 사용하여* 다대다 조인 테이블로 작동합니다. "페이로드 사용"은 `Enrollment` 테이블이 조인된 테이블에 대한 FK 외에도 추가 데이터를 포함하는 것을 의미합니다(이 경우 PK 및 `Grade`).

다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다. (이 다이어그램은 EF 6.x에 대한 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)를 사용하여 생성되었습니다. 다이어그램 만들기는 자습서의 일부가 아닙니다.)

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(*)가 있으며, 이는 일대다 관계를 나타냅니다.

`Enrollment` 테이블에 등급 정보가 포함되지 않은 경우 두 개의 FK(`CourseID` 및 `StudentID`)를 포함해야 합니다. 페이로드가 없는 다대다 조인 테이블은 PJT(순수 조인 테이블)라고도 합니다.

`Instructor` 및 `Course` 엔터티에는 순수 조인 테이블을 사용하는 다대다 관계가 있습니다.

참고: EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다. 자세한 내용은 [EF Core 2.0에서 다대다 관계](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)를 참조하세요.

## <a name="the-courseassignment-entity"></a>CourseAssignment 엔터티

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

다음 코드로 *Models/CourseAssignment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>강사-강좌

![강사-강좌 m:M](complex-data-model/_static/courseassignment.png)

강사-강좌 다대다 관계:

* 엔터티 집합으로 표현되어야 하는 조인 테이블이 필요합니다.
* 순수 조인 테이블(페이로드 없는 테이블)입니다.

조인 엔터티 `EntityName1EntityName2`의 이름을 지정하는 데 일반적입니다. 예를 들어 이 패턴을 사용하는 강사-강좌 조인 테이블은 `CourseInstructor`입니다. 그러나 관계를 설명하는 이름을 사용하는 것이 좋습니다.

데이터 모델은 단순하게 시작하고 증가합니다. 페이로드 없는 조인(PJT)은 페이로드를 포함하도록 자주 발전합니다. 설명이 포함된 엔터티 이름으로 시작하여 이름은 조인 테이블이 변경될 때 변경할 필요가 없습니다. 이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다. 예를 들어 Books 및 Customers는 Ratings라는 조인 엔터티를 통해 연결될 수 있습니다. 강사-강좌 다대다 관계의 경우 `CourseAssignment`는 `CourseInstructor`보다 선호됩니다.

### <a name="composite-key"></a>복합 키

FK는 Null을 허용하지 않습니다. `CourseAssignment`에서 두 개의 FK(`InstructorID` 및 `CourseID`)는 함께 `CourseAssignment` 테이블의 각 행을 고유하게 식별합니다. `CourseAssignment`는 전용 PK가 필요하지 않습니다. `InstructorID` 및 `CourseID` 속성은 복합 PK로 작동합니다. 복합 PK를 EF Core로 지정하는 유일한 방법은 *흐름 API* 를 사용하는 것입니다. 다음 섹션에서는 복합 PK를 구성하는 방법을 보여 줍니다.

복합 키는 다음을 확인합니다.

* 하나의 강좌에 대해 여러 행이 허용됩니다.
* 한 명의 강사에 대해 여러 행이 허용됩니다.
* 동일한 강사 및 강좌에 대한 여러 행은 허용되지 않습니다.

`Enrollment` 조인 엔터티는 고유한 PK를 정의하므로 이러한 종류의 중복이 가능합니다. 이러한 중복 항목을 방지하려면:

* FK 필드에 고유 인덱스를 추가하거나
* `CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성합니다. 자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.

## <a name="update-the-db-context"></a>DB 컨텍스트 업데이트

다음 강조 표시된 코드를 *Data/SchoolContext.cs* 에 추가합니다.

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

위의 코드는 새 엔터티를 추가하고 `CourseAssignment` 엔터티의 복합 PK를 구성합니다.

## <a name="fluent-api-alternative-to-attributes"></a>특성에 대한 흐름 API 대안

위의 코드에서 `OnModelCreating` 메서드는 *흐름 API* 를 사용하여 EF Core 동작을 구성합니다. API는 종종 일련의 메서드 호출을 단일 명령문으로 함께 연결하여 사용되기 때문에 “흐름”이라고 부릅니다. [다음 코드](/ef/core/modeling/#use-fluent-api-to-configure-a-model)는 흐름 API의 예제입니다.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

이 자습서에서 흐름 API는 특성으로 수행될 수 없는 DB 매핑을 위해서만 사용됩니다. 그러나 흐름 API는 특성으로 수행될 수 있는 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정할 수 있습니다.

`MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다. `MinimumLength`는 스키마를 변경하지 않으며 최소 길이 유효성 검사 규칙만 적용합니다.

일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다. 특성 및 흐름 API를 함께 사용할 수 있습니다. 흐름 API로만 수행될 수 있는 일부 구성이 있습니다(복합 PK 지정). 특성으로만 수행될 수 있는 일부 구성이 있습니다(`MinimumLength`). 흐름 API 또는 특성을 사용하기 위한 권장 방법:

* 이 두 가지 방법 중 하나를 선택합니다.
* 가능한 한 계속 선택한 방법을 사용합니다.

이 자습서에서 사용되는 일부 특성은 다음에 사용됩니다.

* 유효성 검사에만(예: `MinimumLength`)
* EF Core 구성에만(예: `HasKey`)
* 유효성 검사 및 EF Core 구성(예: `[StringLength(50)]`)

특성과 흐름 API의 비교에 대한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.

## <a name="entity-diagram-showing-relationships"></a>관계를 보여 주는 엔터티 다이어그램

다음 그림은 EF Power Tools가 완벽한 School 모델을 만드는 다이어그램을 보여 줍니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

위의 다이어그램은 다음을 보여 줍니다.

* 여러 일대다 관계 줄(1 ~ \*)
* `Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계 줄(1 ~ 0..1)이 있습니다.
* `Instructor` 및 `Department` 엔터티 사이에는 영 또는 일대다 관계 줄(0..1 ~ *)이 있습니다.

## <a name="seed-the-db-with-test-data"></a>테스트 데이터로 DB 시드

*Data/DbInitializer.cs* 에서 코드를 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

위의 코드는 새 엔터티에 대한 시드 데이터를 제공합니다. 이 코드의 대부분은 새 엔터티 개체를 만들고 샘플 데이터를 로드합니다. 샘플 데이터는 테스트를 위해 사용됩니다. 다 대 다 조인 테이블을 시드할 수 있는 예제는 `Enrollments` 및 `CourseAssignments`를 참조하세요.

## <a name="add-a-migration"></a>마이그레이션 추가

프로젝트를 빌드합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

위의 명령은 가능한 데이터 손실에 대한 경고를 표시합니다.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

`database update` 명령이 실행되는 경우 다음과 같은 오류가 생성됩니다.

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>마이그레이션 적용

기존 데이터베이스가 있으므로 향후 변경 내용을 적용하는 방법을 고려해야 합니다. 이 자습서에서는 두 가지 방법을 보여 줍니다.

* [데이터베이스를 삭제하고 다시 만들기](#drop)
* [기존 데이터베이스에 마이그레이션 적용](#applyexisting). 이 방법은 더 복잡하고 시간이 오래 걸리지만 실제 프로덕션 환경에 권장되는 방법입니다. **참고** : 이는 자습서의 선택적 섹션입니다. 삭제하고 다시 만들기 단계를 수행하고 이 섹션을 건너뛸 수 있습니다. 이 섹션의 단계를 수행하지 않으려면 삭제하고 다시 만들기 단계를 수행하지 마세요. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>데이터베이스를 삭제하고 다시 만들기

업데이트된 `DbInitializer`의 코드는 새 엔터티에 대한 시드 데이터를 추가합니다. EF Core가 새로운 DB를 만들도록 강제하려면 DB를 삭제하고 업데이트합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC( **패키지 관리자 콘솔** )에서 다음 명령을 입력합니다.

```powershell
Drop-Database
Update-Database
```

PMC에서 `Get-Help about_EntityFrameworkCore`를 실행하여 도움말 정보를 가져옵니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

명령 창을 열고 프로젝트 폴더로 이동합니다. 프로젝트 폴더에는 *Startup.cs* 파일이 포함되어 있습니다.

명령 창에서 다음을 입력합니다.

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

앱을 실행합니다. 앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다. `DbInitializer.Initialize`는 새 DB를 채웁니다.

SSOX에서 DB를 엽니다.

* SSOX가 이전에 열려 있던 경우 **새로 고침** 단추를 클릭합니다.
* **테이블** 노드를 확장합니다. 생성된 테이블이 표시됩니다.

![SSOX의 테이블](complex-data-model/_static/ssox-tables.png)

**CourseAssignment** 테이블을 검사합니다.

* **CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택합니다.
* **CourseAssignment** 테이블에 데이터가 포함되어 있는지 확인합니다.

![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>기존 데이터베이스에 마이그레이션 적용

이 섹션은 선택 사항입니다. 이러한 단계는 이전의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 건너뛴 경우에만 적용됩니다.

마이그레이션이 기존 데이터로 실행될 때 기존 데이터로 충족되지 않는 FK 제약 조건이 있을 수 있습니다. 프로덕션 데이터와 함께 기존 데이터를 마이그레이션하도록 단계를 수행해야 합니다. 이 섹션에서는 FK 제약 조건 위반을 수정하는 예제를 제공합니다. 백업 없이 이러한 코드 변경을 만들지 마십시오. 이전 섹션을 완료하고 데이터베이스를 업데이트한 경우 이러한 코드 변경을 만들지 마십시오.

*{timestamp}_ComplexDataModel.cs* 파일은 다음 코드를 포함합니다.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

위의 코드는 Null을 허용하지 않는 `DepartmentID` FK를 `Course` 테이블에 추가합니다. 테이블을 마이그레이션에서 업데이트할 수 없도록 이전 자습서의 DB는 `Course`에 행을 포함합니다.

기존 데이터를 사용하여 `ComplexDataModel` 마이그레이션 작업을 수행하려면:

* 새 열(`DepartmentID`)에 기본값을 제공하도록 코드를 변경합니다.
* 기본 부서로 작동하도록 "Temp"라는 가짜 부서를 만듭니다.

#### <a name="fix-the-foreign-key-constraints"></a>외래 키 제약 조건 수정

`ComplexDataModel` 클래스 `Up` 메서드를 업데이트합니다.

* *{timestamp}_ComplexDataModel.cs* 파일을 엽니다.
* `DepartmentID` 열을 `Course` 테이블에 추가하는 코드 줄을 주석으로 처리합니다.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

다음 강조 표시된 코드를 추가합니다. 새 코드는 `.CreateTable( name: "Department"` 블록 뒤로 이동합니다.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

앞의 변경 내용으로 `ComplexDataModel` `Up` 메서드를 실행한 후에 기존 `Course` 행은 “Temp” 부서에 연결됩니다.

프로덕션 앱은:

* `Department` 행 및 관련 `Course` 행을 새 `Department` 행에 추가하는 코드 또는 스크립트를 포함합니다.
* `Course.DepartmentID`에 대해 "Temp" 부서 또는 기본값을 사용하지 않습니다.

다음 자습서에서는 관련된 데이터를 설명합니다.

## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전(1부)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [이 자습서의 YouTube 버전(2부)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [이전](xref:data/ef-rp/migrations)
> [다음](xref:data/ef-rp/read-related-data)

::: moniker-end
