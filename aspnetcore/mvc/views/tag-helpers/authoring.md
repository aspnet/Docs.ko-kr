---
title: ASP.NET Core의 작성자 태그 도우미
author: rick-anderson
description: ASP.NET Core에서 태그 도우미를 작성하는 방법을 알아봅니다.
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e0c9f450e4eded49694cbbb0e9fa2614a221ab14
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586828"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>ASP.NET Core의 작성자 태그 도우미

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>태그 도우미 시작

이 자습서는 태그 도우미 프로그래밍에 대한 소개를 제공합니다. [태그 도우미 소개](intro.md)에서는 태그 도우미가 제공하는 이점에 대해 설명합니다.

태그 도우미는 `ITagHelper` 인터페이스를 구현하는 클래스입니다. 그러나 태그 도우미를 작성할 때 일반적으로 `TagHelper`에서 파생되므로 `Process` 메서드에 액세스할 수 있습니다.

1. **AuthoringTagHelpers** 라는 새로운 ASP.NET Core 프로젝트를 만듭니다. 이 프로젝트에 대한 인증이 필요하지 않습니다.

1. *TagHelpers* 라는 태그 도우미를 보관할 폴더를 만듭니다. *TagHelpers* 폴더는 필수는 *아니지만* 있는 것이 좋습니다. 이제 몇 가지 간단한 태그 도우미를 작성하는 것으로 시작해 보겠습니다.

## <a name="a-minimal-tag-helper"></a>최소한의 태그 도우미

이 섹션에서는 이메일 태그를 업데이트하는 태그 도우미를 작성합니다. 예를 들면 다음과 같습니다.

```html
<email>Support</email>
```

서버는 이메일 태그 도우미를 사용하여 태그를 다음으로 변환합니다.

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

즉, 이메일 링크로 만드는 앵커 태그. 블로그 엔진을 작성하고 마케팅, 지원 및 기타 연락처에 대한 이메일을 모두 동일한 도메인으로 보내는 데 필요한 경우 이 작업을 수행할 수 있습니다.

1. 다음 `EmailTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * 태그 도우미는 루트 클래스 이름의 요소를 대상으로 하는 명명 규칙을 사용합니다(클래스 이름의 *TagHelper* 부분 제외). 이 예에서 **EmailTagHelper** 의 루트 이름이 *email* 이므로 `<email>` 태그가 대상으로 지정됩니다. 이 명명 규칙은 대부분의 태그 도우미에서 작동하며, 나중에는 재정의하는 방법에 대해 설명하기로 합니다.

   * `EmailTagHelper` 클래스는 `TagHelper`에서 파생됩니다. `TagHelper` 클래스는 태그 도우미를 작성하기 위한 메서드 및 속성을 제공합니다.

   * 재정의된 `Process` 메서드는 실행될 때 태그 도우미가 수행하는 작업을 제어합니다. `TagHelper` 클래스는 동일한 매개 변수와 함께 비동기 버전(`ProcessAsync`)을 제공합니다.

   * `Process`(및 `ProcessAsync`)에 대한 컨텍스트 매개 변수는 현재 HTML 태그의 실행과 관련된 정보를 포함합니다.

   * `Process`(및 `ProcessAsync`)에 대한 출력 매개 변수는 HTML 태그 및 콘텐츠를 생성하는 데 사용된 원본 소스의 상태 저장 HTML 요소 표현을 포함합니다.

   * 클래스 이름에는 **TagHelper** 라는 접미사가 있으며 필수는 *아니지만* 관례적으로 사용됩니다. 다음과 같이 클래스를 선언할 수 있습니다.

   ```csharp
   public class Email : TagHelper
   ```

1. `EmailTagHelper`모든 보기에서 클래스를 사용할 수 있도록 하려면 Razor `addTagHelper` 다음 지시문을 *views/_ViewImports에 추가 합니다.*

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   위의 코드에서는 와일드 카드 구문을 사용하여 사용할 수 있는 모든 태그 도우미를 어셈블리에서 지정합니다. `@addTagHelper` 뒤의 첫 번째 문자열은 로드할 태그 도우미(모든 태그 도우미에 "*" 사용)를 지정하고 두 번째 문자열 "AuthoringTagHelpers"는 태그 도우미가 있는 어셈블리를 지정합니다. 또한 두 번째 줄은 와일드 카드 구문을 사용 하 여 ASP.NET Core MVC 태그 도우미에 표시 됩니다. 해당 도우미는 [태그 도우미 소개](intro.md)에 설명 되어 있습니다. 이 지시문은 `@addTagHelper` 태그 도우미를 뷰에서 사용할 수 있도록 하는 지시문입니다 Razor . 또는 아래 표시된 것처럼 태그 도우미의 FQN(정규화된 이름)을 제공할 수 있습니다.

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

FQN을 사용하여 뷰에 태그 도우미를 추가하려면 먼저 FQN(`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)을 추가한 후 **어셈블리 이름**(*AuthoringTagHelpers*, 반드시 `namespace`는 아님)을 추가합니다. 대부분의 개발자는 와일드 카드 구문을 사용하는 방법을 선호합니다. [태그 도우미 소개](intro.md)에서는 태그 도우미 추가, 제거, 계층 구조 및 와일드 카드 구문에 대해 자세히 설명합니다.

