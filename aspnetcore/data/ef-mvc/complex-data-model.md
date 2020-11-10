---
title: '자습서: 복잡한 데이터 모델 만들기 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 더 많은 엔터티 및 관계를 추가하고, 서식 지정, 유효성 검사 및 매핑 규칙을 지정하여 데이터 모델을 사용자 지정합니다.
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cee9e9eb4c5435f3f63f7d1d04f131d88effe9f6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054478"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>자습서: 복잡한 데이터 모델 만들기 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서는 세 가지 엔터티로 구성된 간단한 데이터 모델을 사용했습니다. 이 자습서에서는 더 많은 엔터티 및 관계를 추가하고, 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 데이터 모델을 사용자 지정합니다.

완료되면 엔터티 클래스는 다음 그림에 표시된 완성된 데이터 모델을 구성하게 됩니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 데이터 모델 사용자 지정
> * Student 엔터티 변경
> * Instructor 엔터티 만들기
> * OfficeAssignment 엔터티 만들기
> * Course 엔터티 수정
> * Department 엔터티 만들기
> * Enrollment 엔터티 수정
> * 데이터베이스 컨텍스트 업데이트
> * 테스트 데이터로 데이터베이스 시드
> * 마이그레이션 추가
> * 연결 문자열 변경
> * 데이터베이스 업데이트

## <a name="prerequisites"></a>사전 요구 사항

* [EF Core 마이그레이션 사용](migrations.md)

## <a name="customize-the-data-model"></a>데이터 모델 사용자 지정

이 섹션에서는 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하는 특성을 사용하여 데이터 모델을 사용자 지정하는 방법을 배웁니다. 그런 다음, 이어지는 몇 개 섹션에서는 특성을 이미 만든 클래스에 추가하고, 모델에 나머지 엔터티 형식에 대한 새 클래스를 만들어 완벽한 학교 데이터 모델을 만듭니다.

### <a name="the-datatype-attribute"></a>DataType 특성

학생 등록 날짜의 경우, 이 필드에서 필요한 것은 날짜이지만 현재 모든 웹 페이지는 날짜와 함께 시간을 표시합니다. 데이터 주석 특성을 사용하면 데이터를 표시하는 모든 보기에서 표시 형식을 해결하는 하나의 코드 변경을 만들 수 있습니다. 수행 방법의 예제를 보려면 특성을 `Student` 클래스의 `EnrollmentDate` 속성에 추가합니다.

*Models/Student.cs* 에서 다음 예제와 같이 `System.ComponentModel.DataAnnotations` 네임스페이스에 대한 `using` 문을 추가하고 `DataType` 및 `DisplayFormat` 특성을 `EnrollmentDate` 속성에 추가합니다.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

`DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다. 이 경우에는 날짜 및 시간이 아닌 날짜만 추적하고자 합니다. `DataType` 열거형은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 다양한 데이터 형식을 제공합니다. `DataType` 특성을 통해 응용 프로그램에서 자동으로 형식별 기능을 제공하도록 설정할 수도 있습니다. 예를 들어, `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들고 HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기를 제공할 수 있습니다. `DataType` 특성은 HTML 5 브라우저가 인식할 수 있는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다. `DataType` 특성은 유효성 검사를 제공하지 않습니다.

`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다. 기본적으로 데이터 필드는 서버의 CultureInfo의 기본 형식에 따라 표시됩니다.

`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` 설정은 값이 편집을 위해 텍스트 상자에 표시될 때 서식 지정도 적용되어야 함을 지정합니다. (통화 값의 경우 편집을 위한 텍스트 상자에 통화 기호를 표시하지 않는 등, 특정 필드에 대해서 이것이 필요하지 않을 수 있습니다.)

`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성도 사용하는 것이 좋습니다. `DataType` 특성은 데이터를 화면에 렌더링하는 방법과 반대로 데이터의 의미 체계를 전달하고 `DisplayFormat`으로 가져올 수 없는 다음과 같은 이점을 제공합니다.

* 브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크, 일부 클라이언트 쪽 입력 유효성 검사 등을 표시하기 위해).

* 기본적으로 브라우저는 사용자의 로캘에 따른 올바른 서식을 사용하여 데이터를 렌더링합니다.

