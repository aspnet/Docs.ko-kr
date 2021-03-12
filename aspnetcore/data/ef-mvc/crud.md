---
title: '자습서: CRUD 기능 구현 - ASP.NET MVC 및 EF Core 사용'
description: 이 자습서에서는 MVC 스캐폴딩이 컨트롤러 및 보기에서 자동으로 만드는 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토하고 사용자 지정합니다.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
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
uid: data/ef-mvc/crud
ms.openlocfilehash: c3cfee107541a20a089809193d4bf7fa08730bd2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586399"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>자습서: CRUD 기능 구현 - ASP.NET MVC 및 EF Core 사용

이전 자습서에서 Entity Framework 및 SQL Server LocalDB를 사용하여 데이터를 저장하고 표시하는 MVC 애플리케이션을 만들었습니다. 이 자습서에서는 MVC 스캐폴딩이 컨트롤러 및 보기에서 자동으로 만드는 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토하고 사용자 지정합니다.

> [!NOTE]
> 컨트롤러와 데이터 액세스 계층 간에 추상화 계층을 만들기 위해 리포지토리 패턴을 구현하는 일반적인 사례입니다. 이러한 자습서를 간단하고 Entity Framework 자체를 사용하는 방법을 가르치는 데 초점을 두도록 유지하기 위해 리포지토리를 사용하지 않습니다. EF를 사용하는 리포지토리에 대한 자세한 내용은 [이 시리즈의 마지막 자습서](advanced.md)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 세부 정보 사용자 지정 페이지
> * 만들기 페이지 업데이트
> * 편집 페이지 업데이트
> * 삭제 페이지 업데이트
> * 데이터베이스 연결 닫기

## <a name="prerequisites"></a>사전 요구 사항

* [EF Core 및 ASP.NET Core MVC 시작](intro.md)

## <a name="customize-the-details-page"></a>세부 정보 사용자 지정 페이지

속성은 컬렉션을 보유하기 때문에 학생 인덱스 페이지에 대한 스캐폴드 코드는 `Enrollments` 속성을 제외합니다. **세부 정보** 페이지에서 HTML 테이블에 컬렉션의 콘텐츠를 표시합니다.

*Controllers/StudentsController.cs* 에서 세부 정보 보기에 대한 작업 메서드는 `SingleOrDefaultAsync` 메서드를 사용하여 단일 `Student` 엔터티를 검색합니다. `Include`를 호출하는 코드를 추가합니다. 다음 강조 표시된 코드에 표시된 것처럼 `ThenInclude` 및 `AsNoTracking` 메서드입니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

`Include` 및 `ThenInclude` 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내 `Enrollment.Course` 탐색 속성을 로드합니다.  [관련된 데이터 읽기](read-related-data.md) 자습서에서 이러한 메서드에 대해 자세히 알아봅니다.

`AsNoTracking` 메서드는 반환된 엔터티가 현재 컨텍스트의 수명에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다. 이 자습서의 끝에서 `AsNoTracking`에 대해 자세히 알아봅니다.

### <a name="route-data"></a>경로 데이터

`Details` 메서드에 전달되는 키 값은 *경로 데이터* 에서 제공됩니다. 경로 데이터는 모델 바인더가 URL의 세그먼트에서 찾은 데이터입니다. 예를 들어 기본 경로는 컨트롤러, 작업 및 ID 세그먼트를 지정합니다.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

다음 URL에서 기본 경로는 강사를 컨트롤러로, 인덱스를 작업으로, 1을 ID로 매핑합니다. 해당 내용은 경로 데이터 값입니다.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

URL의 마지막 부분("?courseID=2021")은 쿼리 문자열 값입니다. 또한 모델 바인더는 쿼리 문자열 값으로 전달하는 경우 ID 값을 `Index` 메서드 `id` 매개 변수에 전달합니다.

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

