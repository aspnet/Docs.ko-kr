---
title: ASP.NET Core의 링크 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core 링크 태그 도우미 특성 및 HTML 링크 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059119"
---
# <a name="link-tag-helper-in-aspnet-core"></a>ASP.NET Core의 링크 태그 도우미

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[링크 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)는 기본 또는 대체 CSS 파일에 대한 링크를 생성합니다. 일반적으로 기본 CSS 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 위치합니다.

[!INCLUDE[](~/includes/cdn.md)]

링크 태그 도우미를 사용하면 CSS 파일에 CDN 및 CDN을 사용할 수 없는 경우를 대비한 대체를 지정할 수 있습니다. 링크 태그 도우미는 로컬 호스팅의 견고성과 함께 CDN의 성능 이점을 제공합니다.

다음 Razor 태그는 `head` ASP.NET Core 웹 앱 템플릿으로 만든 레이아웃 파일의 요소를 보여 줍니다.

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

다음은 이전 코드로부터 렌더링된 HTML입니다(개발 이외의 환경에서).

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

이전 코드에서 링크 태그 도우미는 `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 요소와 *bootstrap.min.css* 파일을 CDN에서 사용할 수 있는지 확인하는 데 사용되는 이어지는 JavaScript를 생성합니다. 이 경우에는 CSS 파일을 사용할 수 있었기 때문에 태그 도우미가 CDN CSS 파일을 사용하여 `<link />` 요소를 생성했습니다.

## <a name="commonly-used-link-tag-helper-attributes"></a>일반적으로 사용되는 링크 태그 도우미 특성

링크 태그 도우미의 모든 특성, 속성 및 메서드는 [링크 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)를 참조하세요.

### <a name="href"></a>href

링크된 리소스의 기본 주소입니다. 이 주소는 모든 경우에 생성된 HTML로 전달됩니다.

### <a name="asp-fallback-href"></a>asp-fallback-href

기본 URL이 실패할 경우 대체할 CSS 스타일시트의 URL입니다.

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

대체 테스트에 사용할 스타일시트에 정의된 클래스 이름입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>를 참조하세요.

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

대체 테스트에 사용할 CSS 속성 이름입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>를 참조하세요.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

대체 테스트에 사용할 CSS 속성 값입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