자세한 내용은 [\<input> 태그 도우미 설명서](../../mvc/views/working-with-forms.md#the-input-tag-helper)를 참조하세요.

앱을 실행하고 학생 인덱스 페이지로 이동하여 등록 날짜에 대해 시간이 더 이상 표시되지 않음을 확인합니다. 학생 모델을 사용하는 다른 보기에도 동일하게 적용됩니다.

![시간 없이 날짜를 표시하는 학생 인덱스 페이지](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 특성

특성을 사용하여 데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지를 지정할 수 있습니다. `StringLength` 특성은 데이터베이스의 최대 길이를 설정하고, ASP.NET Core MVC에 대한 클라이언트 쪽 및 서버 쪽 유효성을 검사를 제공합니다. 이 특성의 최소 문자열 길이를 지정할 수도 있지만, 최소값은 데이터베이스 스키마에 영향을 주지 않습니다.

사용자가 이름을 50자 이하로 입력하였는지를 확인한다고 가정합니다. 이 제한 사항을 추가하려면 다음 예제와 같이 `StringLength` 특성을 `LastName` 및 `FirstMidName` 속성에 추가합니다.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

`StringLength` 특성은 이름에 공백을 입력할 수 있습니다. `RegularExpression` 특성을 사용하여 입력에 제한을 적용할 수 있습니다. 예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 알파벳순이어야 합니다.

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

`MaxLength` 특성은 `StringLength` 특성과 비슷한 기능을 제공하지만, 클라이언트 쪽 유효성 검사는 제공하지 않습니다.

이제 데이터베이스 스키마에서 변경이 필요한 방식으로 데이터베이스 모델이 변경되었습니다. 애플리케이션 UI를 사용하여 데이터베이스에 추가했을 수도 있는 데이터 손실 없이 마이그레이션을 사용하여 스키마를 업데이트합니다.

변경 내용을 저장하고 프로젝트를 빌드합니다. 그런 다음, 프로젝트 폴더에서 명령 창을 열고 다음 명령을 입력합니다.

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

`migrations add` 명령은 변경으로 인해 두 개의 열에 대한 최대 길이가 짧아질 수 있으므로 데이터 손실이 발생할 수 있음에 대해 경고합니다.  마이그레이션은 *\<timeStamp>_MaxLengthOnNames.cs* 라는 파일을 만듭니다. 이 파일에는 현재 데이터 모델과 일치하도록 데이터베이스를 업데이트하는 `Up` 메서드의 코드가 포함됩니다. `database update` 명령은 해당 코드를 실행합니다.

timestamp가 접두사로 사용된 마이그레이션 파일 이름이 마이그레이션을 요청하는 Entity Framework에서 사용됩니다. update-database 명령을 실행하기 전에 여러 마이그레이션을 만들 수 있습니다. 그런 다음, 모든 마이그레이션이 생성된 순서 대로 적용됩니다.

앱을 실행하고, **학생** 탭을 선택하고, **새로 만들기** 를 클릭한 다음, 50자보다 긴 이름을 입력해 봅니다. 애플리케이션에서 이 작업을 수행하지 못하게 합니다. 

### <a name="the-column-attribute"></a>열 특성

또한 특성을 사용하여 클래스 및 속성을 데이터베이스에 매핑하는 방법을 제어할 수 있습니다. 필드에 중간 이름이 포함될 수 있으므로 첫 번째 이름 필드에 이름 `FirstMidName`을 사용한 경우를 가정합니다. 하지만 데이터베이스에 임시 쿼리를 작성하는 사용자는 해당 이름이 익숙하기 때문에 데이터베이스 열 이름을 `FirstName`으로 하려고 합니다. 이 매핑을 수행하기 위해 `Column` 특성을 사용할 수 있습니다.

`Column` 특성은 데이터베이스를 만드는 시기를 지정하고 `FirstMidName` 속성에 매핑하는 `Student` 테이블의 열 이름은 `FirstName`이 됩니다. 즉, 코드가 `Student.FirstMidName`을 참조하는 경우 데이터는 `Student` 테이블의 `FirstName` 열에서 가져오거나 업데이트됩니다. 열 이름을 지정하지 않으면 속성 이름과 동일한 이름이 지정됩니다.

다음 강조 표시된 코드에 나와 있는 것처럼 *Student.cs* 파일에서 `System.ComponentModel.DataAnnotations.Schema`에 대한 `using` 문을 추가하고, 열 이름 특성을 `FirstMidName` 속성에 추가합니다.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

`Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경되므로 데이터베이스가 일치하지 않습니다.

변경 내용을 저장하고 프로젝트를 빌드합니다. 그런 다음, 프로젝트 폴더에서 명령 창을 열고 다음 명령을 입력하여 다른 마이그레이션을 만듭니다.

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

**SQL Server 개체 탐색기** 에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.

![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

처음 두 마이그레이션을 적용하기 전에 이름 열은 nvarchar(MAX) 형식이었습니다. 이제는 nvarchar(50)이며 열 이름이 FirstMidName에서 FirstName으로 변경되었습니다.

> [!Note]
> 다음 섹션의 모든 엔터티 클래스 만들기를 완료하기 전에 컴파일을 시도하는 경우 컴파일러 오류가 발생할 수 있습니다.

## <a name="changes-to-student-entity"></a>Student 엔터티 변경

![학생 엔터티](complex-data-model/_static/student-entity.png)

*Models/Student.cs* 에서 앞서 추가한 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>필수 특성

`Required` 특성에서 이름 속성은 필수 필드입니다. `Required` 특성은 값 형식(DateTime, int, double, float 등)과 같은 null을 허용하지 않는 형식에 필요하지 않습니다. Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.

`MinimumLength`가 적용되려면 `Required` 특성이 `MinimumLength`와 함께 사용되어야 합니다.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>표시 특성

`Display` 특성은 텍스트 상자에 대한 캡션이 각 인스턴스의 속성 이름 대신 “First Name”, “Last Name”, “Full Name” 및 “Enrollment Date”여야 함을 지정합니다(단어를 분할하는 공백 없음).

### <a name="the-fullname-calculated-property"></a>FullName 계산된 속성

`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다. 따라서 get 접근자만 있으며 `FullName` 열은 데이터베이스에 생성되지 않습니다.

## <a name="create-instructor-entity"></a>Instructor 엔터티 만들기

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

템플릿 코드를 다음 코드로 바꾸어 *Models/Instructor.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

학생과 강사 엔터티의 여러 속성이 동일하다는 것을 확인하세요. 이 시리즈의 뒷부분에 나오는 [상속 구현](inheritance.md) 자습서에서는 이 코드를 리팩터링하여 중복을 제거합니다.

다음과 같이 `HireDate` 특성을 쓸 수 있도록 여러 특성을 한 줄에 배치합니다.

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments 및 OfficeAssignment 탐색 속성

`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.

강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

탐색 속성이 여러 엔터티를 포함할 수 있는 경우, 해당 형식은 항목이 추가, 삭제 및 업데이트될 수 있는 목록이어야 합니다.  `List<T>` 또는 `HashSet<T>`와 같은 형식 또는 `ICollection<T>`를 지정할 수 있습니다. `ICollection<T>`를 지정하는 경우 EF는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.

이러한 항목은 다대다 관계에 대한 섹션의 아래쪽에 설명된 `CourseAssignment` 엔터티이기 때문입니다.

Contoso University 비즈니스 규칙에 따르면 강사는 최대 하나의 사무실만 가질 수 있으므로 `OfficeAssignment` 속성은 단일 OfficeAssignment 엔터티를 포함합니다(사무실이 할당되지 않은 경우 null일 수 있음).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>OfficeAssignment 엔터티 만들기

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

다음 코드로 *Models/OfficeAssignment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Key 특성

강사와 OfficeAssignment 엔터티 간의 일대영 또는 일 관계가 있습니다. 사무실 할당은 할당된 강사에 관하여 존재하며, 따라서 해당 기본 키도 강사 엔터티에 대한 해당 외래 키입니다. 하지만, 해당 이름이 ID 및 classnameID 명명 규칙을 따르지 않으므로 Entity Framework는 자동으로 InstructorID를 이 엔터티의 기본 키로 인식할 수 없습니다. 따라서 `Key` 특성은 키로 식별하는 데 사용됩니다.

```csharp
[Key]
public int InstructorID { get; set; }
```

엔터티에 자체 기본 키가 있지만 classnameID 또는 ID가 아닌 다른 항목 속성의 이름을 지정하려는 경우 `Key` 특성을 사용할 수도 있습니다.

기본적으로 EF는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.

### <a name="the-instructor-navigation-property"></a>강사 탐색 속성

강사 엔터티에 null 허용 `OfficeAssignment` 탐색 속성이 있으므로(강사에 사무실이 할당되지 않을 수 있으므로), OfficeAssignment 엔터티에는 null 비허용 `Instructor` 탐색 속성이 있습니다(사무실 할당은 강사 없이는 존재할 수 없으므로, `InstructorID`는 null을 허용하지 않음). 강사 엔터티에 관련된 OfficeAssignment 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖게 됩니다.

강사 탐색 속성에 `[Required]` 특성을 배치하여 관련된 강사가 반드시 있도록 지정할 수도 있지만, `InstructorID` 외래 키(이 테이블에 대한 키이기도 함)가 null을 허용하지 않으므로 이를 수행하지 않아도 됩니다.

## <a name="modify-course-entity"></a>Course 엔터티 수정

![강좌 엔터티](complex-data-model/_static/course-entity.png)

*Models/Course.cs* 에서 앞서 추가한 코드를 다음 코드로 바꿉니다. 변경 내용은 강조 표시되어 있습니다.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

강좌 엔터티에는 관련된 부서 엔터티를 가리키는 외래 키 속성 `DepartmentID`가 있으며, 여기에는 `Department` 탐색 속성이 있습니다.

Entity Framework는 관련된 엔터티에 대한 탐색 속성이 있는 경우 사용자가 외래 키 속성을 데이터 모델에 추가하지 않아도 됩니다.  EF에서 자동으로 필요할 때마다 데이터베이스에 외래 키를 생성하고, 이에 대한 [그림자 속성](/ef/core/modeling/shadow-properties)을 만듭니다. 하지만 데이터 모델에 외래 키가 있으면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다. 예를 들어, 편집할 강좌 엔터티를 페치하는 경우 이를 로드하지 않으면 부서 엔터티가 null이므로, 강좌 엔터티를 업데이트할 때 먼저 부서 엔터티를 페치해야 합니다. 외래 키 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 부서 엔터티를 페치할 필요가 없습니다.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 특성

`CourseID` 속성의 `None` 매개 변수가 있는 `DatabaseGenerated` 특성은 기본 키 값이 데이터베이스에서 생성되지 않고 사용자에 의해 제공되도록 지정합니다.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

기본적으로 Entity Framework은 기본 키 값이 데이터베이스에서 생성된다고 가정합니다. 이는 대부분의 시나리오에서 사용자가 원하는 것입니다. 그러나 강좌 엔터티의 경우 한 부서에 대해 1000개 시리즈, 다른 부서에 대한 2000개 시리즈 등과 같은 사용자 지정 강좌 수를 사용하게 됩니다.

또한 행이 생성되거나 업데이트된 날짜를 기록하는 데 데이터베이스 열이 사용되는 경우에 `DatabaseGenerated` 특성을 사용하여 기본 값을 생성할 수 있습니다.  자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

강좌 엔터티의 외래 키 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.

강좌는 한 부서에 할당되므로, 위에서 언급한 이유로 `DepartmentID` 외래 키와 `Department` 탐색 속성이 있습니다.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다(형식 `CourseAssignment`는 [나중에](#many-to-many-relationships) 설명).

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Department 엔터티 만들기

![부서 엔터티](complex-data-model/_static/department-entity.png)

다음 코드로 *Models/Department.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>열 특성

앞서 `Column` 특성을 사용하여 열 이름 매핑을 변경했습니다. 부서 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용되었으므로 열은 데이터베이스의 SQL Server 금액 형식을 사용하여 정의됩니다.

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Entity Framework이 속성에 대해 사용자가 정의한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택하기 때문에 열 매핑은 일반적으로 필요하지 않습니다. CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다. 하지만 이 경우 열에 통화 금액이 포함되므로 금액 데이터 형식이 더 적합합니다.

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

외래 키 및 탐색 속성은 다음과 같은 관계를 반영합니다.

부서는 관리자가 있을 수도 있고 없을 수도 있으며, 관리자는 항상 강사입니다. 따라서 `InstructorID` 속성은 강사 엔터티에 외래 키로 포함되며, 속성이 null 허용임을 표시하도록 `int` 형식이 지정된 후 물음표가 추가됩니다. 탐색 속성이 `Administrator`로 명명되나, 강사 엔터티를 포함합니다.

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> 규칙에 따라 Entity Framework는 null 비허용 외래 키 및 다대다 관계에 대한 하위 삭제를 사용하도록 허용합니다. 이는 마이그레이션을 추가하려고 하면 예외가 발생하는 순환 하위 삭제 규칙으로 이어질 수 있습니다. 예를 들어, Department.InstructorID 속성을 nullable로 정의하지 않은 경우 EF는 강사를 삭제할 때 부서를 삭제하도록 계단식 삭제 규칙을 구성합니다. 이는 원하는 결과가 아닙니다. 비즈니스 규칙에 null 비허용인 `InstructorID` 속성이 필요한 경우, 다음 흐름 API 문을 사용하여 관계에서 하위 삭제를 사용하지 않도록 설정해야 합니다.
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Enrollment 엔터티 수정

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

*Models/Enrollment.cs* 에서 앞서 추가한 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>외래 키 및 탐색 속성

외래 키 속성 및 탐색 속성은 다음 관계를 반영합니다.

등록 레코드는 단일 강좌에 해당하므로 `CourseID` 외래 키 속성 및 `Course` 탐색 속성이 있습니다.

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

등록 레코드는 단일 학생에 해당하므로 `StudentID` 외래 키 속성 및 `Student` 탐색 속성이 있습니다.

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>다 대 다 관계

학생과 강좌 엔터티 간의 다대다 관계가 있으며, 등록 엔터티는 데이터베이스에서 *페이로드가 있는* 다대다 조인 테이블로 작동합니다. “페이로드가 있다”는 것은 등록 테이블에 조인된 테이블에 대한 외래 키 외에 추가 데이터가 포함되어 있다는 것을 의미합니다(이 경우 기본 키 및 Grade 속성).

다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다. (이 다이어그램은 EF 6.x에 대한 Entity Framework 파워 도구를 사용하여 생성되었습니다. 다이어그램 만들기는 자습서 내용에 해당하지 않으며 그림으로만 사용됩니다.)

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(*)가 있으며, 이는 일 대 다 관계를 나타냅니다.

등록 테이블에 등급 정보가 포함되지 않은 경우 두 개의 외래 키 CourseID 및 StudentID를 포함해야 합니다. 그런 경우에는 데이터베이스에서 페이로드 없는 다대다 조인 테이블(또는 순수 조인 테이블)일 수 있습니다. 강사 및 강좌 엔터티에 그러한 다대다 관계가 있으며, 다음 단계는 페이로드 없는 조인 테이블로 작동하는 엔터티 클래스를 만드는 것입니다.

(EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다. 자세한 내용은 [EF Core GitHub 리포지토리에서 논의](https://github.com/aspnet/EntityFramework/issues/1368)를 참조하세요.)

## <a name="the-courseassignment-entity"></a>CourseAssignment 엔터티

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

다음 코드로 *Models/CourseAssignment.cs* 를 만듭니다.

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>조인 엔터티 이름

조인 테이블은 강사-강좌 간 다대다 관계에 대한 데이터베이스에 필요하며, 엔터티 집합으로 표시되어야 합니다. 이름은 조인 엔터티 `EntityName1EntityName2`로 지정하는 것이 일반적입니다. 이 경우 `CourseInstructor`가 됩니다. 그러나 관계를 설명하는 이름을 선택하는 것이 좋습니다. 데이터 모델은 단순하게 시작되며 나중에 자주 페이로드를 가져오는 페이로드 없는 조인을 사용하여 증가됩니다. 설명이 포함된 엔터티 이름으로 시작하는 경우 나중에 이름을 변경할 필요가 없습니다. 이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다. 예를 들어 Books 및 Customers는 Ratings를 통해 연결될 수 있습니다. 이 관계의 경우 `CourseAssignment`가 `CourseInstructor`보다 더 낫습니다.

### <a name="composite-key"></a>복합 키

외래 키가 null을 허용하지 않고 고유하게 테이블의 각 행을 식별하기 때문에 별도 기본 키가 필요하지 않습니다. *InstructorID* 및 *CourseID* 속성은 복합 기본 키로 작동해야 합니다. EF에서 복합 기본 키를 식별하는 유일한 방법은 *흐름 API* 를 사용하는 것입니다(특성을 사용해서는 수행할 수 없음). 다음 섹션에 복합 기본 키를 구성하는 방법이 나와 있습니다.

복합 키는 한 강좌에 여러 행 및 한 강사에 여러 행을 가질 수 있는 반면, 동일한 강사 및 강좌에는 여러 행을 가질 수 없음을 확인합니다. `Enrollment` 조인 엔터티는 고유한 기본 키를 정의하므로 이러한 종류의 중복이 가능합니다. 그러한 중복을 방지하려면 외래 키 필드에서 고유한 인덱스를 추가하거나 `CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성할 수 있습니다. 자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.

## <a name="update-the-database-context"></a>데이터베이스 컨텍스트 업데이트

다음 강조 표시된 코드를 *Data/SchoolContext.cs* 파일에 추가합니다.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

이 코드는 새 엔터티를 추가하고 CourseAssignment 엔터티의 복합 기본 키를 구성합니다.

## <a name="about-a-fluent-api-alternative"></a>흐름 API 대안 정보

`DbContext` 클래스에서 `OnModelCreating` 메서드의 코드는 *흐름 API* 를 사용하여 EF 동작을 구성합니다. API는 종종 [EF Core 설명서](/ef/core/modeling/#use-fluent-api-to-configure-a-model)의 이 예제와 같이 일련의 메서드 호출을 단일 명령문으로 연결하여 사용되기 때문에 “흐름”이라고 부릅니다.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

이 자습서에서는 특성으로 작업할 수 없는 데이터베이스 매핑에 대해서만 흐름 API를 사용합니다. 그러나 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정하는 데 흐름 API를 사용할 수 있습니다. `MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다. 앞서 설명한 대로 `MinimumLength`는 스키마를 변경하지 않고, 클라이언트와 서버 쪽 유효성 검사만 적용합니다.

일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다. 원하는 경우 특성과 흐름 API를 섞을 수 있으며, 흐름 API를 사용해야만 수행할 수 있는 몇 가지 사용자 지정이 있습니다. 하지만 일반적으로 이러한 두 가지 방법 중 하나를 선택하여 가능한 한 일관되게 사용하는 것이 좋습니다. 둘 다 사용하는 경우 충돌이 있을 때마다 흐름 API가 특성을 재정의합니다.

특성 대 흐름 API의 비교에 관한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.

## <a name="entity-diagram-showing-relationships"></a>관계를 보여 주는 엔터티 다이어그램

다음 그림에는 Entity Framework 파워 도구가 완벽한 School 모델을 만드는 다이어그램이 나와 있습니다.

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

여기에서 일대다 관계 줄 외에도(1 ~ \*), 강사 및 OfficeAssignment 엔터티 간 일대영 또는 일 관계 줄(1 ~ 0..1)과 강사 및 부서 간 영 또는 일대다 관계 줄(0..1 ~ *)을 확인할 수 있습니다.

## <a name="seed-database-with-test-data"></a>테스트 데이터로 데이터베이스 시드

만든 새 엔터티에 대한 시드 데이터를 제공하기 위해 *Data/DbInitializer.cs* 파일의 코드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

첫 번째 자습서에서 보았듯이, 이 코드의 대부분은 단순히 새 엔터티 개체를 만들고 테스트를 위해 필요에 따라 속성에 샘플 데이터를 로드합니다. 어떻게 다대다 관계가 처리되는지 확인하세요. 코드는 `Enrollments` 및 `CourseAssignment` 조인 엔터티 집합에서 엔터티를 만들어서 관계를 생성합니다.

## <a name="add-a-migration"></a>마이그레이션 추가

변경 내용을 저장하고 프로젝트를 빌드합니다. 그런 다음, 프로젝트 폴더에서 명령 창을 열고 `migrations add` 명령을 입력합니다. (update-database 명령은 아직 실행하지 마십시오.)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

가능한 데이터 손실에 대한 경고가 표시됩니다.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

이 시점에서 `database update` 명령 실행을 시도하면(아직 수행하지 말 것) 다음 오류가 발생합니다.

> ALTER TABLE 문이 FOREIGN KEY 제약 조건 “FK_dbo.Course_dbo.Department_DepartmentID”와 충돌했습니다. 데이터베이스 “ContosoUniversity”, 테이블 “dbo.Department”, 열 ‘DepartmentID’에서 충돌이 발생합니다.

종종 기존 데이터와 마이그레이션을 실행하는 경우 외래 키 제약 조건을 만족하도록 데이터베이스에 스텁 데이터를 삽입해야 합니다. `Up` 메서드의 생성된 코드는 null 비허용 DepartmentID 외래 키를 강좌 테이블에 추가합니다. 코드를 실행할 때 강좌 테이블에 이미 행이 있는 경우 SQL Server에서 null일 수 없는 열에 배치하는 값을 알지 못하므로 `AddColumn` 작업이 실패합니다. 이 자습서의 경우 새 데이터베이스에서 마이그레이션을 실행하지만, 프로덕션 애플리케이션에서는 마이그레이션이 기존 데이터를 처리하도록 해야 합니다. 따라서 다음 지침에는 수행 방법의 예가 나와 있습니다.

기존 데이터로 이 마이그레이션을 수행하려면 새 열에 기본 값을 제공하도록 코드를 변경하고 “Temp”라는 이름의 스텁 부서를 만들어 기본 부서로 작동하도록 합니다. 결과적으로, `Up` 메서드를 실행한 후에 기존 강좌 행은 모두 “Temp” 부서에 연결됩니다.

* *{timestamp}_ComplexDataModel.cs* 파일을 엽니다.

* DepartmentID 열을 강좌 테이블에 추가하는 코드 줄을 주석으로 처리합니다.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* 부서 테이블을 만드는 코드 뒤에 다음 강조 표시된 코드를 추가합니다.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

프로덕션 애플리케이션에서 코드 또는 스크립트를 작성하여 부서 행을 추가하고 강좌 행을 새 부서 행에 연결합니다. 그런 다음, “Temp” 부서 또는 기본 값은 Course.DepartmentID 열에 필요하지 않습니다.

변경 내용을 저장하고 프로젝트를 빌드합니다.

## <a name="change-the-connection-string"></a>연결 문자열 변경

이제 새 엔터티에 대한 시드 데이터를 빈 데이터베이스에 추가하는 새 코드가 `DbInitializer` 클래스에 있습니다. EF가 비어 있는 새 데이터베이스를 만들도록 하려면 *appsettings.json* 의 연결 문자열에서 데이터베이스의 이름을 ContosoUniversity3 또는 사용 중인 컴퓨터에서 사용한 적 없는 다른 이름으로 변경합니다.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

변경 내용을 *appsettings.json* 에 저장합니다.

> [!NOTE]
> 데이터베이스 이름을 변경하는 대신, 데이터베이스를 삭제할 수 있습니다. **SSOX(SQL Server 개체 탐색기)** 또는 `database drop` CLI 명령을 사용합니다.
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>데이터베이스 업데이트

데이터베이스 이름을 변경했거나 데이터베이스를 삭제한 후 명령 창에서 `database update` 명령을 실행하여 마이그레이션을 수행합니다.

```dotnetcli
dotnet ef database update
```

`DbInitializer.Initialize` 메서드로 이어지는 앱을 실행하여 새 데이터베이스를 실행하고 채웁니다.

이전에 수행한 SSOX에서 데이터베이스를 열고, 만들었던 테이블을 모두 볼 수 있도록 **Tables** 노드를 확장합니다. (아직 이전 시점의 SSOX가 열려 있는 경우 **새로 고침** 단추를 클릭합니다.)

![SSOX에서의 테이블](complex-data-model/_static/ssox-tables.png)

앱을 실행하여 데이터베이스를 시드하는 이니셜라이저 코드를 트리거합니다.

**CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기** 를 선택하여 데이터가 있는지 확인합니다.

![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 데이터 모델 사용자 지정
> * Student 엔터티 변경
> * Instructor 엔터티 만들기
> * OfficeAssignment 엔터티 만들기
> * Course 엔터티 수정
> * Department 엔터티 만들기
> * Enrollment 엔터티 수정
> * 데이터베이스 컨텍스트 업데이트
> * 테스트 데이터로 데이터베이스 시드
> * 마이그레이션 추가
> * 연결 문자열 변경
> * 데이터베이스 업데이트

관련 데이터에 액세스하는 방법을 자세히 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [다음: 관련 데이터 액세스](read-related-data.md)