인덱스 페이지에서 하이퍼링크 URL은 Razor 뷰의 태그 도우미 문에 의해 생성됩니다. 다음 Razor 코드에서 `id` 매개 변수는 기본 경로와 일치하므로 `id`는 경로 데이터에 추가됩니다.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

`item.ID`가 6일 때 다음 HTML을 생성합니다.

```html
<a href="/Students/Edit/6">Edit</a>
```

다음 Razor 코드에서 `studentID`는 기본 경로의 매개 변수와 일치하지 않으므로 쿼리 문자열로 추가됩니다.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

`item.ID`가 6일 때 다음 HTML을 생성합니다.

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

### <a name="add-enrollments-to-the-details-view"></a>세부 정보 보기에 등록 추가

*Views/Students/Details.cshtml* 을 엽니다. 각 필드는 다음 예제와 같이 `DisplayNameFor` 및 `DisplayFor` 도우미를 사용하여 표시됩니다.

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

마지막 필드 뒤와 닫기 `</dl>` 태그 바로 앞에 등록의 목록을 표시하도록 다음 코드를 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

코드를 붙여넣은 후 코드 들여쓰기가 잘못된 경우 CTRL-K-D를 눌러 수정합니다.

이 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다. 각 등록의 경우 강좌 제목과 등급을 표시합니다. 강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.

앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다. 선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.

![학생 세부 정보 페이지](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>만들기 페이지 업데이트

*StudentsController.cs* 에서 try-catch 블록을 추가하고 `Bind` 특성에서 ID를 제거하여 HttpPost `Create` 메서드를 수정합니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

이 코드는 ASP.NET Core MVC 모델 바인더에서 만든 학생 엔터티를 학생 엔터티 설정에 추가한 다음, 데이터베이스에 변경 내용을 저장합니다. (모델 바인더는 양식에서 전송한 데이터를 쉽게 사용할 수 있도록 하는 ASP.NET Core MVC 기능을 참조합니다. 모델 바인더는 게시된 양식 값을 CLR 형식으로 변환하고 매개 변수의 동작 메서드에 전달합니다. 이 경우 모델 바인더는 양식 컬렉션에서 속성 값을 사용하여 학생 엔터티를 인스턴스화합니다.)

ID는 행이 삽입될 때 SQL 서버가 자동으로 설정하는 기본 키 값이므로 `Bind` 특성에서 `ID`를 제거했습니다. 사용자의 입력은 ID 값을 설정하지 않습니다.

`Bind` 특성 이외에 try-catch 블록은 스캐폴드 코드에 대해 만든 유일한 변경 내용입니다. `DbUpdateException`에서 파생되는 예외가 변경 내용이 저장되는 동안 발견되는 경우 일반 오류 메시지가 표시됩니다. `DbUpdateException` 예외는 경우에 따라 프로그래밍 오류가 아니라 애플리케이션에 대한 외부적인 문제로 발생하므로 사용자는 다시 시도하는 것이 좋습니다. 이 샘플에서 구현되지 않지만 프로덕션 품질 애플리케이션은 예외를 기록합니다. 자세한 내용은 [모니터링 및 원격 분석(Azure로 실제 클라우드 앱 빌드)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)에서 **정보에 대한 로그** 섹션을 참조하세요.

`ValidateAntiForgeryToken` 특성은 CSRF(사이트 간 요청 위조) 공격을 방지하도록 돕습니다. 토큰은 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)에 의한 보기로 자동으로 주입되며 사용자에 의해 양식이 제출될 때 포함됩니다. 토큰은 `ValidateAntiForgeryToken` 특성으로 유효성이 검사됩니다. 자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>초과 게시에 대한 보안 정보

스캐폴드된 코드가 `Create` 메서드에 포함하는 `Bind` 특성은 만들기 시나리오에서 초과 게시를 방지하는 한 가지 방법입니다. 예를 들어 학생 엔터티가 이 웹 페이지에서 설정하는 것을 원하지 않는 `Secret` 속성을 포함한다고 가정합니다.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

