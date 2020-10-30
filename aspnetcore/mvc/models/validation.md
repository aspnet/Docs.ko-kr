---
title: ASP.NET Core MVC의 모델 유효성 검사
author: rick-anderson
description: MVC 및 페이지 ASP.NET Core의 모델 유효성 검사에 대해 알아봅니다 Razor .
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 77d49710b9d69f6fbbe92970f1c455de32489444
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056961"
---
# <a name="model-validation-in-aspnet-core-mvc-and-no-locrazor-pages"></a>MVC 및 페이지 ASP.NET Core의 모델 유효성 검사 Razor

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkin](https://github.com/serpent5)

이 문서에서는 ASP.NET Core MVC 또는 Pages 앱에서 사용자 입력의 유효성을 검사 하는 방법을 설명 합니다 Razor .

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

## <a name="model-state"></a>모델 상태

모델 상태는 모델 바인딩 및 모델 유효성 검사 두 하위 시스템에서 발생하는 오류를 나타냅니다. [모델 바인딩](model-binding.md)에서 발생하는 오류는 일반적으로 데이터 변환 오류입니다. 예를 들어 정수 필드에 “x”가 입력됩니다. 모델 유효성 검사는 모델 바인딩 후에 발생하며 데이터가 비즈니스 규칙에 맞지 않으면 오류를 보고합니다. 예를 들어 1에서 5 사이의 등급을 필요한 필드에 0이 입력됩니다.

모델 바인딩 및 모델 유효성 검사는 모두 컨트롤러 작업 또는 페이지 처리기 메서드를 실행 하기 전에 수행 Razor 됩니다. 웹앱의 경우 `ModelState.IsValid`를 검사하고 적절히 반응하는 기능은 앱에서 실행합니다. 일반적으로 웹앱은 페이지를 오류 메시지와 함께 다시 표시합니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

웹 API 컨트롤러는 `[ApiController]` 특성을 포함하는 경우 `ModelState.IsValid`를 확인할 필요가 없습니다. 이 경우 모델 상태가 잘못되면 오류 세부 정보를 포함하는 자동 HTTP 400 응답이 반환됩니다. 자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.

## <a name="rerun-validation"></a>유효성 검사 다시 실행

유효성 검사는 자동으로 실행 되지만 수동으로 반복하는 것이 좋습니다. 예를 들어 속성에 대한 값을 계산하고 해당 속성을 계산된 값으로 설정한 후 유효성 검사를 다시 실행하는 것이 좋습니다. 유효성 검사를 다시 실행하려면 다음과 같이 `TryValidateModel` 메서드를 호출합니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>유효성 검사 특성

유효성 검사 특성을 사용하여 모델 속성에 대한 유효성 검사 규칙을 지정할 수 있습니다. 샘플 앱의 다음 예제는 유효성 검사 특성으로 주석을 단 모델 클래스를 나타냅니다. `[ClassicMovie]` 특성은 사용자 지정 유효성 검사 특성이며 다른 특성은 기본 제공 특성입니다. `[ClassicMovieWithClientValidator]`는 표시되지 않습니다. `[ClassicMovieWithClientValidator]`는 사용자 지정 특성을 구현하는 다른 방법을 보여 줍니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>기본 제공 특성

다음은 몇 가지 기본 제공 유효성 검사 특성입니다.

* `[CreditCard]`: 속성에 신용 카드 형식이 있는지 유효성을 검사 합니다. [JQuery 유효성 검사 추가 방법이](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js)필요 합니다.
* `[Compare]`: 모델의 두 속성이 일치 하는지 확인 합니다.
* `[EmailAddress]`: 속성에 전자 메일 형식이 있는지 확인 합니다.
* `[Phone]`: 속성에 전화 번호 형식이 있는지 확인 합니다.
* `[Range]`: 속성 값이 지정 된 범위 내에 속하는지 확인 합니다.
* `[RegularExpression]`: 속성 값이 지정 된 정규식과 일치 하는지 확인 합니다.
* `[Required]`: 필드가 null이 아닌지 확인 합니다. 이 특성의 동작에 대 한 자세한 내용은 [ `[Required]` 특성](#required-attribute) 을 참조 하세요.
* `[StringLength]`: 문자열 속성 값이 지정 된 길이 제한을 초과 하지 않는지 확인 합니다.
* `[Url]`: 속성에 URL 형식이 있는지 확인 합니다.
* `[Remote]`: 서버에서 동작 메서드를 호출 하 여 클라이언트에서 입력의 유효성을 검사 합니다. 이 특성의 동작에 대 한 자세한 내용은 [ `[Remote]` 특성](#remote-attribute) 을 참조 하세요.

유효성 검사 특성의 전체 목록은 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 네임스페이스에서 확인할 수 있습니다.

### <a name="error-messages"></a>오류 메시지

유효성 검사 특성을 사용하여 잘못된 입력에 대해 표시할 오류 메시지를 지정할 수 있습니다. 다음은 그 예입니다.

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

내부적으로 이 특성은 필드 이름에 대한 자리 표시자 및 경우에 따라 추가 자리 표시자를 사용하여 `String.Format`을 호출합니다. 다음은 그 예입니다.

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

`Name` 속성에 적용할 경우 위의 코드에 의해 생성되는 오류 메시지는 "이름의 길이는 6문자에서 8문자 사이여야 합니다"일 것입니다.

특정 특성의 오류 메시지에 대해 `String.Format` 에 전달되는 매개 변수를 알아보려면 [DataAnnotations 소스 코드](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)를 참조하세요.

## <a name="required-attribute"></a>[Required] 특성

.NET Core 3.0 이상의 유효성 검사 시스템은 Null을 허용하지 않는 매개 변수 또는 바인딩된 속성을 `[Required]` 특성을 포함한 것처럼 처리합니다. `decimal` 및 `int`와 같은 [값 형식](/dotnet/csharp/language-reference/keywords/value-types)은 Null을 허용하지 않습니다. `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes>를 구성하여 이 동작을 사용하지 않도록 설정할 수 있습니다.

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>서버에 대한 [Required] 유효성 검사

서버에서 속성이 Null인 경우 필수 값이 없는 것으로 간주합니다. Null을 허용하지 않는 필드는 언제나 유효하며 `[Required]` 특성의 오류 메시지는 표시되지 않습니다.

그러나 Null을 허용하지 않는 속성에 대한 모델 바인딩이 실패하여 `The value '' is invalid`와 같은 오류 메시지가 표시될 수 있습니다. Null을 허용하지 않는 형식의 서버 쪽 유효성 검사에 대한 사용자 지정 오류 메시지를 지정하려는 경우 다음과 같은 방법이 있습니다.

* 필드를 Null 허용으로 만듭니다(예: `decimal` 대신에 `decimal?`). [Nullable \<T> ](/dotnet/csharp/programming-guide/nullable-types/) 값 형식은 표준 nullable 형식 처럼 처리 됩니다.
* 다음 예제에서와 같이 모델 바인딩에 의해 사용할 기본 오류 메시지를 지정합니다.

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  기본 메시지를 설정할 수 있는 모델 바인딩 오류에 대한 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>를 참조하십시오.

### <a name="required-validation-on-the-client"></a>클라이언트에 대한 [Required] 유효성 검사

클라이언트에서 Null을 허용하지 않는 형식 및 문자열은 서버의 경우와 다르게 처리됩니다. 클라이언트에서:

* 값은 해당 값에 대한 입력이 있는 경우에만 존재하는 것으로 간주됩니다. 그러므로 클라이언트 쪽 유효성 검사에서는 Null을 허용하지 않는 형식을 Null 허용 형식과 동일하게 처리합니다.
* 문자열 필드의 공백은 jQuery 유효성 검사 [필수](https://jqueryvalidation.org/required-method/) 메서드에 의해 유효한 입력으로 간주됩니다. 서버 쪽 유효성 검사에서는 공백만 입력된 경우 필수 문자열 필드를 잘못된 것으로 간주합니다.

위에서 설명했듯이 Null을 허용하지 않는 형식은 `[Required]` 특성을 포함한 것처럼 처리됩니다. 즉, `[Required]` 특성을 적용하지 않더라도 클라이언트 쪽 유효성 검사가 이루어집니다. 그러나 해당 특성을 사용하지 않으면 기본 오류 메시지가 표시됩니다. 사용자 지정 오류 메시지를 지정하려면 해당 특성을 사용하십시오.

## <a name="remote-attribute"></a>[Remote] 특성

`[Remote]` 특성은 필드 입력이 유효한지 여부를 결정하기 위해 서버에서 메서드를 호출해야 하는 클라이언트 쪽 유효성 검사를 구현합니다. 예를 들어 앱에서 사용자 이름을 이미 사용 중인지 여부를 확인해야 할 수 있습니다.

원격 유효성 검사를 구현하려면:

1. JavaScript가 호출할 작업 메서드를 만듭니다.  JQuery 유효성 검사 [원격](https://jqueryvalidation.org/remote-method/) 메서드에는 JSON 응답이 필요 합니다.

   * `true`는 입력 데이터가 유효함을 의미합니다.
   * `false`, `undefined` 또는 `null`은 입력이 잘못되었음을 의미합니다. 기본 오류 메시지를 표시합니다.
   * 다른 문자열은 모두 입력이 잘못되었음을 의미합니다. 문자열을 사용자 지정 오류 메시지로 표시합니다.

   다음은 사용자 지정 오류 메시지를 반환하는 작업 메서드의 예입니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. 다음 예제와 같이 모델 클래스에서는 유효성 검사 작업 메서드를 가리키는 `[Remote]` 특성으로 속성에 주석을 답니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   `Microsoft.AspNetCore.Mvc` 네임스페이스에 `[Remote]` 특성이 있습니다.
   
### <a name="additional-fields"></a>추가 필드

`[Remote]` 특성의 `AdditionalFields` 속성을 사용하여 서버의 데이터를 기준으로 필드 조합의 유효성을 검사할 수 있습니다. 예를 들어 `User` 모델이 `FirstName` 및 `LastName` 속성을 포함한 경우 해당 이름 쌍을 이미 가진 기존 사용자가 없는지 확인하는 것이 좋습니다. 다음 예제에서는 `AdditionalFields`을 사용하는 방법을 보여 줍니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields`는 명시적으로 믄자열 "FirstName" 및 "LastName"으로 설정할 수 있지만 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자를 사용하면 나중에 리팩터링이 간단해집니다. 이 유효성 검사의 작업 메서드는 `firstName`과 `lastName` 인수를 모두 허용해야 합니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

사용자가 이름 또는 성을 입력하면 JavaScript에서는 원격 호출을 실행하여 해당 이름 쌍이 사용 중인지 확인합니다.

두 개 이상의 추가 필드에 대한 유효성을 검사하려면 해당 필드를 쉼표로 구분된 목록으로 제공합니다. 예를 들어 `MiddleName` 속성을 모델에 추가하려면 `[Remote]` 특성을 다음 예제와 같이 설정합니다.

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

모든 특성 인수와 같은 `AdditionalFields`은 상수 식이어야 합니다. 따라서 `AdditionalFields`를 초기화하기 위해 [보간된 문자열](/dotnet/csharp/language-reference/keywords/interpolated-strings)을 사용하거나 <xref:System.String.Join*>을 호출하지 마십시오.

## <a name="alternatives-to-built-in-attributes"></a>기본 제공 특성에 대한 대안

기본 제공 특성이 제공하지 않는 유효성 검사가 필요한 경우 다음과 같이 할 수 있습니다.

* [사용자 지정 특성을 만듭니다](#custom-attributes).
* [IValidatableObject](#ivalidatableobject)를 구현합니다.

## <a name="custom-attributes"></a>사용자 지정 특성

기본 제공 유효성 검사 특성이 처리하지 않는 시나리오의 경우 사용자 지정 유효성 검사 특성을 만들 수 있습니다. <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>에서 상속하는 클래스를 만들고 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 메서드를 재정의합니다.

`IsValid` 메서드에 대해서는 유효성을 검사할 입력인 *값* 이라는 개체를 지정할 수 있습니다. 또한 오버로드에 대해서는 모델 바인딩에 의해 생성된 모델 인스턴스와 같은 추가 정보를 제공하는 `ValidationContext` 개체를 지정할 수 있습니다.

다음 예제에서는 *클래식* 장르의 영화에 대한 개봉 날짜가 지정된 연도보다 이후가 아닌지 유효성을 검사합니다. `[ClassicMovie]` 특성:

* 서버에서만 실행됩니다.
* 클래식 동영상의 경우 릴리스 날짜의 유효성을 검사합니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

위의 예제에서 `movie` 변수는 양식 제출에서 제공된 데이터를 포함하는 `Movie` 개체를 나타냅니다. 유효성 검사에 실패하면 오류 메시지가 발생하여 `ValidationResult`가 반환됩니다.

## <a name="ivalidatableobject"></a>IValidatableObject

앞의 예제는 `Movie` 형식에 대해서만 작동합니다. 또 다른 클래스 수준 유효성 검사 방법은 다음 예제와 같이 모델 클래스 내에 `IValidatableObject`를 구현하는 것입니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>최상위 노드 유효성 검사

최상위 수준 노드에는 다음이 포함됩니다.

* 작업 매개 변수
* 컨트롤러 속성
* 페이지 처리기 매개 변수
* 페이지 모델 속성

모델 바인딩 최상위 노드는 모델 속성의 유효성을 검사하는 것 외에도 유효성이 검사됩니다. 샘플 앱의 다음 예제에서 `VerifyPhone` 메서드는 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>를 사용하여 `phone` 작업 매개 변수의 유효성을 검사합니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

최상위 노드는 유효성 검사 특성과 함께 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute>를 사용할 수 있습니다. 샘플 앱의 다음 예제에서 `CheckAge` 메서드는 양식을 제출할 때 쿼리 문자열에서 `age` 매개 변수를 바인딩해야 함을 지정합니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

기간 확인 페이지( *CheckAge.cshtml* )에는 두 가지 양식이 있습니다. 첫 번째 양식은 `99`의 `Age` 값을 쿼리 문자열 매개 변수(`https://localhost:5001/Users/CheckAge?Age=99`)로 제출합니다.

쿼리 문자열에서 올바른 서식이 지정된 `age` 매개 변수를 제출하면 양식이 유효성을 검사합니다.

기간 확인 페이지의 두 번째 양식은 요청 본문의 `Age` 값을 제출하고 유효성 검사가 실패합니다. `age` 매개 변수는 쿼리 문자열에서 가져와야 하므로 바인딩이 실패합니다.

## <a name="maximum-errors"></a>최대 오류 수

유효성 검사는 최대 오류 수(기본값 200)에 도달하면 중지됩니다. `Startup.ConfigureServices`에 다음 코드로 이 번호를 구성할 수 있습니다.

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>최대 재귀

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>는 유효성 검사 중인 모델의 개체 그래프를 트래버스합니다. 깊거나 무한히 재귀하는 모델의 경우 유효성 검사를 실행하면 스택 오버플로가 발생할 수 있습니다. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth)는 방문자 재귀가 구성된 깊이를 초과하는 경우 유효성 검사를 조기에 중지하는 방법을 제공합니다. `MvcOptions.MaxValidationDepth`의 기본값은 32입니다.

## <a name="automatic-short-circuit"></a>자동 단락

모델 그래프에 대한 유효성 검사가 필요하지 않은 경우 유효성 검사는 자동으로 단락됩니다(건너뜀). 런타임에 유효성 검사를 건너뛰는 개체로는 기본 형식(`byte[]`, `string[]`, `Dictionary<string, string>`) 수집 및 유효성 검사기를 포함하지 않은 복합 개체 그래프가 있습니다.

## <a name="disable-validation"></a>유효성 검사를 사용하지 않도록 설정

유효성 검사를 사용하지 않도록 설정하려면:

1. 필드를 잘못된 것으로 표시하지 않는 `IObjectModelValidator` 구현을 만듭니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. 종속성 검사 컨테이너에서 다음 코드를 `Startup.ConfigureServices`에 추가하여 기본 `IObjectModelValidator` 구현을 대체합니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

모델 바인딩에서 발생하는 모델 상태 오류는 여전히 표시될 수 있습니다.

## <a name="client-side-validation"></a>클라이언트 쪽 유효성 검사

클라이언트 쪽 유효성 검사는 형식이 유효할 때까지 전송을 방지합니다. 제출 단추는 형식을 전송하거나 오류 메시지를 표시하는 JavaScript를 실행합니다.

클라이언트 쪽 유효성 검사는 양식에 대한 입력 오류가 있는 경우 불필요한 서버 왕복을 방지합니다. *_Layout.cshtml* 및 *_ValidationScriptsPartial.cshtml* 의 다음 스크립트 참조는 클라이언트 쪽 유효성 검사를 지원합니다.

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

Jquery가 없는 [유효성 검사](https://github.com/aspnet/jquery-validation-unobtrusive) 스크립트는 널리 사용 되는 [jquery 유효성 검사](https://jqueryvalidation.org/) 플러그 인을 기반으로 하는 사용자 지정 Microsoft 프런트 엔드 라이브러리입니다. jQuery 비간섭 유효성 검사를 사용하지 않을 경우 두 위치(모델 속성에 대한 서버 쪽 유효성 검사 특성에서 한 번 및 클라이언트 쪽 스크립트에서 다시 한 번)에서 동일한 유효성 검사 논리를 코딩해야 할 수 있습니다. 그 대신 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미 ](xref:mvc/views/overview)는 유효성 검사가 필요한 양식 요소에 대해 모델 특성의 유효성 검사 특성 및 형식 메타데이터를 사용하여 HTML 5 `data-` 특성을 렌더링합니다. jQuery를 방해 하지 않는 유효성 검사는 특성을 구문 분석 하 `data-` 고이 논리를 JQuery 유효성 검사에 전달 하 여 서버 쪽 유효성 검사 논리를 클라이언트에 효과적으로 "복사" 합니다. 다음과 같이 태그 도우미를 사용하여 클라이언트에서 유효성 검사 오류를 표시할 수 있습니다.

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

위의 태그 도우미는 다음 HTML을 렌더링합니다.

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

HTML의 `data-` 특성 출력은 `Movie.ReleaseDate` 속성에 대한 유효성 검사 특성에 해당합니다. `data-val-required` 특성은 사용자가 릴리스 날짜 필드를 입력하지 않았음을 표시하는 오류 메시지를 포함합니다. jQuery를 사용할 수 없는 유효성 검사는이 값을 jQuery Validation [required ()](https://jqueryvalidation.org/required-method/) 메서드에 전달 하 여 해당 메시지를 해당 요소에 표시 **\<span>** 합니다.

데이터 형식 유효성 검사는 `[DataType]` 특성에 의해 재정의되지 않은 한, 속성의 .NET 형식을 기반으로 합니다. 브라우저는 고유한 기본 오류 메시지를 가지고 있지만 jQuery유효성 검사의 비간섭 유효성 검사 패키지는 해당 메시지를 재정의할 수 있습니다. `[DataType]` 특성 및 `[EmailAddress]`와 같은 서브클래스를 사용하여 오류 메시지를 지정할 수 있습니다.

## <a name="unobtrusive-validation"></a>비간섭 유효성 검사

비간섭 유효성 검사에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/1111)를 참조하세요.

### <a name="add-validation-to-dynamic-forms"></a>동적 형식에 유효성 검사 추가

jQuery를 방해 하지 않는 유효성 검사는 페이지가 처음 로드 될 때 유효성 검사 논리와 매개 변수를 jQuery 유효성 검사에 전달 합니다. 따라서 동적으로 생성된 양식에 대해서는 유효성 검사가 자동으로 작동하지 않습니다. 유효성 검사를 사용하도록 설정하려면 jQuery 비간섭 유효성 검사에서 동적 폼을 만든 후에 즉시 구문 분석하도록 지시합니다. 예를 들어 다음 코드에서는 AJAX를 통해 추가된 양식에 대해 클라이언트 쪽 유효성 검사를 설정합니다.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` 메서드는 하나의 인수에 대해 jQuery 선택기를 허용합니다. 이 메서드는 jQuery 비간섭 유효성 검사에서 해당 선택기 내에 있는 형식의 `data-` 특성을 구문 분석하도록 지시합니다. 그런 다음 이러한 특성의 값은 jQuery 유효성 검사 플러그 인에 전달 됩니다.

### <a name="add-validation-to-dynamic-controls"></a>동적 컨트롤에 유효성 검사 추가

`$.validator.unobtrusive.parse()` 메서드는 `<input>` 및 `<select/>`와 같은 동적으로 생성된 개별 컨트롤이 아닌 전체 양식에 대해 작용합니다. 양식을 다시 구문 분석하려면 다음 예제와 같이 이전에 양식을 구문 분석했을 때 추가된 유효성 검사 데이터를 제거합니다.

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>사용자 지정 클라이언트 쪽 유효성 검사

사용자 지정 클라이언트 쪽 유효성 검사는 `data-` 사용자 지정 JQuery 유효성 검사 어댑터에서 작동 하는 HTML 특성을 생성 하 여 수행 됩니다. 다음 샘플 어댑터 코드는 이 문서에서 이전에 소개한 `[ClassicMovie]` 및 `[ClassicMovieWithClientValidator]` 특성용으로 작성되었습니다.

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

어댑터를 작성 하는 방법에 대 한 자세한 내용은 [JQuery 유효성 검사 설명서](https://jqueryvalidation.org/documentation/)를 참조 하세요.

지정된 필드에 대한 어댑터 사용은 `data-` 특성에 의해 트리거됩니다.

* 필드를 유효성 검사가 적용되는 것으로 표시합니다(`data-val="true"`).
* 유효성 검사 규칙 이름 및 오류 메시지 텍스트(예: `data-val-rulename="Error message."`)를 식별합니다.
* 유효섬 검사기에 필요한 추가 매개 변수(예: `data-val-rulename-param1="value"`)를 제공합니다.

다음 에제에서는 샘플 앱의 `ClassicMovie` 특성에 대한 `data-` 특성을 보여줍니다.

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

앞에서 설명했듯이 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미](xref:mvc/views/overview)는 유효성 검사 특성의 정보를 사용하여 `data-` 특성을 렌더링합니다. 사용자 지정 `data-` HTML 특성을 생성하는 코드를 작성하는 두 가지 방법이 있습니다.

* `AttributeAdapterBase<TAttribute>`에서 파생하는 클래스 및 `IValidationAttributeAdapterProvider`를 구현하는 클래스를 만들고 사용자의 특성 및 해당 어댑터를 DI에 등록합니다. 이 방법은 서버 관련 및 클라이언트 관련 유효성 검사 코드가 별도의 클래스에 있는 [단일 책임 보안 주체](https://wikipedia.org/wiki/Single_responsibility_principle)를 따릅니다. 또한 이 어댑터는 DI에 등록되었으므로 필요한 경우 DI의 다른 서비스를 사용할 수 있다는 이점이 있습니다.
* 사용자의 `ValidationAttribute` 클래스에서 `IClientModelValidator`를 구현합니다. 이 방법은 특성이 서버 쪽 유효성 검사를 수행하지 않고 DI의 서비스가 필요하지 않은 경우 적절할 수 있습니다.

### <a name="attributeadapter-for-client-side-validation"></a>클라이언트 쪽 유효성 검사에 대한 특성 어댑터

이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovie` 특성에 사용됩니다. 이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:

1. 사용자 지정 유효성 검사 특성에 대한 특성 어댑터 클래스를 생성합니다. [Attributeadapterbase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)에서 클래스를 파생 시킵니다. 이 예제와 같이 렌더링된 출력에 `data-` 특성을 추가하는 `AddValidation` 메서드를 생성합니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>를 구현하는 어댑터 공급자 클래스를 생성합니다. `GetAttributeAdapter` 메서드에서 다음 예제와 같이 사용자 지정 특성을 어댑터의 생성자에 전달합니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. DI용 어댑터 공급자를 `Startup.ConfigureServices`에 등록합니다.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>클라이언트 쪽 유효성 검사를 위한 IClientModelValidator

이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovieWithClientValidator` 특성에 사용됩니다. 이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:

* 사용자 지정 유효성 검사 특성에서 `IClientModelValidator` 인터페이스를 구현하고 `AddValidation` 메서드를 생성합니다. `AddValidation` 메서드에서 다음 예제와 같이 유효성 검사를 위한 `data-` 특성을 추가합니다.

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>클라이언트 쪽 유효성 검사를 사용하지 않도록 설정

다음 코드에서는 페이지에서 클라이언트 유효성 검사를 사용 하지 않도록 설정 합니다 Razor .

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

클라이언트 쪽 유효성 검사를 사용하지 않도록 설정하는 다른 옵션:

* 모든 *.cshtml* 파일의 `_ValidationScriptsPartial`에 대한 참조를 주석으로 처리합니다.
* *Pages\Shared\_ValidationScriptsPartial* 파일의 콘텐츠를 제거합니다.

위의 방법으로는 클래스 라이브러리의 클라이언트 쪽 유효성 검사를 수행할 수 없습니다 ASP.NET Core Identity Razor . 자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [System.ComponentModel.DataAnnotations 네임스페이스](xref:System.ComponentModel.DataAnnotations)
* [모델 바인딩](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 ASP.NET Core MVC 또는 Pages 앱에서 사용자 입력의 유효성을 검사 하는 방법을 설명 합니다 Razor .

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.

## <a name="model-state"></a>모델 상태

모델 상태는 모델 바인딩 및 모델 유효성 검사 두 하위 시스템에서 발생하는 오류를 나타냅니다. [모델 바인딩](model-binding.md)에서 발생하는 오류는 일반적으로 데이터 변환 오류입니다(예를 들어 정수를 입력해야 하는 필드에 "x"를 입력하는 오류). 모델 유효성 검사는 모델 바인딩 후에 실행되며 데이터가 비즈니스 규칙을 준수하지 않는 경우(예를 들어 1에서 5 사이의등급을 입력해야 하는 필드에 0을 입력) 오류를 보고합니다.

모델 바인딩 및 유효성 검사는 모두 컨트롤러 작업 또는 페이지 처리기 메서드를 실행 하기 전에 수행 Razor 됩니다. 웹앱의 경우 `ModelState.IsValid`를 검사하고 적절히 반응하는 기능은 앱에서 실행합니다. 일반적으로 웹앱은 페이지를 오류 메시지와 함께 다시 표시합니다.

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

웹 API 컨트롤러는 `[ApiController]` 특성을 포함하는 경우 `ModelState.IsValid`를 확인할 필요가 없습니다. 이 경우 모델 상태가 잘못되면 오류 세부 정보를 포함하는 자동 HTTP 400 응답이 반환됩니다. 자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.

## <a name="rerun-validation"></a>유효성 검사 다시 실행

유효성 검사는 자동으로 실행 되지만 수동으로 반복하는 것이 좋습니다. 예를 들어 속성에 대한 값을 계산하고 해당 속성을 계산된 값으로 설정한 후 유효성 검사를 다시 실행하는 것이 좋습니다. 유효성 검사를 다시 실행하려면 다음과 같이 `TryValidateModel` 메서드를 호출합니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>유효성 검사 특성

유효성 검사 특성을 사용하여 모델 속성에 대한 유효성 검사 규칙을 지정할 수 있습니다. [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)의 다음 예제는 유효성 검사 특성으로 주석을 단 모델 클래스를 나타냅니다. `[ClassicMovie]` 특성은 사용자 지정 유효성 검사 특성이며 다른 특성은 기본 제공 특성입니다. 사용자 지정 특성을 구현하는 다른 방법을 보여 주는 `[ClassicMovie2]`는 표시되지 않습니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>기본 제공 특성

기본 제공 유효성 검사 특성은 다음과 같습니다.

* `[CreditCard]`: 속성에 신용 카드 형식이 있는지 유효성을 검사 합니다.
* `[Compare]`: 모델의 두 속성이 일치 하는지 확인 합니다. 예를 들어 *Register.cshtml.cs* 파일은 `[Compare]`를 사용하여 입력된 두 암호가 일치하는지 유효성을 검사합니다. [스 캐 폴드 Identity ](xref:security/authentication/scaffold-identity) 등록 코드를 확인 합니다.
* `[EmailAddress]`: 속성에 전자 메일 형식이 있는지 확인 합니다.
* `[Phone]`: 속성에 전화 번호 형식이 있는지 확인 합니다.
* `[Range]`: 속성 값이 지정 된 범위 내에 속하는지 확인 합니다.
* `[RegularExpression]`: 속성 값이 지정 된 정규식과 일치 하는지 확인 합니다.
* `[Required]`: 필드가 null이 아닌지 확인 합니다. 이 특성의 동작에 대 한 자세한 내용은 [ `[Required]` 특성](#required-attribute) 을 참조 하세요.
* `[StringLength]`: 문자열 속성 값이 지정 된 길이 제한을 초과 하지 않는지 확인 합니다.
* `[Url]`: 속성에 URL 형식이 있는지 확인 합니다.
* `[Remote]`: 서버에서 동작 메서드를 호출 하 여 클라이언트에서 입력의 유효성을 검사 합니다. 이 특성의 동작에 대 한 자세한 내용은 [ `[Remote]` 특성](#remote-attribute) 을 참조 하세요.

클라이언트 쪽 유효성 검사에 `[RegularExpression]` 특성을 사용하면 클라이언트의 JavaScript에서 regex가 실행됩니다. 이는 [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) 일치 동작이 사용됨을 의미합니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/corefx/issues/42487)를 참조하세요.

유효성 검사 특성의 전체 목록은 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 네임스페이스에서 확인할 수 있습니다.

### <a name="error-messages"></a>오류 메시지

유효성 검사 특성을 사용하여 잘못된 입력에 대해 표시할 오류 메시지를 지정할 수 있습니다. 다음은 그 예입니다.

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

내부적으로 이 특성은 필드 이름에 대한 자리 표시자 및 경우에 따라 추가 자리 표시자를 사용하여 `String.Format`을 호출합니다. 다음은 그 예입니다.

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

`Name` 속성에 적용할 경우 위의 코드에 의해 생성되는 오류 메시지는 "이름의 길이는 6문자에서 8문자 사이여야 합니다"일 것입니다.

특정 특성의 오류 메시지에 대해 `String.Format` 에 전달되는 매개 변수를 알아보려면 [DataAnnotations 소스 코드](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)를 참조하세요.

## <a name="required-attribute"></a>[Required] 특성

기본적으로 유효성 검사 시스템은 Null을 허용하지 않는 매개 변수 또는 속성을 `[Required]` 특성을 포함한 것처럼 처리합니다. `decimal` 및 `int`와 같은 [값 형식](/dotnet/csharp/language-reference/keywords/value-types)은 Null을 허용하지 않습니다.

### <a name="required-validation-on-the-server"></a>서버에 대한 [Required] 유효성 검사

서버에서 속성이 Null인 경우 필수 값이 없는 것으로 간주합니다. Null을 허용하지 않는 필드는 언제나 유효하며 [Required] 특성의 오류 메시지는 표시되지 않습니다.

그러나 Null을 허용하지 않는 속성에 대한 모델 바인딩이 실패하여 `The value '' is invalid`와 같은 오류 메시지가 표시될 수 있습니다. Null을 허용하지 않는 형식의 서버 쪽 유효성 검사에 대한 사용자 지정 오류 메시지를 지정하려는 경우 다음과 같은 방법이 있습니다.

* 필드를 Null 허용으로 만듭니다(예: `decimal` 대신에 `decimal?`). [Nullable \<T> ](/dotnet/csharp/programming-guide/nullable-types/) 값 형식은 표준 nullable 형식 처럼 처리 됩니다.
* 다음 예제에서와 같이 모델 바인딩에 의해 사용할 기본 오류 메시지를 지정합니다.

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  기본 메시지를 설정할 수 있는 모델 바인딩 오류에 대한 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>를 참조하십시오.

### <a name="required-validation-on-the-client"></a>클라이언트에 대한 [Required] 유효성 검사

클라이언트에서 Null을 허용하지 않는 형식 및 문자열은 서버의 경우와 다르게 처리됩니다. 클라이언트에서:

* 값은 해당 값에 대한 입력이 있는 경우에만 존재하는 것으로 간주됩니다. 그러므로 클라이언트 쪽 유효성 검사에서는 Null을 허용하지 않는 형식을 Null 허용 형식과 동일하게 처리합니다.
* 문자열 필드의 공백은 jQuery 유효성 검사 [필수](https://jqueryvalidation.org/required-method/) 메서드에 의해 유효한 입력으로 간주됩니다. 서버 쪽 유효성 검사에서는 공백만 입력된 경우 필수 문자열 필드를 잘못된 것으로 간주합니다.

위에서 설명했듯이 Null을 허용하지 않는 형식은 `[Required]` 특성을 포함한 것처럼 처리됩니다. 즉, `[Required]` 특성을 적용하지 않더라도 클라이언트 쪽 유효성 검사가 이루어집니다. 그러나 해당 특성을 사용하지 않으면 기본 오류 메시지가 표시됩니다. 사용자 지정 오류 메시지를 지정하려면 해당 특성을 사용하십시오.

## <a name="remote-attribute"></a>[Remote] 특성

`[Remote]` 특성은 필드 입력이 유효한지 여부를 결정하기 위해 서버에서 메서드를 호출해야 하는 클라이언트 쪽 유효성 검사를 구현합니다. 예를 들어 앱에서 사용자 이름을 이미 사용 중인지 여부를 확인해야 할 수 있습니다.

원격 유효성 검사를 구현하려면:

1. JavaScript가 호출할 작업 메서드를 만듭니다.  jQuery 유효성 검사 [remote](https://jqueryvalidation.org/remote-method/) 메서드는 JSON 응답을 수신해야 합니다.

   * `"true"`는 입력 데이터가 유효함을 의미합니다.
   * `"false"`, `undefined` 또는 `null`은 입력이 잘못되었음을 의미합니다.  기본 오류 메시지를 표시합니다.
   * 다른 문자열은 모두 입력이 잘못되었음을 의미합니다. 문자열을 사용자 지정 오류 메시지로 표시합니다.

   다음은 사용자 지정 오류 메시지를 반환하는 작업 메서드의 예입니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. 다음 예제와 같이 모델 클래스에서는 유효성 검사 작업 메서드를 가리키는 `[Remote]` 특성으로 속성에 주석을 답니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   `Microsoft.AspNetCore.Mvc` 네임스페이스에 `[Remote]` 특성이 있습니다. `Microsoft.AspNetCore.App` 또는 `Microsoft.AspNetCore.All` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet 패키지를 설치합니다.
   
### <a name="additional-fields"></a>추가 필드

`[Remote]` 특성의 `AdditionalFields` 속성을 사용하여 서버의 데이터를 기준으로 필드 조합의 유효성을 검사할 수 있습니다. 예를 들어 `User` 모델이 `FirstName` 및 `LastName` 속성을 포함한 경우 해당 이름 쌍을 이미 가진 기존 사용자가 없는지 확인하는 것이 좋습니다. 다음 예제에서는 `AdditionalFields`을 사용하는 방법을 보여 줍니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields`는 명시적으로 문자열 `"FirstName"` 및 `"LastName"`으로 설정할 수 있지만 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자를 사용하면 이후 리팩터링이 간단해집니다. 이 유효성 검사에 대한 작업 메서드에 대해서는 이름과 성 인수를 모두 지정할 수 있습니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

사용자가 이름 또는 성을 입력하면 JavaScript에서는 원격 호출을 실행하여 해당 이름 쌍이 사용 중인지 확인합니다.

두 개 이상의 추가 필드에 대한 유효성을 검사하려면 해당 필드를 쉼표로 구분된 목록으로 제공합니다. 예를 들어 `MiddleName` 속성을 모델에 추가하려면 `[Remote]` 특성을 다음 예제와 같이 설정합니다.

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

모든 특성 인수와 같은 `AdditionalFields`은 상수 식이어야 합니다. 따라서 `AdditionalFields`를 초기화하기 위해 [보간된 문자열](/dotnet/csharp/language-reference/keywords/interpolated-strings)을 사용하거나 <xref:System.String.Join*>을 호출하지 마십시오.

## <a name="alternatives-to-built-in-attributes"></a>기본 제공 특성에 대한 대안

기본 제공 특성이 제공하지 않는 유효성 검사가 필요한 경우 다음과 같이 할 수 있습니다.

* [사용자 지정 특성을 만듭니다](#custom-attributes).
* [IValidatableObject](#ivalidatableobject)를 구현합니다.

## <a name="custom-attributes"></a>사용자 지정 특성

기본 제공 유효성 검사 특성이 처리하지 않는 시나리오의 경우 사용자 지정 유효성 검사 특성을 만들 수 있습니다. <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>에서 상속하는 클래스를 만들고 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 메서드를 재정의합니다.

`IsValid` 메서드에 대해서는 유효성을 검사할 입력인 *값* 이라는 개체를 지정할 수 있습니다. 또한 오버로드에 대해서는 모델 바인딩에 의해 생성된 모델 인스턴스와 같은 추가 정보를 제공하는 `ValidationContext` 개체를 지정할 수 있습니다.

다음 예제에서는 *클래식* 장르의 영화에 대한 개봉 날짜가 지정된 연도보다 이후가 아닌지 유효성을 검사합니다. `[ClassicMovie2]` 특성은 먼저 장르를 확인하고 해당 장르가 *클래식* 인 경우에만 계속합니다. 클래식으로 식별된 영화에 대해서는 개봉 날짜를 확인하여 해당 날짜가 특성 생성자에 전달된 한계보다 이후가 아닌지 확인합니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

위의 예제에서 `movie` 변수는 양식 제출에서 제공된 데이터를 포함하는 `Movie` 개체를 나타냅니다. `IsValid` 메서드는 날짜 및 장르를 확인합니다. 유효성 검사에 성공하면 `IsValid`는 `ValidationResult.Success` 코드를 반환합니다. 유효성 검사에 실패하면 오류 메시지가 발생하여 `ValidationResult`가 반환됩니다.

## <a name="ivalidatableobject"></a>IValidatableObject

앞의 예제는 `Movie` 형식에 대해서만 작동합니다. 또 다른 클래스 수준 유효성 검사 방법은 다음 예제와 같이 모델 클래스 내에 `IValidatableObject`를 구현하는 것입니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>최상위 노드 유효성 검사

최상위 수준 노드에는 다음이 포함됩니다.

* 작업 매개 변수
* 컨트롤러 속성
* 페이지 처리기 매개 변수
* 페이지 모델 속성

모델 바인딩 최상위 노드는 모델 속성의 유효성을 검사하는 것 외에도 유효성이 검사됩니다. 샘플 앱의 다음 예제에서 `VerifyPhone` 메서드는 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>를 사용하여 `phone` 작업 매개 변수의 유효성을 검사합니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

최상위 노드는 유효성 검사 특성과 함께 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute>를 사용할 수 있습니다. 샘플 앱의 다음 예제에서 `CheckAge` 메서드는 양식을 제출할 때 쿼리 문자열에서 `age` 매개 변수를 바인딩해야 함을 지정합니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

기간 확인 페이지( *CheckAge.cshtml* )에는 두 가지 양식이 있습니다. 첫 번째 양식은 `99`의 `Age` 값을 쿼리 문자열(`https://localhost:5001/Users/CheckAge?Age=99`)로 제출합니다.

쿼리 문자열에서 올바른 서식이 지정된 `age` 매개 변수를 제출하면 양식이 유효성을 검사합니다.

기간 확인 페이지의 두 번째 양식은 요청 본문의 `Age` 값을 제출하고 유효성 검사가 실패합니다. `age` 매개 변수는 쿼리 문자열에서 가져와야 하므로 바인딩이 실패합니다.

`CompatibilityVersion.Version_2_1` 이상을 사용하여 실행하는 경우 최상위 노드 유효성 검사가 기본적으로 활성화됩니다. 그렇지 않으면 최상위 노드 유효성 검사가 비활성화됩니다. 아래와 같이 (`Startup.ConfigureServices`)에서 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 속성을 설정하여 기본 옵션을 재정의할 수 있습니다.

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>최대 오류 수

유효성 검사는 최대 오류 수(기본값 200)에 도달하면 중지됩니다. `Startup.ConfigureServices`에 다음 코드로 이 번호를 구성할 수 있습니다.

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>최대 재귀

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>는 유효성 검사 중인 모델의 개체 그래프를 트래버스합니다. 매우 깊거나 무한히 재귀하는 모델의 경우 유효성 검사를 실행하면 스택 오버플로가 발생할 수 있습니다. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth)는 방문자 재귀가 구성된 깊이를 초과하는 경우 유효성 검사를 조기에 중지하는 방법을 제공합니다. `CompatibilityVersion.Version_2_2` 이상에서 실행하는 경우 `MvcOptions.MaxValidationDepth`의 기본값은 32입니다. 그보다 이전 버전의 경우 이 값은 Null이며, 이는 깊이 제약 조건이 없음을 의미합니다.

## <a name="automatic-short-circuit"></a>자동 단락

모델 그래프에 대한 유효성 검사가 필요하지 않은 경우 유효성 검사는 자동으로 단락됩니다(건너뜀). 런타임에 유효성 검사를 건너뛰는 개체로는 기본 형식(`byte[]`, `string[]`, `Dictionary<string, string>`) 수집 및 유효성 검사기를 포함하지 않은 복합 개체 그래프가 있습니다.

## <a name="disable-validation"></a>유효성 검사를 사용하지 않도록 설정

유효성 검사를 사용하지 않도록 설정하려면:

1. 필드를 잘못된 것으로 표시하지 않는 `IObjectModelValidator` 구현을 만듭니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. 종속성 검사 컨테이너에서 다음 코드를 `Startup.ConfigureServices`에 추가하여 기본 `IObjectModelValidator` 구현을 대체합니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

모델 바인딩에서 발생하는 모델 상태 오류는 여전히 표시될 수 있습니다.

## <a name="client-side-validation"></a>클라이언트 쪽 유효성 검사

클라이언트 쪽 유효성 검사는 형식이 유효할 때까지 전송을 방지합니다. 제출 단추는 형식을 전송하거나 오류 메시지를 표시하는 JavaScript를 실행합니다.

클라이언트 쪽 유효성 검사는 양식에 대한 입력 오류가 있는 경우 불필요한 서버 왕복을 방지합니다. *_Layout.cshtml* 및 *_ValidationScriptsPartial.cshtml* 의 다음 스크립트 참조는 클라이언트 쪽 유효성 검사를 지원합니다.

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

[jQuery 비간섭 유효성 검사](https://github.com/aspnet/jquery-validation-unobtrusive) 스크립트는 널리 사용되는 [jQuery 유효성 검사](https://jqueryvalidation.org/) 플러그 인에 기반한 사용자 지정 Microsoft 프런트 엔드 라이브러리입니다. jQuery 비간섭 유효성 검사를 사용하지 않을 경우 두 위치(모델 속성에 대한 서버 쪽 유효성 검사 특성에서 한 번 및 클라이언트 쪽 스크립트에서 다시 한 번)에서 동일한 유효성 검사 논리를 코딩해야 할 수 있습니다. 그 대신 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미 ](xref:mvc/views/overview)는 유효성 검사가 필요한 양식 요소에 대해 모델 특성의 유효성 검사 특성 및 형식 메타데이터를 사용하여 HTML 5 `data-` 특성을 렌더링합니다. jQuery 비간섭 유효성 검사는 `data-` 특성을 구문 분석하고 jQuery 유효성 검사에 대한 논리를 전달하여 효과적으로 서버 쪽 유효성 검사 논리를 클라이언트에 “복사”합니다. 다음과 같이 태그 도우미를 사용하여 클라이언트에서 유효성 검사 오류를 표시할 수 있습니다.

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

위의 태그 도우미는 다음 HTML을 렌더링합니다.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

HTML의 `data-` 특성 출력은 `ReleaseDate` 속성에 대한 유효성 검사 특성에 해당합니다. `data-val-required` 특성은 사용자가 릴리스 날짜 필드를 입력하지 않았음을 표시하는 오류 메시지를 포함합니다. jQuery를 사용할 수 없는 유효성 검사는이 값을 jQuery Validate [required ()](https://jqueryvalidation.org/required-method/) 메서드에 전달 하 여 해당 메시지를 해당 요소에 표시 **\<span>** 합니다.

데이터 형식 유효성 검사는 `[DataType]` 특성에 의해 재정의되지 않은 한, 속성의 .NET 형식을 기반으로 합니다. 브라우저는 고유한 기본 오류 메시지를 가지고 있지만 jQuery유효성 검사의 비간섭 유효성 검사 패키지는 해당 메시지를 재정의할 수 있습니다. `[DataType]` 특성 및 `[EmailAddress]`와 같은 서브클래스를 사용하여 오류 메시지를 지정할 수 있습니다.

### <a name="add-validation-to-dynamic-forms"></a>동적 형식에 유효성 검사 추가

jQuery 비간섭 유효성 검사는 페이지가 먼저 로드되는 경우 유효성 검사 논리 및 매개 변수를 jQuery 유효성 검사에 전달합니다. 따라서 동적으로 생성된 양식에 대해서는 유효성 검사가 자동으로 작동하지 않습니다. 유효성 검사를 사용하도록 설정하려면 jQuery 비간섭 유효성 검사에서 동적 폼을 만든 후에 즉시 구문 분석하도록 지시합니다. 예를 들어 다음 코드에서는 AJAX를 통해 추가된 양식에 대해 클라이언트 쪽 유효성 검사를 설정합니다.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` 메서드는 하나의 인수에 대해 jQuery 선택기를 허용합니다. 이 메서드는 jQuery 비간섭 유효성 검사에서 해당 선택기 내에 있는 형식의 `data-` 특성을 구문 분석하도록 지시합니다. 그러면 이러한 특성의 값이 jQuery 유효성 검사 플러그 인에 전달됩니다.

### <a name="add-validation-to-dynamic-controls"></a>동적 컨트롤에 유효성 검사 추가

`$.validator.unobtrusive.parse()` 메서드는 `<input>` 및 `<select/>`와 같은 동적으로 생성된 개별 컨트롤이 아닌 전체 양식에 대해 작용합니다. 양식을 다시 구문 분석하려면 다음 예제와 같이 이전에 양식을 구문 분석했을 때 추가된 유효성 검사 데이터를 제거합니다.

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>사용자 지정 클라이언트 쪽 유효성 검사

사용자 지정 클라이언트 쪽 유효성 검사는 사용자 지정 jQuery 유효성 검사 어댑터에서 작동하는 `data-` HTML 특성을 생성하여 수행됩니다. 다음 샘플 어댑터 코드는 이 문서에서 이전에 소개한 `ClassicMovie` 및 `ClassicMovie2` 특성용으로 작성되었습니다.

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

어댑터 작성 방법에 대한 자세한 내용은 [jQuery 유효성 검사 설명서](https://jqueryvalidation.org/documentation/)를 참조하세요.

지정된 필드에 대한 어댑터 사용은 `data-` 특성에 의해 트리거됩니다.

* 필드를 유효성 검사가 적용되는 것으로 표시합니다(`data-val="true"`).
* 유효성 검사 규칙 이름 및 오류 메시지 텍스트(예: `data-val-rulename="Error message."`)를 식별합니다.
* 유효섬 검사기에 필요한 추가 매개 변수(예: `data-val-rulename-parm1="value"`)를 제공합니다.

다음 에제에서는 샘플 앱의 `ClassicMovie` 특성에 대한 `data-` 특성을 보여줍니다.

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

앞에서 설명했듯이 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미](xref:mvc/views/overview)는 유효성 검사 특성의 정보를 사용하여 `data-` 특성을 렌더링합니다. 사용자 지정 `data-` HTML 특성을 생성하는 코드를 작성하는 두 가지 방법이 있습니다.

* `AttributeAdapterBase<TAttribute>`에서 파생하는 클래스 및 `IValidationAttributeAdapterProvider`를 구현하는 클래스를 만들고 사용자의 특성 및 해당 어댑터를 DI에 등록합니다. 이 방법은 서버 관련 및 클라이언트 관련 유효성 검사 코드가 별도의 클래스에 있는 [단일 책임 보안 주체](https://wikipedia.org/wiki/Single_responsibility_principle)를 따릅니다. 또한 이 어댑터는 DI에 등록되었으므로 필요한 경우 DI의 다른 서비스를 사용할 수 있다는 이점이 있습니다.
* 사용자의 `ValidationAttribute` 클래스에서 `IClientModelValidator`를 구현합니다. 이 방법은 특성이 서버 쪽 유효성 검사를 수행하지 않고 DI의 서비스가 필요하지 않은 경우 적절할 수 있습니다.

### <a name="attributeadapter-for-client-side-validation"></a>클라이언트 쪽 유효성 검사에 대한 특성 어댑터

이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovie` 특성에 사용됩니다. 이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:

1. 사용자 지정 유효성 검사 특성에 대한 특성 어댑터 클래스를 생성합니다. [Attributeadapterbase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)에서 클래스를 파생 시킵니다. 이 예제와 같이 렌더링된 출력에 `data-` 특성을 추가하는 `AddValidation` 메서드를 생성합니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>를 구현하는 어댑터 공급자 클래스를 생성합니다. `GetAttributeAdapter` 메서드에서 다음 예제와 같이 사용자 지정 특성을 어댑터의 생성자에 전달합니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. DI용 어댑터 공급자를 `Startup.ConfigureServices`에 등록합니다.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>클라이언트 쪽 유효성 검사를 위한 IClientModelValidator

이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovie2` 특성에 사용됩니다. 이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:

* 사용자 지정 유효성 검사 특성에서 `IClientModelValidator` 인터페이스를 구현하고 `AddValidation` 메서드를 생성합니다. `AddValidation` 메서드에서 다음 예제와 같이 유효성 검사를 위한 `data-` 특성을 추가합니다.

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>클라이언트 쪽 유효성 검사를 사용하지 않도록 설정

다음 코드는 MVC 보기에 클라이언트 유효성 검사를 사용하지 않도록 설정합니다.

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

및 Razor 페이지:

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

클라이언트 유효성 검사를 사용하지 않도록 설정하는 또 다른 방법은 사용자의 *.cshtml* 파일에서 `_ValidationScriptsPartial` 참조를 주석으로 처리하는 것입니다.

## <a name="additional-resources"></a>추가 리소스

* [System.ComponentModel.DataAnnotations 네임스페이스](xref:System.ComponentModel.DataAnnotations)
* [모델 바인딩](model-binding.md)

::: moniker-end
