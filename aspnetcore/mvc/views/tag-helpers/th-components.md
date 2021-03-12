---
title: ASP.NET Core의 태그 도우미 구성 요소
author: scottaddie
description: 태그 도우미 구성 요소란 무엇이며 ASP.NET Core에서 어떻게 사용하는지 알아봅니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: fb0bda0cf8d225df4c58ae43f81ed0dce10c1adc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587153"
---
# <a name="tag-helper-components-in-aspnet-core"></a>ASP.NET Core의 태그 도우미 구성 요소

작성자: [Scott Addie](https://twitter.com/Scott_Addie), [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

태그 도우미 구성 요소는 서버 쪽 코드에서 HTML 요소를 조건부로 수정하거나 추가할 수 있도록 하는 태그 도우미입니다. 이 기능은 ASP.NET Core 2.0 이상에서 사용할 수 있습니다.

ASP.NET Core에는 두 개의 기본 제공 태그 도우미 구성 요소, 즉 `head` 및 `body`가 포함되어 있습니다. <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers>네임 스페이스는 네임 스페이스에 있으며 MVC와 페이지에서 모두 사용할 수 있습니다 Razor . 태그 도우미 구성 요소는 *_ViewImports.cshtml* 에서 앱을 등록할 필요가 없습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>사용 사례

태그 도우미 구성 요소의 두 가지 일반적인 사용 사례는 다음과 같습니다.

1. [에를 삽입 `<link>` `<head>` 합니다.](#inject-into-html-head-element)
1. [에를 삽입 `<script>` `<body>` 합니다.](#inject-into-html-body-element)

다음 섹션에서는 이 사용 사례를 설명합니다.

### <a name="inject-into-html-head-element"></a>HTML 헤드 요소에 주입

HTML `<head>` 요소 내에서 CSS 파일은 일반적으로 HTML `<link>` 요소로 가져옵니다. 다음 코드는 `head` 태그 도우미 구성 요소를 사용하여 `<head>` 요소에 `<link>` 요소를 주입합니다.

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

위의 코드에서

* `AddressStyleTagHelperComponent`는 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>를 구현합니다. 추상은 다음과 같습니다.
  * <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>를 사용한 클래스 초기화를 허용합니다.
  * 태그 도우미 구성 요소를 사용하여 HTML 요소를 추가하거나 수정할 수 있게 합니다.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> 속성은 구성 요소가 렌더링되는 순서를 정의합니다. 앱에서 태그 도우미 구성 요소가 여러 번 사용되는 경우 `Order`가 필요합니다.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*>는 실행 컨텍스트의 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> 속성 값을 `head`와 비교합니다. 비교가 true로 평가되면 `_style` 필드의 내용이 HTML `<head>` 요소에 주입됩니다.

### <a name="inject-into-html-body-element"></a>HTML 본문 요소에 주입

`body` 태그 도우미 구성 요소는 `<body>` 요소에 `<script>` 요소를 주입할 수 있습니다. 다음 코드에서는 이 기술을 보여 줍니다.

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

별도의 HTML 파일이 `<script>` 요소를 저장하는 데 사용됩니다. HTML 파일은 코드를 더 깔끔하고 유지 관리가 가능하도록 만듭니다. 위의 코드는 *TagHelpers/Templates/AddressToolTipScript.html* 의 내용을 읽고 태그 도우미 출력으로 추가합니다. *AddressToolTipScript.html* 파일에는 다음 태그가 포함됩니다.

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

위의 코드는 [부트스트랩 도구 설명 위젯](https://getbootstrap.com/docs/3.3/javascript/#tooltips)을 `printable` 특성을 포함하는 `<address>` 요소에 바인드합니다. 요소 위에 마우스 포인터를 놓으면 효과가 표시됩니다.

## <a name="register-a-component"></a>구성 요소 등록

태그 도우미 구성 요소를 앱의 태그 도우미 구성 요소 컬렉션에 추가해야 합니다. 컬렉션에 추가하는 세 가지 방법이 있습니다.

* [서비스 컨테이너를 통한 등록](#registration-via-services-container)
* [File via 등록 Razor](#registration-via-razor-file)
* [페이지 모델 또는 컨트롤러를 통한 등록](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>서비스 컨테이너를 통한 등록

태그 도우미 구성 요소 클래스가 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>로 관리되지 않는 경우 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 시스템에 등록되어야 합니다. 다음 `Startup.ConfigureServices` 코드는 [임시 수명](xref:fundamentals/dependency-injection#lifetime-and-registration-options)이 있는 `AddressStyleTagHelperComponent` 및 `AddressScriptTagHelperComponent` 클래스를 등록합니다.

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>File via 등록 Razor

태그 도우미 구성 요소가 DI를 사용 하 여 등록 되지 않은 경우 Razor 페이지 페이지나 MVC 뷰에서 등록할 수 있습니다. 이 기법은 파일에서 삽입 된 태그와 구성 요소 실행 순서를 제어 하는 데 사용 됩니다 Razor .

`ITagHelperComponentManager`는 태그 도우미 구성 요소를 추가하거나 앱에서 제거하는 데 사용됩니다. 다음 코드에서는 `AddressTagHelperComponent`를 사용한 이 기술을 보여 줍니다.

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

위의 코드에서

* `@inject` 지시문은 `ITagHelperComponentManager`의 인스턴스를 제공합니다. 인스턴스는 `manager` 파일의 access 다운스트림에 대해 라는 변수에 할당 됩니다 Razor .
* `AddressTagHelperComponent` 인스턴스가 앱의 태그 도우미 구성 요소 컬렉션에 추가됩니다.

`AddressTagHelperComponent`는 `markup` 및 `order` 매개 변수를 허용하는 생성자를 수용하도록 수정됩니다.

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

제공된 `markup` 매개 변수는 다음과 같이 `ProcessAsync`에 사용됩니다.

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>페이지 모델 또는 컨트롤러를 통한 등록

태그 도우미 구성 요소가 DI를 사용 하 여 등록 되지 않은 경우 Razor 페이지 페이지 모델 또는 MVC 컨트롤러에서 등록할 수 있습니다. 이 기법은 c # 논리를 파일에서 분리 하는 데 유용 Razor 합니다.

생성자 주입은 `ITagHelperComponentManager`의 인스턴스에 액세스하는 데 사용됩니다. 태그 도우미 구성 요소를 인스턴스의 태그 도우미 구성 요소 컬렉션에 추가합니다. 다음 페이지 Razor 페이지 모델에서는이 기법을 보여 줍니다 `AddressTagHelperComponent` .

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

위의 코드에서

* 생성자 주입은 `ITagHelperComponentManager`의 인스턴스에 액세스하는 데 사용됩니다.
* `AddressTagHelperComponent` 인스턴스가 앱의 태그 도우미 구성 요소 컬렉션에 추가됩니다.

## <a name="create-a-component"></a>구성 요소 만들기

사용자 지정 태그 도우미 구성 요소를 만들려면 다음을 수행합니다.

* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>에서 파생된 공용 클래스를 만듭니다.
* [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute)클래스에 특성을 적용 합니다. 대상 HTML 요소의 이름을 지정합니다.
* *선택 사항*: [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) 클래스에 특성을 적용 하 여 IntelliSense에서 형식의 표시를 표시 하지 않습니다.

다음 코드는 `<address>` HTML 요소를 대상으로 하는 사용자 지정 태그 도우미 구성 요소를 만듭니다.

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

다음과 같이 사용자 지정 `address` 태그 도우미 구성 요소를 사용하여 HTML 태그를 주입합니다.

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

위의 `ProcessAsync` 메서드는 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*>에 제공된 HTML을 일치하는 `<address>` 요소에 주입합니다. 주입은 다음과 같은 경우에 발생합니다.

* 실행 컨텍스트의 `TagName` 속성 값이 `address`와 같습니다.
* 해당 `<address>` 요소에 `printable` 특성이 있습니다.

예를 들어 `if` 문은 다음 `<address>` 요소를 처리할 때 true로 평가됩니다.

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