웹 페이지에 `Secret` 필드가 없는 경우에도 해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다. 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하는 `Bind` 특성 없이 모델 바인더는 해당 `Secret` 양식 값을 선택하고 학생 엔터티 인스턴스를 만드는 데 사용합니다. 그런 다음, 해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 데이터베이스에서 업데이트됩니다. 다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.

![암호 필드를 추가하는 Fiddler](crud/_static/fiddler.png)

값 "OverPost"는 웹 페이지가 속성을 설정할 수 있도록 의도하지 않았지만 삽입된 행의 `Secret` 속성에 성공적으로 추가됩니다.

먼저 데이터베이스에서 엔터티를 읽은 다음, `TryUpdateModel`을 호출하고, 허용되는 명시적 속성 목록에 전달하여 편집 시나리오에서 초과 게시를 방지할 수 있습니다. 이러한 자습서에서 사용되는 방법입니다.

대부분의 개발자가 선호하는 초과 게시를 방지하는 다른 방법은 모델 바인딩으로 엔터티 클래스를 사용하지 않고 보기 모델을 사용하는 것입니다. 보기 모델에서 업데이트하려는 속성만 포함합니다. MVC 모델 바인더가 완료되면 필요에 따라 AutoMapper와 같은 도구를 사용하여 엔터티 인스턴스에 보기 모델 속성을 복사합니다. 엔터티 인스턴스의 `_context.Entry`를 사용하여 해당 상태를 `Unchanged`로 설정한 다음, 보기 모델에 포함된 각 엔터티 속성에서 `Property("PropertyName").IsModified`를 true로 설정합니다. 이 방법은 편집 및 만들기 시나리오에서 모두 작동합니다.

### <a name="test-the-create-page"></a>만들기 페이지 테스트

*Views/Students/Create.cshtml* 에서 코드는 각 필드에 대해 `label`, `input` 및 `span`(유효성 검사 메시지의 경우) 태그 도우미를 사용합니다.

앱을 실행하고, **학생** 탭을 선택하고, **새로 만들기** 를 클릭합니다.

이름 및 날짜를 입력합니다. 브라우저가 그렇게 수행하도록 하는 경우 잘못된 날짜를 입력합니다. (일부 브라우저는 강제로 날짜 선택을 사용하도록 합니다.) 그런 다음, **만들기** 를 클릭하여 오류 메시지를 확인합니다.

![날자 유효성 검사 오류](crud/_static/date-error.png)

이는 기본적으로 얻는 서버 쪽 유효성 검사입니다. 이후의 자습서에서 클라이언트 쪽 유효성 검사에 대한 코드를 생성하는 특성을 추가하는 방법도 알아봅니다. 다음 강조 표시된 코드는 `Create` 메서드에서 모델 유효성 검사를 보여 줍니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

유효한 값으로 날짜를 변경하고 **만들기** 를 클릭하여 **인덱스** 페이지에 표시되는 새 학생을 봅니다.

## <a name="update-the-edit-page"></a>편집 페이지 업데이트

*StudentController.cs* 에서 HttpGet `Edit` 메서드(`HttpPost` 특성이 없는 것)는 `SingleOrDefaultAsync` 메서드를 사용하여 `Details` 메서드에서 본 것처럼 선택한 학생 엔터티를 검색합니다. 이 메서드를 변경할 필요가 없습니다.

### <a name="recommended-httppost-edit-code-read-and-update"></a>권장되는 HttpPost 편집 코드: 읽기 및 업데이트

HttpPost 편집 작업 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

이러한 변경 내용은 초과 게시를 방지하기 위해 보안 모범 사례를 구현합니다. 스캐폴더는 `Bind` 특성을 생성했고 모델 바인더에서 만든 엔터티를 `Modified` 플래그로 설정된 엔터티에 추가했습니다. `Bind` 특성은 `Include` 매개 변수에 나열되지 않은 필드에서 기존의 모든 데이터를 지우므로 해당 코드는 대부분의 시나리오에 권장되지 않습니다.