1. *Views/Home/Contact.cshtml* 파일에서 태그를 다음 변경 내용으로 업데이트합니다.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. 앱을 실행하고 선호하는 브라우저를 사용하여 HTML 소스를 보면 이메일 태그가 앵커 태그로 대체되었는지 확인할 수 있습니다(예: `<a>Support</a>`). *지원* 및 *마케팅* 은 링크로 렌더링되지만 작동하도록 하는 `href` 특성이 없습니다. 다음 섹션에서 이 문제를 해결합니다.

## <a name="setattribute-and-setcontent"></a>SetAttribute 및 SetContent

이 섹션에서는 `EmailTagHelper`를 업데이트하여 이메일에 대한 유효한 앵커 태그를 생성합니다. 이를 업데이트 하 여 Razor 보기 (특성 형식)에서 정보를 가져와 `mail-to` 앵커 생성에 사용 합니다.

`EmailTagHelper` 클래스를 다음으로 업데이트합니다.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* 태그 도우미에 대한 파스칼식 클래스 및 속성 이름이 [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)로 변환됩니다. 따라서 `MailTo` 특성을 사용하려면 `<email mail-to="value"/>`를 사용하는 것과 동일합니다.

* 마지막 줄은 최소로 작동하는 태그 도우미에 대한 완성된 콘텐츠를 설정합니다.

* 강조 표시된 줄은 특성 추가를 위한 구문을 보여 줍니다.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

이 방법은 특성 컬렉션에 현재 존재하지 않는 한, 속성 "href" 특성에 대해 작동합니다. 또한 `output.Attributes.Add` 메서드를 사용하여 태그 특성 컬렉션 끝에 태그 도우미 특성을 추가할 수도 있습니다.

1. *Views/Home/Contact.cshtml* 파일에서 태그를 다음 변경 내용으로 업데이트합니다.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. 앱을 실행하고 올바른 링크를 생성하는지 확인합니다.

<a name="self-closing"></a>

   > [!NOTE]
   > 이메일 태그 자체 닫음(`<email mail-to="Rick" />`)을 작성하려는 경우 최종 출력도 자체로 닫힙니다. 시작 태그(`<email mail-to="Rick">`)만 사용하여 태그를 작성하는 기능을 사용하려면 다음을 사용하여 클래스를 표시해야 합니다.
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   자체 닫음 이메일 태그 도우미를 사용하면 출력은 `<a href="mailto:Rick@contoso.com" />`이 됩니다. 자체 닫음 앵커 태그가 유효한 HTML이 아니므로 태그를 만들지는 않겠지만 자체 닫음 태그 도우미를 만들 수 있습니다. 태그 도우미는 태그를 읽은 후 `TagMode` 속성의 형식을 설정합니다.

