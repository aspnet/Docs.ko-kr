---
title: 9부. ASP.NET Core MVC 앱에 유효성 검사 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 9부입니다.
ms.author: riande
ms.date: 04/13/2017
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
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 340a66c4a561c6e00bf6f38bcf51abc795aa649c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059093"
---
# <a name="part-9-add-validation-to-an-aspnet-core-mvc-app"></a>9부. ASP.NET Core MVC 앱에 유효성 검사 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 다음 작업을 수행합니다.

* `Movie` 모델에 유효성 검사 논리를 추가합니다.
* 사용자가 영화를 만들거나 편집할 때마다 유효성 검사 규칙을 적용합니다.

## <a name="keeping-things-dry"></a>반복 금지 준수

MVC의 설계 원칙 중 하나는 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)(반복 금지, Don't Repeat Yourself)입니다. ASP.NET Core MVC는 기능 또는 동작을 한 번만 지정한 다음, 앱의 모든 곳에 반영하는 것을 권장합니다. 이렇게 하면 작성할 코드의 양이 줄어들고 작성 코드에서 오류가 발생할 가능성이 낮아지며 테스트 및 유지 관리가 더 쉬워집니다.

MVC 및 Entity Framework Core Code First가 제공하는 유효성 검사 지원은 반복 금지 원칙의 좋은 사례입니다. 유효성 검사 규칙을 한 위치(모델 클래스에서)에서 선언적으로 지정하고 앱의 모든 위치에서 해당 규칙을 시행할 수 있습니다.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>유효성 검사 오류 UI

앱을 실행하고 Movies 컨트롤러로 이동합니다.

**Create New** 링크를 눌러 새 영화를 추가합니다. 일부 잘못된 값으로 양식을 기입합니다. jQuery 클라이언트 쪽 유효성 검사가 오류를 감지하자마자 오류 메시지를 표시합니다.

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 표시된 영화 보기 양식](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

양식에서 잘못된 값을 포함하고 있는 각 필드에 적절한 유효성 검사 오류 메시지가 자동으로 렌더링되는 방법을 확인합니다. 오류는 클라이언트 쪽(JavaScript 및 jQuery 사용 시) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두에서 발생합니다.

가장 큰 이점은 이 유효성 검사 UI를 사용하기 위해 `MoviesController` 클래스 또는 *Create.cshtml* 의 코드를 한 줄도 변경할 필요가 없다는 점입니다. 이 자습서에서 이전에 만든 컨트롤러 및 보기는 `Movie` 모델 클래스의 속성에 유효성 검사 특성을 사용하여 지정한 유효성 검사 규칙을 자동으로 가져옵니다. `Edit` 작업 메서드로 유효성 검사를 테스트하여 동일한 유효성 검사가 적용되는지 확인해보세요.

양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 전송되지 않습니다. [Fiddler 도구](https://www.telerik.com/fiddler) 또는 [F12 개발자 도구](/microsoft-edge/devtools-guide)를 사용하여 `HTTP Post` 메서드에 중단점을 넣어 이를 확인할 수 있습니다.

## <a name="how-validation-works"></a>유효성 검사 작동 방식

컨트롤러나 보기의 코드를 전혀 수정하지 않고도 어떻게 유효성 검사 UI가 생성되는지 궁금할 것입니다. 다음 코드는 두 `Create` 메서드를 보여줍니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

첫 번째(HTTP GET) `Create` 작업 메서드는 최초 Create 양식을 표시합니다. 두 번째(`[HttpPost]`) 버전은 양식 게시를 처리합니다. 두 번째 `Create` 메서드(`[HttpPost]` 버전)는 `ModelState.IsValid`를 호출하여 영화의 유효성 검사 오류 여부를 확인합니다. 이 메서드를 호출하면 개체에 적용된 모든 유효성 검사 특성이 평가됩니다. 개체에 유효성 검사 오류가 있으면 `Create` 메서드는 양식을 다시 표시합니다. 오류가 없으면 메서드가 데이터베이스에 새 영화를 저장합니다. 이 영화 예제에서는 클라이언트 쪽에서 유효성 검사 오류가 감지되면 서버에 양식이 게시되지 않으며, 두 번째 `Create` 메서드가 절대 호출되지 않습니다. 브라우저에서 JavaScript를 사용하지 않으면 클라이언트 유효성 검사가 비활성화되며 모든 유효성 검사 오류를 감지하는 HTTP POST `Create` 메서드 `ModelState.IsValid`를 테스트할 수 있습니다.

`[HttpPost] Create` 메서드에서 중단점을 설정하고 메서드가 호출되지 않았는지 확인할 수 있으며, 유효성 검사 오류가 감지되면 클라이언트 쪽 유효성 검사가 양식 데이터를 제출하지 않습니다. 브라우저에서 JavaScript를 사용하지 않고 오류가 있는 상태로 양식을 제출하면 중단점에 이르게 됩니다. JavaScript 없이도 여전히 완전한 유효성 검사가 가능합니다. 

다음 이미지는 Firefox 브라우저에서 JavaScript를 사용하지 않도록 설정하는 방법을 보여 줍니다.

![Firefox: 옵션의 콘텐츠 탭에서 Javascript 사용 확인란의 선택을 취소합니다.](~/tutorials/first-mvc-app/validation/_static/ff.png)

다음 이미지는 Chrome 브라우저에서 JavaScript를 사용하지 않도록 설정하는 방법을 보여줍니다.

![Google Chrome: 콘텐츠 설정의 Javascript에서 모든 사이트의 JavaScript 실행 허용 안 함을 선택합니다.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

JavaScript를 사용하지 않도록 설정한 뒤에 잘못된 데이터를 게시하고 디버거를 단계별로 실행합니다.

![잘못된 데이터 게시에 대한 디버깅 중에는 ModelState.IsValid의 Intellisense에 해당 값이 false로 표시됩니다.](~/tutorials/first-mvc-app/validation/_static/ms.png)

*Create.cshtml* 보기 템플릿의 일부분이 다음 태그에 나타나 있습니다.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

위의 태크는 최초 양식을 표시하고 오류 발생 시 양식을 다시 표시하기 위해 작업 메서드에서 사용됩니다.

[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하여 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다. [유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다. 자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.

이 방식의 가장 멋진 장점은 컨트롤러나 `Create` 보기 템플릿이 실제 적용되는 유효성 검사 규칙이나 표시되는 특정 오류 메시지에 대해 전혀 알지 못한다는 것입니다. 유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다. 동일한 유효성 검사 규칙이 `Edit` 보기 및 모델을 편집하는 만들 수 있는 모든 다른 보기 템플릿에 자동으로 적용됩니다.

유효성 검사 논리를 변경해야 할 경우 모델에 유효성 검사 특성을 추가하여(이 예제에서는 `Movie` 클래스) 정확히 한 곳에서 변경할 수 있습니다. 모든 유효성 검사 논리가 한 곳에서 정의되어 모든 곳에서 사용되므로 애플리케이션의 서로 다른 부분이 규칙 적용 방법에 부합하는지 우려하지 않아도 됩니다. 이렇게 하면 코드가 매우 깔끔해지고 유지 관리 및 확장이 간편합니다. 또한 반복 금지 원칙에 완전히 부합하게 됩니다.

## <a name="using-datatype-attributes"></a>DataType 특성 사용

*Movie.cs* 파일을 열고 `Movie` 클래스를 검토합니다. `System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성 모음 외에도 서식 특성을 제공합니다. 이미 `DataType` 열거 값을 출시일 밑 가격 필드에 적용했습니다. 다음 코드는 적절한 `DataType` 특성이 적용된 `ReleaseDate` 및 `Price` 속성을 보여줍니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` 특성은 데이터에 서식을 지정하도록(그리고 URL에 대한 `<a>` 및 이메일에 대한 `<a href="mailto:EmailAddress.com">` 과 같은 요소/특성을 제공하도록) 보기 엔진에 대한 힌트만 제공합니다. `RegularExpression` 특성을 사용하여 데이터 형식의 유효성을 검사할 수 있습니다. `DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용되며 유효성 검사 특성이 아닙니다. 이 예제에서는 시간을 제외한 날짜만 추적하고자 합니다. `DataType` 열거형은 Date, Time, PhoneNumber, Currency, EmailAddress를 비롯한 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 응용 프로그램에서 자동으로 형식별 기능을 제공하도록 설정할 수도 있습니다. 예를 들어, `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들고 HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기를 제공할 수 있습니다. `DataType` 특성은 HTML 5 브라우저가 인식할 수 있는 HTML 5 `data-`(데이터 대시라고 발음합니다) 특성을 내보냅니다. `DataType` 특성은 유효성 검사를 전혀 제공하지 **않습니다**

`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다. 기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.

`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 설정은 값이 편집을 위해 텍스트 상자에 표시될 때 서식 지정도 적용되어야 함을 지정합니다. (예를 들어 통화 값의 경우 편집을 위한 텍스트 상자에 통화 기호를 사용하지 않는 등, 특정 필드에 대해서는 이 기능이 필요없을 수도 있습니다.)

`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성을 사용하는 것이 좋습니다. `DataType` 특성은 데이터를 화면에 렌더링하는 방법이 아닌 데이터의 의미 체계를 전달하고 DisplayFormat으로는 얻을 수 없는 다음과 같은 이점을 제공합니다.

* 브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크 등을 표시합니다).

* 기본적으로 브라우저는 사용자의 로캘에 따른 올바른 서식을 사용하여 데이터를 렌더링합니다.

* `DataType` 특성을 사용하면 MVC가 데이터 렌더링에 적합한 필드 템플릿을 선택할 수 있습니다(`DisplayFormat`만 사용할 경우 문자열 템플릿을 사용합니다).

> [!NOTE]
> jQuery 유효성 검사는 `Range` 특성 및 `DateTime`에서 동작하지 않습니다. 예를 들어 다음 코드는 날짜가 지정된 범위에 존재할 경우에도 항상 클라이언트 쪽 유효성 검사 오류를 표시합니다.
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

`DateTime`에 `Range` 특성을 사용하려면 jQuery 날짜 유효성 검사를 사용하지 않도록 설정해야 합니다. 일반적으로 모델에서 고정된 날짜를 컴파일하는 방식은 좋지 않으므로 `Range` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.

다음 코드는 특성을 한 줄로 결합하는 방법을 보여줍니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

시리즈의 다음 부분에서는 앱을 검토하고 자동으로 생성된 `Details` 및 `Delete` 메서드를 일부 개선합니다.

## <a name="additional-resources"></a>추가 자료

* [양식 사용](xref:mvc/views/working-with-forms)
* [세계화 및 지역화](xref:fundamentals/localization)
* [태그 도우미 소개](xref:mvc/views/tag-helpers/intro)
* [태그 도우미 작성](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [이전](new-field.md)
> [다음](details.md)  
