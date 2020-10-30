---
title: ASP.NET Core 형식의 태그 도우미
author: rick-anderson
description: 형식과 함께 사용되는 기본 제공 태그 도우미를 설명합니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: a568ad481eb09587e2ddce8e84d0ac3eff01e990
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060900"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>ASP.NET Core 형식의 태그 도우미

작성자 [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) 및 [Jerrie Pelser](https://github.com/jerriep)

이 문서에서는 형식 및 형식에서 일반적으로 사용되는 HTML 요소 작업을 보여줍니다. HTML [형식](https://www.w3.org/TR/html401/interact/forms.html) 요소는 서버에 데이터를 다시 게시하는 기본 메커니즘 웹앱을 사용하도록 제공합니다. 이 문서에서는 대부분 [태그 도우미](tag-helpers/intro.md) 및 강력한 HTML 형식을 생산적으로 만들 수 있는 방법을 설명합니다. 이 문서를 읽기 전에 [태그 도우미 소개](tag-helpers/intro.md)를 참조하세요.

많은 경우 HTML 도우미는 특정 태그 도우미에 대한 대체 방법을 제공하지만, 태그 도우미는 HTML 도우미를 대체하지 않으며 각 HTML 도우미에 대한 태그 도우미가 없다는 사실을 인지하는 것이 중요합니다. HTML 도우미 대안이 있다면 그 내용도 다룹니다.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>형식 태그 도우미

[Form](https://www.w3.org/TR/html401/interact/forms.html) 태그 도우미는 다음과 같습니다.

* [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` MVC 컨트롤러 작업 또는 명명 된 경로에 대 한 HTML 특성 값을 생성 합니다.

* 사이트 간 요청 위조를 방지하기 위해 숨겨진 [요청 확인 토큰](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)을 만듭니다(HTTP Post 작업 메서드에서 `[ValidateAntiForgeryToken]` 특성과 함께 사용할 경우).

* `asp-route-<Parameter Name>` 특성을 제공합니다. 여기서 `<Parameter Name>`을 경로 값에 추가합니다. `Html.BeginForm` 및 `Html.BeginRouteForm`에 대한 `routeValues` 매개 변수는 유사한 기능을 제공합니다.

* HTML 도우미 대안 `Html.BeginForm` 및 `Html.BeginRouteForm`가 있습니다.

샘플:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

위의 형식 태그 도우미에서는 다음 HTML을 생성합니다.

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

MVC 런타임은 형식 태그 도우미 특성 `asp-controller` 및 `asp-action`에서 `action` 특성 값을 만듭니다. 형식 태그 도우미도 사이트 간 요청 위조를 방지하기 위해 숨겨진 [요청 확인 토큰](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)을 만듭니다(HTTP Post 작업 메서드에서 `[ValidateAntiForgeryToken]` 특성과 함께 사용할 경우). 사이트 간 요청 위조로부터 순수한 HTML 형식을 보호하기는 어렵습니다. 형식 태그 도우미는 이러한 서비스를 제공합니다.

### <a name="using-a-named-route"></a>명명된 경로 사용

`asp-route` 태그 도우미 특성은 HTML `action` 특성에 대한 태그를 만들 수도 있습니다. `register`라는 [경로](../../fundamentals/routing.md)를 사용하는 앱은 등록 페이지에 다음 태그를 사용할 수 있습니다.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

*보기/계정* 폴더의 보기 중 다수( *개별 사용자 계정* 을 사용하여 새로운 웹앱을 만들 때 생성됨)에는 [asp-route-returnurl](xref:mvc/views/working-with-forms) 특성이 포함됩니다.

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>기본 제공된 템플릿을 사용하면 권한이 부여된 리소스에 액세스하려고 하지만 인증되거나 권한이 없는 경우에만 `returnUrl`이 자동으로 채워집니다. 권한이 없는 액세스를 시도하는 경우 보안 미들웨어는 사용자를 `returnUrl` 설정이 포함된 로그인 페이지에 리디렉션합니다.

## <a name="the-form-action-tag-helper"></a>양식 작업 태그 도우미

양식 작업 태그 도우미는 생성된 `<button ...>` 또는 `<input type="image" ...>` 태그의 `formaction` 특성을 생성합니다. `formaction` 특성은 양식이 해당 데이터를 제출하는 위치를 제어합니다. 이 클래스 [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 는 형식 및 요소의 요소에 바인딩됩니다 `image` [\<button>](https://www.w3.org/wiki/HTML/Elements/button) . 양식 작업 태그 도우미를 통해 여러 개의 [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` 특성을 사용하여 해당 요소에 대해 생성되는 `formaction` 링크를 제어할 수 있습니다.

`formaction`의 값을 제어하기 위해 지원되는 [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 특성:

|attribute|설명|
|---|---|
|[asp-controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|컨트롤러의 이름입니다.|
|[asp-action](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|작업 메서드의 이름입니다.|
|[asp-area](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|영역의 이름입니다.|
|[asp-page](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|페이지의 이름 Razor 입니다.|
|[asp-page-handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|페이지 처리기의 이름 Razor 입니다.|
|[asp-route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|경로의 이름입니다.|
|[asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|단일 URL 경로 값입니다. 예: `asp-route-id="1234"`.|
|[asp-all-route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|모든 경로 값입니다.|
|[asp-fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|URL 조각입니다.|

### <a name="submit-to-controller-example"></a>컨트롤러에 제출 예제

다음 태그는 입력 또는 단추가 선택될 때 `HomeController`의 `Index` 작업에 양식을 제출합니다.

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

위의 태그는 다음 HTML을 생성합니다.

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>페이지에 제출 예제

다음 태그는 폼을 페이지에 제출 합니다 `About` Razor .

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

위의 태그는 다음 HTML을 생성합니다.

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>경로에 제출 예제

`/Home/Test` 엔드포인트를 살펴보겠습니다.

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

다음 태그는 `/Home/Test` 엔드포인트에 양식을 제출합니다.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

위의 태그는 다음 HTML을 생성합니다.

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>입력 태그 도우미

입력 태그 도우미는 [\<input>](https://www.w3.org/wiki/HTML/Elements/input) razor 뷰의 모델 식에 HTML 요소를 바인딩합니다.

구문

```cshtml
<input asp-for="<Expression Name>">
```

입력 태그 도우미:

* `asp-for` 특성에 지정된 식 이름에 대해 `id` 및 `name` HTML 특성을 만듭니다. `asp-for="Property1.Property2"`은 `m => m.Property1.Property2`와 동등합니다. 식의 이름은 `asp-for` 특성 값에 사용됩니다. 추가 정보는 [식 이름](#expression-names) 섹션을 참조하세요.

* 모델 속성에 적용된 모델 형식 및 [데이터 주석](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) 특성에 따라 HTML `type` 특성 값을 설정합니다.

* HTML `type` 특성 값이 지정 된 경우 덮어쓰지 않습니다.

* 모델 속성에 적용되는 [데이터 주석](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) 특성에서 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) 유효성 검사 특성을 만듭니다.

* `Html.TextBoxFor` 및 `Html.EditorFor`와 HTML 도우미 기능이 겹칩니다. 자세한 내용은 **입력 태그 도우미에 대한 HTML 도우미 대안** 섹션을 참조하세요.

* 강력한 형식 지정을 제공합니다. 속성의 이름이 변경되고 태그 도우미를 업데이트하지 않은 경우 다음과 비슷한 오류가 표시됩니다.

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

`Input` 태그 도우미는 .NET 형식을 기반으로 HTML `type` 특성을 설정합니다. 다음 표에서는 몇 가지 일반적인 .NET 형식 및 생성된 HTML 형식을 나열합니다(.NET 형식의 일부만 나열됨).

|.NET 형식|입력 형식|
|---|---|
|Bool|type="checkbox"|
|String|type="text"|
|DateTime|type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|type="number"|
|Int|type="number"|
|Single, Double|type="number"|

다음 표에서는 입력 태그 도우미가 특정 입력 형식에 매핑되는 몇 가지 일반적인 [데이터 주석](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) 특성을 보여줍니다(유효성 검사 특성의 일부만 나열됨).

|attribute|입력 형식|
|---|---|
|[EmailAddress]|type="email"|
|[Url]|type="url"|
|[HiddenInput]|type="hidden"|
|[Phone]|type="tel"|
|[DataType(DataType.Password)]|type="password"|
|[DataType(DataType.Date)]|type="date"|
|[DataType(DataType.Time)]|type="time"|

샘플:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

위의 코드는 다음과 같은 HTML을 생성합니다.

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

`Email` 및 `Password` 속성에 적용할 데이터 주석은 모델에서 메타데이터를 생성합니다. 입력 태그 도우미는 모델 메타데이터를 사용하고 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` 특성을 생성합니다([모델 유효성 검사](../models/validation.md) 참조). 이러한 특성에서는 입력 필드에 연결할 유효성 검사기를 설명합니다. 이 기능은 비간섭 HTML5 및 [jQuery](https://jquery.com/) 유효성 검사를 제공합니다. 가 중이 아닌 특성에는 형식이 있습니다 `data-val-rule="Error Message"` . 여기서 rule은 유효성 검사 규칙의 이름 (예:,, 등 `data-val-required` `data-val-email` )입니다 `data-val-maxlength` . 특성에 오류 메시지가 제공 되 면 특성에 대 한 값으로 표시 됩니다 `data-val-rule` . 또한 규칙에 대한 추가 세부 정보를 제공하는 `data-val-ruleName-argumentName="argumentValue"` 형식의 특성이 있습니다(예: `data-val-maxlength-max="1024"`).

### <a name="html-helper-alternatives-to-input-tag-helper"></a>입력 태그 도우미에 대한 HTML 도우미 대안

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` 및 `Html.EditorFor`에는 입력 태그 도우미와 겹치는 기능이 있습니다. 입력 태그 도우미는 `type` 특성을 자동으로 설정하는 반면 `Html.TextBox` 및 `Html.TextBoxFor`는 설정하지 않습니다. `Html.Editor` 및 `Html.EditorFor`는 컬렉션, 복잡한 개체 및 템플릿을 처리하는 반면 입력 태그 도우미는 처리하지 않습니다. 입력 태그 도우미인 `Html.EditorFor` 및 `Html.TextBoxFor`은 강력한 형식이 지정(람다 식 사용)되는 반면 `Html.TextBox` 및 `Html.Editor`는 지정되지 않습니다(식 이름 사용).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` 및 `@Html.EditorFor()`는 해당 기본 템플릿을 실행할 때 `htmlAttributes`라는 특수한 `ViewDataDictionary` 항목을 사용합니다. 이 동작은 필요에 따라 `additionalViewData` 매개 변수를 사용하여 확대됩니다. "htmlAttributes" 키는 대/소문자를 구분합니다. "htmlAttributes" 키는 `@Html.TextBox()`와 같은 입력 도우미에 전달된 `htmlAttributes` 개체와 유사하게 처리됩니다.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>식 이름

`asp-for` 특성 값은 `ModelExpression`이며 람다 식의 오른쪽입니다. 따라서 `asp-for="Property1"`은 생성된 코드에서 `m => m.Property1`이 됩니다. 따라서 `Model`과 함께 접두사로 사용할 필요가 없습니다. “\@” 문자를 사용하여 인라인 식을 시작하고 `m.` 앞으로 이동할 수 있습니다.

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

다음 항목을 생성합니다.

```html
<input type="text" id="joe" name="joe" value="Joe">
```

컬렉션 속성을 가진 `asp-for="CollectionProperty[23].Member"`은 `i`에 `23` 값이 포함될 경우 `asp-for="CollectionProperty[i].Member"`와 동일한 이름을 생성합니다.

ASP.NET Core MVC가 `ModelExpression`의 값을 계산하는 경우 `ModelState`를 비롯한 여러 원본을 검사합니다. `<input type="text" asp-for="@Name">`을 고려합니다. 계산된 `value` 특성은 첫 번째 null이 아닌 값입니다.

* "Name" 키를 가진 `ModelState` 항목입니다.
* 식 `Model.Name`의 결과입니다.

### <a name="navigating-child-properties"></a>자식 속성 탐색

보기 모델의 속성 경로를 사용하여 자식 속성을 탐색할 수 있습니다. 자식 `Address` 속성을 포함하는 보다 복잡한 모델 클래스를 사용해보세요.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

보기에서 `Address.AddressLine1`에 바인딩합니다.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

다음 HTML이 `Address.AddressLine1`에 생성됩니다.

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>식 이름 및 컬렉션

샘플, `Colors`의 배열을 포함하는 모델은 다음과 같습니다.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

작업 방법:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

다음은 Razor 특정 요소에 액세스 하는 방법을 보여 줍니다 `Color` .

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

*Views/Shared/EditorTemplates/String.cshtml* 템플릿:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

`List<T>`를 사용하는 샘플:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

다음은 Razor 컬렉션을 반복 하는 방법을 보여 줍니다.

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

*Views/Shared/EditorTemplates/ToDoItem.cshtml* 템플릿:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

값이 `asp-for` 또는 `Html.DisplayFor` 해당 컨텍스트에서 사용될 때 가능한 경우 `foreach`를 사용해야 합니다. 일반적으로, `for`는 열거자를 할당할 필요가 없으므로 `foreach`보다 좋습니다(시나리오에서 허용하는 경우). 그러나 LINQ 식에서 인덱서를 평가하는 작업은 비용이 많이 들기 때문에 최소화해야 합니다.

&nbsp;

>[!NOTE]
>위의 주석 처리된 코드 샘플은 람다 식을 `@` 연산자와 바꿔서 목록에 있는 각 `ToDoItem`에 액세스하는 방법을 보여줍니다.

## <a name="the-textarea-tag-helper"></a>텍스트 영역 태그 도우미

`Textarea Tag Helper` 태그 도우미는 입력 태그 도우미와 비슷합니다.

* `id` `name` 요소에 대 한 모델에서 및 특성과 데이터 유효성 검사 특성을 생성 합니다 [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) .

* 강력한 형식 지정을 제공합니다.

* HTML 도우미 대안: `Html.TextAreaFor`

샘플:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

다음 HTML이 생성됩니다.

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>레이블 태그 도우미

* `for` [\<label>](https://www.w3.org/wiki/HTML/Elements/label) 식 이름에 대해 요소에서 레이블 캡션과 특성을 생성 합니다.

* HTML 도우미 대안: `Html.LabelFor`

`Label Tag Helper`는 HTML 레이블 요소를 통해 다음과 같은 이점을 제공합니다.

* `Display` 특성에서 설명 레이블을 값을 자동으로 가져옵니다. 의도한 표시 이름은 시간에 따라 변경될 수 있고, `Display` 특성 및 레이블 태그 도우미의 조합은 사용되는 모든 곳에서 `Display`을 적용합니다.

* 소스 코드의 간단한 태그

* 모델 속성을 사용하는 강력한 형식화입니다.

샘플:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

다음 HTML이 `<label>` 요소에 생성됩니다.

```html
<label for="Email">Email Address</label>
```

레이블 태그 도우미는 "이메일"의 `for` 특성 값을 생성했습니다. 이 값은 `<input>` 요소와 연결된 ID입니다. 태그 도우미는 일관된 `id` 및 `for` 요소를 제대로 연결할 수 있도록 생성합니다. 이 샘플의 캡션은 `Display` 특성에서 제공됩니다. 모델에 `Display` 특성이 포함되지 않는 경우 캡션은 식의 속성 이름입니다.

## <a name="the-validation-tag-helpers"></a>유효성 검사 태그 도우미

두 개의 유효성 검사 태그 도우미가 있습니다. `Validation Message Tag Helper`(모델의 단일 속성에 대한 유효성 검사 메시지 표시) 및 `Validation Summary Tag Helper`(유효성 검사 오류의 요약 표시)입니다. `Input Tag Helper`는 모델 클래스의 데이터 주석 특성에 따라 입력 요소에 HTML5 클라이언트 쪽 유효성 검사 특성을 추가합니다. 유효성 검사도 서버에서 수행됩니다. 유효성 검사 태그 도우미는 유효성 검사 오류가 발생하는 경우 이러한 오류 메시지를 표시합니다.

### <a name="the-validation-message-tag-helper"></a>유효성 검사 메시지 태그 도우미

* [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` 특성을 [범위](https://developer.mozilla.org/docs/Web/HTML/Element/span) 요소에 추가합니다. 그러면 지정된 모델 속성의 입력 필드에서 유효성 검사 오류 메시지를 표시합니다. 클라이언트 쪽 유효성 검사 오류가 발생할 때 [jQuery](https://jquery.com/)는 `<span>` 요소에서 오류 메시지를 표시합니다.

* 유효성 검사도 서버에서 수행됩니다. 클라이언트는 JavaScript를 사용하지 않도록 설정할 수 있고 일부 유효성 검사는 서버 쪽에서만 수행될 수 있습니다.

* HTML 도우미 대안: `Html.ValidationMessageFor`

`Validation Message Tag Helper`는 HTML [범위](https://developer.mozilla.org/docs/Web/HTML/Element/span) 요소에서 `asp-validation-for` 특성과 함께 사용됩니다.

```cshtml
<span asp-validation-for="Email"></span>
```

유효성 검사 메시지 태그 도우미는 다음 HTML을 생성합니다.

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

일반적으로 동일한 속성에서 `Input` 태그 도우미 이후에 `Validation Message Tag Helper`를 사용합니다. 이렇게 하면 오류가 발생하는 입력 주변에서 유효성 검사 오류 메시지가 표시됩니다.

> [!NOTE]
> 클라이언트 쪽 유효성 검사 대신 올바른 JavaScript 및 [jQuery](https://jquery.com/) 스크립트 참조를 사용하는 보기가 있어야 합니다. 자세한 내용은 [모델 유효성 검사](../models/validation.md) 를 참조 하세요.

서버 쪽 유효성 검사 오류가 발생하는 경우(예: 사용자 지정 서버 쪽 유효성 검사 또는 클라이언트 쪽 유효성 검사를 사용하지 않는 경우) MVC는 해당 오류 메시지를 `<span>` 요소의 본문으로 배치합니다.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>유효성 검사 요약 태그 도우미

* `asp-validation-summary` 특성이 있는 `<div>` 요소를 대상으로 지정합니다.

* HTML 도우미 대안: `@Html.ValidationSummary`

`Validation Summary Tag Helper`는 유효성 검사 메시지의 요약 정보를 표시하는 데 사용됩니다. `asp-validation-summary` 특성 값은 다음 중 하나일 수 있습니다.

|asp-validation-summary|표시되는 유효성 검사 메시지|
|--- |--- |
|ValidationSummary.All|속성 및 모델 수준|
|ValidationSummary.ModelOnly|모델|
|ValidationSummary.None|없음|

### <a name="sample"></a>예제

다음 예제에서 데이터 모델에는 `DataAnnotation` 특성이 있습니다. 이 특성은 `<input>` 요소에 대한 유효성 검사 오류 메시지를 생성합니다.  유효성 검사 오류가 발생하는 경우 유효성 검사 태그 도우미는 다음 오류 메시지를 표시합니다.

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

생성된 HTML(모델은 유효한 경우):

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>선택 태그 도우미

* 모델의 속성에 대한 [선택](https://www.w3.org/wiki/HTML/Elements/select) 및 관련된 [옵션](https://www.w3.org/wiki/HTML/Elements/option) 요소를 생성합니다.

* HTML 도우미 대안 `Html.DropDownListFor` 및 `Html.ListBoxFor`가 있습니다.

`Select Tag Helper` `asp-for`는 [선택](https://www.w3.org/wiki/HTML/Elements/select) 요소에 대한 모델 속성 이름을 지정하고 `asp-items`는 [옵션](https://www.w3.org/wiki/HTML/Elements/option) 요소를 지정합니다.  다음은 그 예입니다.

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

샘플:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

`Index` 메서드는 `CountryViewModel`를 초기화하고, 선택한 국가를 설정하고, `Index` 보기에 전달합니다.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

HTTP POST `Index` 메서드는 선택 항목을 표시합니다.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index` 보기:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

다음 HTML을 생성합니다("CA"를 선택함).

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> 선택 태그 도우미와 함께 `ViewBag` 또는 `ViewData`를 사용하는 것이 좋습니다. 보기 모델은 일반적으로 더 강력하고 문제가 적은 방식으로 MVC 메타데이터를 제공합니다.

`asp-for` 특성 값은 특별한 경우이며 다른 태그 도우미 특성(예: `asp-items`)과 달리 `Model` 접두사를 필요로 하지 않습니다.

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>열거형 바인딩

`enum` 속성과 함께 `<select>`를 사용하고 `enum` 값에서 `SelectListItem` 요소를 생성하는 것이 편리합니다.

샘플:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

`GetEnumSelectList` 메서드는 열거형에 대해 `SelectList` 개체를 생성합니다.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

`Display` 특성으로 열거자 목록을 표시하여 보다 풍부한 UI를 사용할 수 있습니다.

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

다음 HTML이 생성됩니다.

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>옵션 그룹

HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) 요소는 뷰 모델에 하나 이상의 개체가 포함 된 경우에 생성 됩니다 `SelectListGroup` .

`CountryViewModelGroup`은 `SelectListItem` 요소를 "북아메리카" 및 "유럽" 그룹으로 그룹화합니다.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

두 개의 그룹은 다음과 같습니다.

![옵션 그룹 예제](working-with-forms/_static/grp.png)

생성되는 HTML은 다음과 같습니다.

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>다중 선택

`asp-for` 특성에 지정된 속성이 `IEnumerable`인 경우 태그 선택 도우미는 [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) 특성을 자동으로 생성합니다. 예를 들어, 다음과 같은 모델을 가정합니다.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

다음 보기에서:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

다음 HTML을 생성합니다.

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>선택 영역 없음

여러 페이지에서 "지정 안 됨" 옵션을 사용하는 경우 HTML의 반복을 제거하는 템플릿을 만들 수 있습니다.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

*Views/Shared/EditorTemplates/CountryViewModel.cshtml* 템플릿:

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) 요소를 추가 하는 경우는 *선택 하지 않는* 것으로 제한 되지 않습니다. 예를 들어 다음과 같은 보기 및 작업 메서드는 위의 코드와 유사한 HTML을 생성합니다.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

현재 `Country` 값에 따라 올바른 `<option>` 요소가 선택됩니다(`selected="selected"` 특성 포함).

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/views/tag-helpers/intro>
* [HTML 양식 요소](https://www.w3.org/TR/html401/interact/forms.html)
* [요청 확인 토큰](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [IAttributeAdapter 인터페이스](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [이 문서의 코드 조각](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
