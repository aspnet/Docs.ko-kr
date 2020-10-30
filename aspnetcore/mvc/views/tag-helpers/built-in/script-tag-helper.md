---
title: ASP.NET Core의 스크립트 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core 스크립트 태그 도우미 특성 및 HTML 스크립트 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- appsettings.json
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053503"
---
# <a name="script-tag-helper-in-aspnet-core"></a>ASP.NET Core의 스크립트 태그 도우미

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[스크립트 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)는 기본 또는 대체 스크립트 파일에 대한 링크를 생성합니다. 일반적으로 기본 스크립트 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 위치합니다.

[!INCLUDE[](~/includes/cdn.md)]

스크립트 태그 도우미를 사용하면 스크립트 파일에 CDN 및 CDN을 사용할 수 없는 경우를 대비한 대체를 지정할 수 있습니다. 스크립트 태그 도우미는 로컬 호스팅의 견고성과 함께 CDN의 성능 이점을 제공합니다.

다음 Razor 태그는 대체 ( `script` fallback)가 있는 요소를 보여 줍니다.

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

`<script>` 요소의 [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) 특성을 사용하여 CDN 스크립트 로드를 지연시키지 마세요. 스크립트 태그 도우미는 [asp-fallback-test](#asp-fallback-test) 식을 즉시 실행하는 JavaScript를 렌더링합니다. CDN 스크립트 로딩이 지연되면 해당 식이 실패합니다.

## <a name="commonly-used-script-tag-helper-attributes"></a>일반적으로 사용되는 스크립트 태그 도우미 특성

스크립트 태그 도우미의 모든 특성, 속성 및 메서드는 [스크립트 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)를 참조하세요.

### <a name="asp-fallback-test"></a>asp-fallback-test

대체 테스트에 사용할 기본 스크립트에 정의된 스크립트 메서드입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>를 참조하세요.

### <a name="asp-fallback-src"></a>asp-fallback-src

기본 스크립트가 실패할 경우 대체할 Script 태그의 URL입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