### <a name="processasync"></a>ProcessAsync

이 섹션에서는 비동기 이메일 도우미를 작성합니다.

1. `EmailTagHelper` 클래스를 다음 코드로 바꿉니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **참고:**

   * 이 버전은 비동기 `ProcessAsync` 메서드를 사용합니다. 비동기 `GetChildContentAsync`는 `TagHelperContent`가 포함된 `Task`를 반환합니다.

   * `output` 매개 변수를 사용하여 HTML 요소의 내용을 가져옵니다.

1. 태그 도우미가 대상 이메일을 가져올 수 있도록 *Views/Home/Contact.cshtml* 파일을 다음과 같이 변경합니다.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. 앱을 실행하고 유효한 이메일 링크를 생성하는지 확인합니다.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent 및 PostContent.SetHtmlContent

1. 다음 `BoldTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * `[HtmlTargetElement]` 특성은 "bold"라는 HTML 특성이 포함된 HTML 요소가 일치하고 클래스에서 `Process` 재정의 메서드가 실행되도록 지정하는 특성 매개 변수를 전달합니다. 이 샘플에서 `Process` 메서드는 "bold" 특성을 제거하고 포함된 태그를 `<strong></strong>`으로 둘러쌉니다.

   * 기존 태그 내용을 바꾸지 않기 때문에 `PreContent.SetHtmlContent` 메서드로 여는 `<strong>` 태그를 작성하고 `PostContent.SetHtmlContent` 메서드로 닫는 `</strong>` 태그를 작성해야 합니다.

1. `bold` 특성 값을 포함하도록 *About.cshtml* 뷰를 수정합니다. 완성된 코드는 다음과 같습니다.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. 앱을 실행합니다. 선호하는 브라우저를 사용하여 소스를 검사하고 태그를 확인할 수 있습니다.

   위의 `[HtmlTargetElement]` 특성은 "bold" 특성 이름을 제공하는 HTML 태그만 대상으로 합니다. `<bold>` 요소는 태그 도우미에 의해 수정되지 않았습니다.

1. `[HtmlTargetElement]` 특성 줄을 주석 처리하고 기본적으로 `<bold>` 태그를 대상으로 합니다. 즉, `<bold>` 형식의 HTML 태그입니다. 기본 명명 규칙은 클래스 이름 **Bold** TagHelper를 `<bold>` 태그와 일치시킨다는 점을 유의합니다.

1. 앱을 실행하고 `<bold>` 태그가 태그 도우미에 의해 처리되는지 확인합니다.

클래스를 여러 개의 `[HtmlTargetElement]` 특성으로 데코레이팅하면 그 결과는 대상의 논리 OR입니다. 예를 들어 아래 코드를 사용하여 bold 태그 또는 bold 특성을 일치시킵니다.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

동일한 문에 특성이 여러 개 추가된 경우, 런타임에서 논리 AND로 처리합니다. 예를 들어 아래 코드에서는 일치시킬 "bold"(`<bold bold />`) 특성을 사용하여 HTML 요소 이름을 "bold"로 지정해야 합니다.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

또는 `[HtmlTargetElement]`를 사용하여 대상 요소의 이름을 변경할 수 있습니다. 예를 들어 `BoldTagHelper`가 `<MyBold>` 태그를 대상으로 지정하도록 하려면 다음 특성을 사용합니다.

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>모델을 태그 도우미에 전달

1. *모델* 폴더를 추가합니다.

1. 다음 `WebsiteContext` 클래스를 *Models* 폴더에 추가합니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. 다음 `WebsiteInformationTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * 앞에서 언급했듯이, 태그 도우미는 파스칼식 C# 클래스 이름과 태그 도우미 속성을 [kebab case](https://wiki.c2.com/?KebabCase)로 변환합니다. 따라서에서를 사용 하려면 `WebsiteInformationTagHelper` 를 Razor 작성 `<website-information />` 합니다.

   * `[HtmlTargetElement]` 특성을 사용하여 대상 요소를 명시적으로 식별하지 않으므로 `website-information`의 기본값이 대상으로 지정됩니다. 다음 특성을 적용한 경우(kebab 대/소문자는 아니지만 클래스 이름 일치):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   kebab case 태그 `<website-information />`은 일치하지 않습니다. `[HtmlTargetElement]` 특성을 사용하려는 경우 아래와 같이 kebab 대/소문자를 사용합니다.

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * 자체 닫음 요소에는 내용이 없습니다. 이 예제에서 태그는 Razor 자체 닫는 태그를 사용 하지만, 태그 도우미는 [section](https://www.w3.org/TR/html5/sections.html#the-section-element) 요소를 만듭니다 .이 요소는 자체 닫히지 않으며 요소 내에 콘텐츠를 작성 하는 것입니다 `section` . 따라서 `TagMode`를 `StartTagAndEndTag`로 설정하여 출력을 작성해야 합니다. 또는 `TagMode` 설정 줄을 주석 처리하고 닫는 태그로 태그를 작성할 수 있습니다. (예제 태그는 이 자습서의 뒷부분에 제공됩니다.)

   * 다음 줄에서 `$`(달러 기호)는 [보간된 문자열](/dotnet/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. 다음 태그를 *About.cshtml* 뷰에 추가합니다. 강조 표시된 태그는 웹 사이트 정보를 표시합니다.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > Razor아래 표시 된 태그에서:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor 특성은 `info` 문자열이 아니라 클래스 이며 c # 코드를 작성 하려고 합니다. 모든 문자열이 아닌 태그 도우미 특성이 `@` 문자 없이 작성됩니다.

1. 앱을 실행하고 About 뷰로 이동하여 웹 사이트 정보를 확인합니다.

   > [!NOTE]
   > 닫는 태그로 다음 태그를 사용하고 태그 도우미에서 `TagMode.StartTagAndEndTag`로 해당 줄을 제거할 수 있습니다.
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>조건 태그 도우미

조건 태그 도우미는 true 값을 전달한 경우 출력을 렌더링합니다.

1. 다음 `ConditionTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. *Views/Home/Index.cshtml* 파일의 내용을 다음 태그로 대체합니다.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. `Home` 컨트롤러에서 `Index` 메서드를 다음 코드로 바꿉니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. 앱을 실행하고 홈 페이지로 이동합니다. 조건부 `div`의 태그가 렌더링되지 않습니다. 쿼리 문자열 `?approved=true`를 URL에 추가합니다(예: `http://localhost:1235/Home/Index?approved=true`). `approved`가 true로 설정되고 조건부 태그가 표시됩니다.

> [!NOTE]
> [nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자를 사용하여 bold 태그 도우미에서와 마찬가지로 문자열을 지정하지 않고 대상에 특성을 지정합니다.
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> [nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자는 리팩토링해야 하는 코드를 보호합니다(이름을 `RedCondition`으로 변경할 수 있음).

### <a name="avoid-tag-helper-conflicts"></a>태그 도우미 충돌 방지

이 섹션에서는 자동 링크 태그 도우미 쌍을 작성합니다. 첫 번째는 HTTP로 시작하는 URL을 포함하는 태그를 동일한 URL을 포함하는 HTML 앵커 태그로 대체합니다(따라서 URL에 대한 링크를 생성함). 두 번째는 WWW로 시작하는 URL에 대해 동일한 작업을 수행합니다.

이러한 두 도우미는 밀접하게 관련되어 있으며 향후 리팩터링할 수 있으므로 동일한 파일에 유지합니다.

1. 다음 `AutoLinkerHttpTagHelper` 클래스를 *TagHelpers* 폴더에 추가합니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >`AutoLinkerHttpTagHelper` 클래스는 `p` 요소를 대상으로 하고 [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference)를 사용하여 앵커를 만듭니다.

1. *Views/Home/Contact.cshtml* 파일 끝에 다음 마크업을 추가합니다.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. 앱을 실행하고 태그 도우미가 앵커를 제대로 렌더링하는지 확인합니다.

1. www 텍스트를 원본 www 텍스트를 포함하는 앵커 태그로 변환할 `AutoLinkerWwwTagHelper`를 포함하도록 `AutoLinker` 클래스를 업데이트합니다. 업데이트된 코드는 아래에 강조 표시됩니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. 앱을 실행합니다. www 텍스트는 링크로 렌더링되지만 HTTP 텍스트는 그렇지 않습니다. 두 클래스 모두에 중단점을 넣으면 HTTP 태그 도우미 클래스가 먼저 실행되는 것을 확인할 수 있습니다. 문제는 태그 도우미 출력이 캐시된 후 WWW 태그 도우미가 실행될 때, HTTP 태그 도우미의 캐시된 출력을 덮어쓴다는 것입니다. 자습서의 뒷부분에서 태그 도우미가 실행되는 순서를 제어하는 방법을 살펴 보겠습니다. 코드를 다음과 같이 수정합니다.

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > 자동 링크 태그 도우미의 첫 번째 버전에서는 다음 코드를 사용하여 대상의 내용을 얻었습니다.
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > 즉, `ProcessAsync` 메서드에 전달된 `TagHelperOutput`을 사용하여 `GetChildContentAsync`를 호출합니다. 이전에 언급한 것처럼, 출력이 캐시되므로 실행할 마지막 태그 도우미가 우선합니다. 다음 코드로 문제를 해결했습니다.
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > 위의 코드는 내용이 수정되었는지 확인하고, 수정되었으면 출력 버퍼에서 내용을 가져옵니다.

1. 앱을 실행하고 두 링크가 예상대로 작동하는지 확인합니다. 자동 링커 태그 도우미가 정확하고 완전한 것으로 보일 수 있지만 미묘한 문제가 있습니다. WWW 태그 도우미가 먼저 실행되면 www 링크는 올바르지 않습니다. 태그가 실행되는 순서를 제어하기 위해 `Order` 오버로드를 추가하여 코드를 업데이트합니다. `Order` 속성은 동일한 요소를 대상으로 하는 다른 태그 도우미를 기준으로 실행 순서를 결정합니다. 기본 순서 값은 0이고 값이 낮은 인스턴스가 먼저 실행됩니다.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   위의 코드는 HTTP 태그 도우미가 WWW 태그 도우미보다 먼저 실행되도록 보장합니다. `Order`를 `MaxValue`로 변경하고 WWW 태그에 대해 보장된 태그가 잘못되었는지 확인합니다.

## <a name="inspect-and-retrieve-child-content"></a>자식 콘텐츠 검사 및 검색

태그 도우미는 콘텐츠를 검색하기 위한 여러 가지 속성을 제공합니다.

* `GetChildContentAsync`의 결과는 `output.Content`에 추가할 수 있습니다.
* `GetContent`로 `GetChildContentAsync`의 결과를 검사할 수 있습니다.
* `output.Content`를 수정하고 `GetChildContentAsync`를 자동 링커 샘플에서처럼 호출하지 않는 경우, TagHelper 본문이 실행 또는 렌더링되지 않습니다.

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* `GetChildContentAsync`를 여러 번 호출해도 같은 값이 반환되며 캐시된 결과를 사용하지 않음을 나타내는 false 매개 변수를 전달하지 않은 경우 `TagHelper` 본문을 다시 실행하지 않습니다.

## <a name="load-minified-partial-view-taghelper"></a>축소된 부분 보기 TagHelper 로드

프로덕션 환경에서는 축소된 부분 보기를 로드하여 성능을 향상할 수 있습니다. 프로덕션에서 축소된 부분 보기를 활용하려면 다음을 수행하세요.

* 부분 보기를 축소하는 사전 빌드 프로세스를 만들거나 설정합니다.
* 다음 코드를 사용하여 개발 환경이 아닌 환경에서 축소된 부분 보기를 로드합니다.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