새 코드는 기존 엔터티를 읽고 `TryUpdateModel`을 호출하여 [게시된 양식 데이터에서 사용자 입력에 따라](xref:mvc/models/model-binding) 검색된 엔터티에서 필드를 업데이트합니다. Entity Framework의 자동 변경 내용 추적은 양식 입력에 의해 변경된 필드에서 `Modified` 플래그를 설정합니다. `SaveChanges` 메서드가 호출되는 경우 Entity Framework는 SQL 문을 만들어 데이터베이스 행을 업데이트합니다. 동시성 충돌은 무시되고 사용자가 업데이트한 테이블 열만 데이터베이스에서 업데이트됩니다. (이후의 자습서에서는 동시성 충돌을 처리하는 방법을 보여 줍니다.)

초과 게시를 방지하는 모범 사례로 **편집** 페이지에서 업데이트 가능하도록 하려는 필드가 `TryUpdateModel` 매개 변수에서 선언됩니다. (매개 변수 목록에서 필드 목록 앞의 빈 문자열은 양식 필드 이름으로 사용하는 접두사에 대한 것입니다.) 현재 보호하는 추가 필드가 없지만 모델 바인더에서 바인딩하길 원하는 필드를 나열하는 것은 향후에 데이터 모델에 필드를 추가하는지 확인하며, 여기에서 명시적으로 추가할 때까지 자동으로 보호됩니다.

이러한 변경 내용의 결과로 HttpPost `Edit` 메서드의 메서드 서명은 HttpGet `Edit` 메서드와 동일하므로 메서드 `EditPost`의 이름을 변경했습니다.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>대체 HttpPost 편집 코드: 만들기 및 연결

