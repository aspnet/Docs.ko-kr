---
title: 8부. ASP.NET Core Razor Page에 유효성 검사 추가
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 8부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 991a0f29c0edc5a220dfde69bd22dc4ed758394d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060731"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a>8부. ASP.NET Core Razor Page에 유효성 검사 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서 유효성 검사 논리는 `Movie` 모델에 추가됩니다. 유효성 검사 규칙은 사용자가 동영상을 만들거나 편집할 때 언제든지 적용됩니다.

## <a name="validation"></a>유효성 검사

소프트웨어 개발의 주요 개념은 [반복 금지](https://wikipedia.org/wiki/Don%27t_repeat_yourself)라고 합니다. Razor Pages는 기능이 한 번 정의된 곳의 개발을 원활하게 하고 앱 전체에서 반영됩니다. DRY는 다음 작업에 도움이 됩니다.

* 앱에서 코드의 양 감소
* 코드에 오류 감소 및 쉽게 테스트 및 유지 관리

Razor Pages에서 제공하는 유효성 검사 지원 및 Entity Framework는 반복 금지 원칙의 좋은 예제입니다. 유효성 검사 규칙은 한 위치(모델 클래스에서)에서 선언적으로 지정되고 규칙은 앱의 모든 위치에 적용됩니다.

## <a name="add-validation-rules-to-the-movie-model"></a>영화 모델에 유효성 검사 규칙 추가

DataAnnotations 네임스페이스는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 모음을 제공합니다. 또한 DataAnnotations는 서식 지정을 돕고 아무런 유효성 검사도 제공하지 않는 `DataType` 같은 서식 지정 특성도 포함하고 있습니다.

기본 제공되는 `Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 수정합니다.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

유효성 검사 특성은 해당 특성이 적용되는 모델 속성에 시행하려는 동작을 지정합니다

* `Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 함을 지정합니다. 그러나 사용자가 이 유효성 검사를 만족시키기 위해 공백을 입력하는 것까지 막을 수는 없습니다.
* `RegularExpression` 특성은 입력할 수 있는 문자를 제한하는 데 사용됩니다. 이전 코드에서 “Genre”는 다음 조건을 충족해야 합니다.

  * 문자만 사용해야 합니다.
  * 첫 번째 문자는 대문자여야 합니다 공백, 숫자 및 특수 문자는 허용되지 않습니다.

* `RegularExpression` “Rating”은 다음 조건을 충족해야 합니다.

  * 첫 번째 문자는 대문자여야 합니다.
  * 이어지는 자리에서는 특수 문자와 숫자가 허용됩니다. “PG-13”은 등급에서는 유효하지만 “Genre”에서는 허용되지 않습니다.

* `Range` 특성은 지정한 범위 내로 값을 제한합니다.
* `StringLength` 특성을 사용하면 문자열 속성의 최대 길이와, 그리고 필요에 따라 최소 길이를 설정할 수 있습니다.
* 값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필수적이며 `[Required]` 특성이 필요하지 않습니다.

ASP.NET Core가 자동으로 적용하는 유효성 검사 규칙을 사용하면 앱을 더욱 강력하게 만들 수 있습니다. 또한 무언가의 유효성 검사를 잊거나, 실수로 데이터베이스에 불량 데이터가 들어가지 않도록 할 수 있습니다.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Razor Pages에서 유효성 검사 오류 UI

앱을 실행하고 Pages/Movies로 이동합니다.

**새로 만들기** 링크를 선택합니다. 일부 잘못된 값으로 양식을 완성합니다. jQuery 클라이언트 쪽 유효성 검사에서 오류를 발견하면 오류 메시지를 표시합니다.

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

양식에서 잘못된 값을 포함하는 각 필드에 유효성 검사 오류 메시지를 자동으로 렌더링하는 방법을 확인합니다. 오류는 클라이언트 쪽(JavaScript 및 jQuery 사용) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두 적용됩니다.

중요한 이점은 만들기 또는 편집 페이지에서 코드 변경이 필요하지 **않았다는** 점입니다. DataAnnotations가 모델에 적용된 후 유효성 검사 UI가 활성화되었습니다. 이 자습서에서 만든 Razor Pages는 자동으로 유효성 검사 규칙을 선택했습니다(`Movie` 모델 클래스의 속성에서 유효성 검사 특성 사용). 편집 페이지를 사용하는 테스트 유효성 검사는 동일한 유효성 검사가 적용됩니다.

양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 게시되지 않습니다. 양식 데이터가 다음 방법 중 하나 이상으로 게시되지 않았는지 확인합니다.

* `OnPostAsync` 메서드에 중단점을 배치합니다. 양식을 제출합니다( **만들기** 또는 **저장** 선택). 중단점은 적중되지 않습니다.
* [Fiddler 도구](https://www.telerik.com/fiddler)를 사용합니다.
* 브라우저 개발자 도구를 사용하여 네트워크 트래픽을 모니터링합니다.

### <a name="server-side-validation"></a>서버 쪽 유효성 검사

브라우저에서 JavaScript가 비활성화된 경우 오류가 있는 양식 제출에서 서버에 게시합니다.

선택 가능한 서버 쪽 유효성 검사 테스트:

* 브라우저에서 JavaScript를 비활성화합니다. 브라우저의 개발자 도구를 사용하여 JavaScript를 사용하지 않도록 설정할 수 있습니다. 브라우저에서 JavaScript를 비활성화할 수 없는 경우 다른 브라우저를 시도하세요.
* 만들기 또는 편집 페이지의 `OnPostAsync` 메서드에서 중단점을 설정합니다.
* 잘못된 데이터가 포함된 양식을 제출합니다.
* 모델 상태가 유효하지 않은 것을 확인합니다.

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
또는 [서버에서 클라이언트 쪽 유효성 검사를 사용하지 않도록 설정](xref:mvc/models/validation#disable-client-side-validation)할 수 있습니다.

다음 코드는 자습서의 앞부분에서 스캐폴드한 *Create.cshtml* 페이지의 일부를 보여 줍니다. 만들기 및 편집 페이지에서 초기 양식을 표시하고 오류가 발생한 경우 양식을 다시 표시하기 위해 사용됩니다.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다. [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다. 자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.

만들기 및 편집 페이지에 유효성 검사 규칙이 없습니다. 유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다. 이러한 유효성 검사 규칙은 `Movie` 모델을 편집하는 Razor Pages에 자동으로 적용됩니다.

유효성 검사 논리를 변경해야 하는 경우 모델에서만 수행됩니다. 유효성 검사는 애플리케이션에 걸쳐 일관되게 적용됩니다(유효성 검사 논리는 한 위치에서 정의됨). 한 위치의 유효성 검사를 통해 코드를 깨끗이 유지하고 쉽게 유지 관리하고 업데이트할 수 있습니다.

## <a name="using-datatype-attributes"></a>데이터 형식 특성 사용

`Movie` 클래스를 검사합니다. `System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성 모음 외에도 서식 특성을 제공합니다. `DataType` 특성은 `ReleaseDate` 및 `Price` 속성에 적용됩니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` 특성은 데이터의 서식을 지정하도록 뷰 엔진에 대한 힌트만을 제공합니다(그리고 URL에 대한 `<a>` 및 전자 메일에 대한 `<a href="mailto:EmailAddress.com">`과 같은 특성 제공). `RegularExpression` 특성을 사용하여 데이터 형식의 유효성을 검사합니다. `DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다. `DataType` 특성은 유효성 검사 특성이 아닙니다. 샘플 애플리케이션에서 날짜는 시간 없이 표시됩니다.

`DataType` 열거형은 날짜, 시간, 전화 번호, 통화, 전자 메일 주소 등과 같은 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 애플리케이션에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다. 예를 들어 `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들 수 있습니다. HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기가 제공될 수 있습니다. `DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다. `DataType` 특성은 유효성 검사를 전혀 제공하지 **않습니다**

`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다. 기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.

Entity Framework Core가 `Price`를 데이터베이스의 통화에 올바르게 매핑하기 위해서는 `[Column(TypeName = "decimal(18, 2)")]` 데이터 주석이 필요합니다. 자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.

`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 설정은 값이 편집을 위해 표시될 때 서식 지정이 적용되어야 함을 지정합니다. 일부 필드에 대해 해당 동작을 원하지 않을 수도 있습니다. 예를 들어 통화 값의 편집 UI에서 통화 기호를 원하지 않을 수 있습니다.

`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성을 사용하는 것이 좋습니다. `DataType` 특성은 데이터를 화면에 렌더링하는 방법이 아닌 데이터의 의미 체계를 전달하고 DisplayFormat으로는 얻을 수 없는 다음과 같은 이점을 제공합니다.

* 브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크 등을 표시합니다).
* 기본적으로 브라우저는 사용자의 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.
* `DataType` 특성은 ASP.NET Core 프레임워크를 활성화하여 데이터를 렌더링할 올바른 필드 템플릿을 선택할 수 있습니다. 자체적으로 사용되는 경우 `DisplayFormat`은 문자열 템플릿을 사용합니다.

**참고:** jQuery 유효성 검사는 `Range` 특성 및 `DateTime`을 사용하지 않습니다. 예를 들어 다음 코드는 날짜가 지정된 범위에 있을 경우에도 클라이언트 쪽 유효성 검사 오류를 항상 표시합니다.

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

일반적으로 모델에서 고정된 날짜를 컴파일하는 방식은 좋지 않으므로 `Range` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.

다음 코드는 특성을 한 줄로 결합하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Razor Pages 및 EF Core 시작](xref:data/ef-rp/intro)에서는 Razor Pages를 사용한 고급 EF Core 작업을 보여 줍니다.

### <a name="apply-migrations"></a>마이그레이션 적용

클래스에 적용된 DataAnnotations는 스키마를 변경합니다. 예를 들어 `Title` 필드에 적용되는 DataAnnotations는 다음과 같습니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* 문자를 60자로 제한합니다.
* `null` 값은 허용하지 않습니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

`Movie` 테이블에는 현재 다음과 같은 스키마가 있습니다.

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

위의 스키마 변경으로 인해 EF가 예외를 throw하지 않습니다. 그러나 스키마가 모델과 일치하도록 마이그레이션을 만듭니다.

**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔** 을 선택합니다.
PMC에서 다음 명령을 입력합니다.

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database`는 `New_DataAnnotations` 클래스의 `Up` 메서드를 실행합니다. 다음과 같이 `Up` 메서드를 검사합니다.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

업데이트된 `Movie` 테이블에는 다음 스키마가 있습니다.

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

SQLite에는 마이그레이션이 필요하지 않습니다.

---

### <a name="publish-to-azure"></a>Azure에 게시

Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: SQL Database가 포함된 Azure에서 ASP.NET Core 앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)를 참조하세요.

Razor Pages에 관한 이 소개를 완료해 주셔서 감사합니다. [Razor Pages 및 EF Core 시작](xref:data/ef-rp/intro)은 이 자습서의 유용한 후속편입니다.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [이 자습서의 YouTube 버전](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [이전: 새 필드 추가](xref:tutorials/razor-pages/new-field)