권장된 HttpPost 편집 코드는 변경된 열만 업데이트되도록 하며 모델 바인딩에 포함되지 않기를 원하는 속성에 데이터를 유지합니다. 그러나 읽기 우선 접근 방식에는 추가 데이터베이스 읽기가 필요하며 동시성 충돌 처리에 대한 더 복잡한 코드가 발생할 수 있습니다. 대안은 모델 바인더에서 만든 엔터티를 EF 컨텍스트에 연결하고 수정된 것으로 표시하는 것입니다. (이 코드로 프로젝트를 업데이트하지 마십시오. 선택적 접근 방식을 설명하기 위해 표시되었습니다.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

웹 페이지 UI가 엔터티에 모든 필드를 포함하고 그 중 하나를 업데이트할 수 있는 경우 이 방법을 사용할 수 있습니다.

스캐폴드된 코드는 만들기 및 연결 방법을 사용하지만 `DbUpdateConcurrencyException` 예외를 catch하고 404 오류 코드를 반환합니다.  표시된 예제는 모든 데이터베이스 업데이트 예외를 catch하고 오류 메시지를 표시합니다.

### <a name="entity-states"></a>엔터티 상태

데이터베이스 컨텍스트는 메모리의 엔터티가 데이터베이스의 해당 열과 동기화 상태인지 여부의 추적을 유지하고 이 정보는 `SaveChanges` 메서드를 호출할 때 발생하는 작업을 결정합니다. 예를 들어 새 엔터티를 `Add` 메서드에 전달하는 경우 해당 엔터티의 상태가 `Added`로 설정됩니다. 그런 다음, `SaveChanges` 메서드를 호출하면 데이터베이스 컨텍스트는 SQL INSERT 명령을 실행합니다.

엔터티는 다음 상태 중 하나일 수 있습니다.

* `Added`. 엔터티가 아직 데이터베이스에 존재하지 않습니다. `SaveChanges` 메서드가 INSERT 문을 발급합니다.

* `Unchanged`. `SaveChanges` 메서드에서 이 엔터티로 아무 작업도 수행할 필요가 없습니다. 데이터베이스에서 엔터티를 읽을 때 엔터티는 이 상태로 시작합니다.

* `Modified`. 일부 또는 모든 엔터티의 속성 값이 수정되었습니다. `SaveChanges` 메서드는 UPDATE 문을 발급합니다.

* `Deleted`. 엔터티가 삭제되도록 표시되었습니다. `SaveChanges` 메서드는 DELETE 문을 발급합니다.

* `Detached`. 엔터티가 데이터베이스 컨텍스트에 의해 추적되지 않습니다.

데스크톱 애플리케이션에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다. 엔터티를 읽고 해당 속성 값의 일부를 변경합니다. 이렇게 하면 해당 엔터티 상태가 자동으로 `Modified`로 변경됩니다. 그런 다음, `SaveChanges`를 호출하면 Entity Framework는 변경한 실제 속성만 업데이트하는 SQL UPDATE 문을 생성합니다.

웹앱에서는 페이지가 렌더링된 후 처음에 엔터티를 읽고 편집될 해당 데이터를 표시하는 `DbContext`가 삭제됩니다. HttpPost `Edit` 작업 메스드가 호출되면 새 웹 요청이 만들어지고 `DbContext`의 새 인스턴스를 갖습니다. 새로운 컨텍스트의 엔터티를 다시 읽는 경우 데스크톱 처리를 시뮬레이트합니다.

하지만 추가 읽기 작업을 수행하지 않으려는 경우 모델 바인더를 통해 생성된 엔터티 개체를 사용해야 합니다.  이 작업을 수행하는 가장 간단한 방법은 앞에 표시된 대체 HttpPost 편집 코드에서 수행되는 것처럼 엔터티 상태를 Modified로 설정하는 것입니다. 그런 다음, `SaveChanges`를 호출하면 Entity Framework는 컨텍스트에서 변경한 속성을 알 수 없기 때문에 데이터베이스 행의 모든 열을 업데이트합니다.

읽기 우선 접근 방식을 피하길 원하지만 SQL UPDATE 문이 사용자가 실제로 변경한 필드만 업데이트하기를 원하는 경우 코드는 더 복잡합니다. HttpPost `Edit` 메서드가 호출될 때 사용할 수 있도록 특정한 방식으로(예: 숨겨진 필드를 사용하여) 원래 값을 저장해야 합니다. 그러면 원래 값을 사용하여 학생 엔터티를 만들고, 엔터티의 해당 원래 값으로 `Attach` 메서드를 호출하고, 엔터티의 값을 새 값으로 업데이트한 다음, `SaveChanges`를 호출할 수 있습니다.

### <a name="test-the-edit-page"></a>편집 페이지 테스트

앱을 실행하고, **학생** 탭을 선택한 다음, **편집** 하이퍼링크를 클릭합니다.

![학생 편집 페이지](crud/_static/student-edit.png)

데이터의 일부를 변경하고 **저장** 을 클릭합니다. **인덱스** 페이지가 열리고 변경된 데이터가 표시됩니다.

## <a name="update-the-delete-page"></a>삭제 페이지 업데이트

*StudentController.cs* 에서 HttpGet `Delete` 메서드에 대한 템플릿 코드는 `SingleOrDefaultAsync` 메서드를 사용하여 세부 정보 및 편집 메서드에서 본 것처럼 선택한 학생 엔터티를 검색합니다. 그러나 `SaveChanges`에 대한 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현하려면 이 메서드 및 해당 보기에 일부 기능을 추가합니다.

업데이트 및 만들기 작업에 대해 본 것과 같이 삭제 작업에는 두 개의 작업 메서드가 필요합니다. GET 요청에 대한 응답에서 호출되는 메서드는 사용자에게 삭제 작업을 승인 또는 취소하는 기회를 제공하는 보기를 표시합니다. 사용자가 승인하는 경우 POST 요청이 생성됩니다. 이 경우 HttpPost `Delete` 메서드가 호출되고 해당 메서드는 삭제 작업을 실제로 수행합니다.

try-catch 블록을 HttpPost `Delete` 메서드에 추가하여 데이터베이스가 업데이트될 때 발생할 수 있는 오류를 처리합니다. 오류가 발생하는 경우 HttpPost Delete 메서드는 오류가 발생했음을 나타내는 매개 변수를 전달하여 HttpGet Delete 메서드를 호출합니다. 그런 다음, HttpGet Delete 메서드는 사용자에게 취소 또는 다시 시도하는 기회를 제공하는 오류 메시지와 함께 확인 페이지를 다시 표시합니다.

HttpGet `Delete` 작업 메서드를 오류 보고를 관리하는 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

이 코드는 변경 내용 저장에 실패한 후 메서드가 호출되었는지 여부를 나타내는 선택적 매개 변수를 허용합니다. HttpGet `Delete` 메서드가 이전 실패 없이 호출되는 경우 이 매개 변수는 false입니다. 데이터베이스 업데이트 오류에 대한 응답에서 HttpPost `Delete` 메서드로 호출되는 경우 매개 변수는 true이고 오류 메시지가 보기에 전달됩니다.

### <a name="the-read-first-approach-to-httppost-delete"></a>HttpPost Delete에 대한 읽기 우선 방법

HttpPost `Delete` 작업 메서드(`DeleteConfirmed`라는)를 실제 삭제 작업을 수행하고 모든 데이터베이스 업데이트 오류를 catch하는 다음 코드로 바꿉니다.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

이 코드는 선택한 엔터티를 검색한 다음, `Remove` 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다. `SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>HttpPost Delete에 대한 만들기 및 연결 방법

대규모 애플리케이션의 성능 향상이 우선 순위인 경우 기본 키 값만을 사용하여 학생 엔터티를 인스턴스화한 다음, 엔터티 상태를 `Deleted`로 설정하여 불필요한 SQL 쿼리를 피할 수 있습니다. Entity Framework에서 엔터티를 삭제하기 위해 필요한 모든 것입니다. (프로젝트에 이 코드를 배치하지 마십시오. 대안을 설명하기 위한 것입니다.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

엔터티에 삭제되어야 하는 관련된 데이터가 있는 경우 계단식 삭제가 데이터베이스에 구성되어 있는지 확인합니다. 엔터티 삭제에 대한 이 접근 방식을 사용하여 EF는 삭제될 관련된 엔터티가 있다는 것을 모를 수도 있습니다.

### <a name="update-the-delete-view"></a>삭제 보기 업데이트

*Views/Student/Delete.cshtml* 에서 다음 예제와 같이 h2 제목과 h3 제목 사이에 오류 메시지를 추가합니다.

[!code-cshtml[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

앱을 실행하고, **학생** 탭을 선택하고, **삭제** 하이퍼링크를 클릭합니다.

![삭제 확인 페이지](crud/_static/student-delete.png)

**삭제** 를 클릭합니다. 삭제된 학생 없이 인덱스 페이지가 표시됩니다. (동시성 자습서의 작업에서 오류 처리 코드의 예를 볼 수 있습니다.)

## <a name="close-database-connections"></a>데이터베이스 연결 닫기

데이터베이스 연결이 유지하는 리소스를 해제하려면 컨텍스트 인스턴스는 작업을 완료했을 때 가능한 빨리 삭제되어야 합니다. ASP.NET Core 기본 제공 [종속성 주입](../../fundamentals/dependency-injection.md)은 해당 작업을 담당합니다.

*Startup.cs* 에서 [AddDbContext 확장 메서드](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs)를 호출하여 ASP.NET Core DI 컨테이너에서 `DbContext` 클래스를 프로비전합니다. 해당 메서드는 서비스 수명을 기본적으로 `Scoped`로 설정합니다. `Scoped`는 웹 요청 수명과 일치하는 컨텍스트 개체 수명을 의미하며, `Dispose` 메서드는 웹 요청이 끝날 때 자동으로 호출됩니다.

## <a name="handle-transactions"></a>트랜잭션 처리

기본적으로 Entity Framework는 트랜잭션을 암시적으로 구현합니다. 여러 행 또는 테이블을 변경한 다음, `SaveChanges`를 호출하는 시나리오에서 Entity Framework는 모든 변경 내용이 성공했는지 또는 모두 실패했는지를 자동으로 확인합니다. 일부 변경 내용이 먼저 완료된 다음, 오류가 발생하는 경우 해당 변경 내용이 자동으로 롤백됩니다. 더 많은 컨트롤이 필요한 시나리오의 경우, 예를 들어 트랜잭션의 Entity Framework 밖에서 수행한 작업을 포함하려는 경우 [트랜잭션](/ef/core/saving/transactions)을 참조하세요.

## <a name="no-tracking-queries"></a>비 추적 쿼리

데이터베이스 컨텍스트가 테이블 행을 검색하고 해당 내용을 나타내는 엔터티 개체를 만드는 경우 기본적으로 메모리의 엔터티가 데이터베이스의 해당 내용과 동기화 상태인지 여부의 추적을 유지합니다. 메모리의 데이터는 캐시의 역할을 하고 엔터티를 업데이트할 때 사용됩니다. 컨텍스트 인스턴스는 일반적으로 수명이 짧으며(각 요청에 대해 새 것이 만들어지고 삭제됨) 엔터티를 읽는 컨텍스트는 일반적으로 해당 엔터티가 다시 사용되기 전에 삭제되므로 이 캐싱은 웹 애플리케이션에 종종 필요하지 않습니다.

`AsNoTracking` 메서드를 호출하여 메모리에서 엔터티 개체의 추적을 해제할 수 있습니다. 이러한 작업을 수행할 수 있는 일반적인 시나리오는 다음을 포함합니다.

* 컨텍스트 수명 동안 엔터티를 업데이트할 필요가 없으며 EF에서 [별도 쿼리에 의해 검색된 엔터티가 포함된 탐색 속성을 자동으로 로드](read-related-data.md)하도록 할 필요가 없습니다. 이러한 조건은 자주 컨트롤러의 HttpGet 작업 메서드에서 충족됩니다.

* 많은 양의 데이터를 검색하는 쿼리를 실행하며 반환된 데이터의 작은 부분만 업데이트됩니다. 큰 쿼리에 대한 추적을 해제하고 업데이트되어야 하는 몇 가지 엔터티에 대한 쿼리를 나중에 실행하는 것이 보다 효율적일 수 있습니다.

* 업데이트하기 위해 엔터티를 연결하려고 하지만 이전에 다른 목적을 위해 동일한 엔터티를 검색했습니다. 엔터티는 데이터베이스 컨텍스트에서 이미 추적 중이므로 변경하려는 엔터티를 연결할 수 없습니다. 이 상황을 처리하는 한 가지 방법은 이전 쿼리에서 `AsNoTracking`을 호출하는 것입니다.

자세한 내용은 [추적과 비추적 비교](/ef/core/querying/tracking)를 참조하세요.

## <a name="get-the-code"></a>코드 가져오기

[완성된 애플리케이션을 다운로드하거나 확인합니다.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행합니다.

> [!div class="checklist"]
> * 세부 정보 페이지 사용자 지정
> * 만들기 페이지 업데이트
> * 편집 페이지 업데이트
> * 삭제 페이지 업데이트
> * 데이터베이스 연결 닫기

정렬, 필터링 및 페이징을 추가하여 **인덱스** 페이지의 기능을 확장하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [다음: 정렬, 필터링 및 페이징](sort-filter-page.md)
