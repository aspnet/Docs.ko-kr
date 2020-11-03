---
title: ASP.NET Core Blazor CSS 격리
author: daveabrock
description: CSS 격리를 통해 CSS의 범위를 구성 요소로 지정하여 CSS를 간소화하고 다른 구성 요소나 라이브러리와의 충돌을 방지할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
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
uid: blazor/components/css-isolation
ms.openlocfilehash: c154e746c4c88fc919b2c0dddaea5fd585427a82
ms.sourcegitcommit: d84a225ec3381355c343460deed50f2fa5722f60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92431827"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a>ASP.NET Core Blazor CSS 격리

작성자: [Dave Brock](https://twitter.com/daveabrock)

CSS 격리는 전역 스타일에 대한 종속성을 방지하여 앱의 CSS 공간을 간소화하고 구성 요소와 라이브러리 간에 스타일 충돌을 방지하는 데 도움을 줍니다.

## <a name="enable-css-isolation"></a>CSS 격리 사용 

구성 요소별 스타일을 정의하려면 구성 요소에 대한 `.razor` 파일의 이름과 일치하는 `razor.css` 파일을 만듭니다. 이 `razor.css` 파일은 ‘범위가 지정된 CSS 파일’입니다. 

`MyComponent.razor` 파일이 있는 `MyComponent` 구성 요소에 대해서는 `MyComponent.razor.css`라는 구성 요소와 함께 파일을 만듭니다. `razor.css` 파일 이름의 `MyComponent` 값은 대/소문자를 구분하지 **않습니다** .

예를 들어 기본 Blazor 프로젝트 템플릿의 `Counter` 구성 요소에 CSS 격리를 추가하려면 `Counter.razor` 파일과 함께 `Counter.razor.css`라는 새 파일을 추가하고 다음 CSS를 추가합니다.

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

`Counter.razor.css`에 정의된 스타일은 `Counter` 구성 요소의 렌더링된 출력에만 적용됩니다. 앱의 다른 위치에 정의된 모든 `h1` CSS 선언이 `Counter` 스타일과 충돌하지 않습니다.

> [!NOTE]
> 묶음이 발생할 때 스타일 격리를 보장하기 위해 범위가 지정된 CSS 파일에서는 `@import` Razor 블록이 지원되지 않습니다.

## <a name="css-isolation-bundling"></a>CSS 격리 묶음

CSS 격리는 빌드 시간에 발생합니다. 이 프로세스 중에 Blazor는 구성 요소에 의해 렌더링된 태그와 일치하도록 CSS 선택기를 다시 작성합니다. 이렇게 다시 작성된 CSS 스타일은 `{PROJECT NAME}.styles.css`에서 정적 자산으로 묶여 생성됩니다. 여기서 `{PROJECT NAME}` 자리 표시자는 참조된 패키지 또는 제품 이름입니다.

이러한 정적 파일은 기본적으로 앱의 루트 경로에서 참조됩니다. 앱에서 생성된 HTML의 `<head>` 태그 내에서 참조를 검사하여 묶은 파일을 참조합니다.

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

묶은 파일 내에서 각 구성 요소는 범위 식별자와 연결됩니다. 스타일이 지정된 각 구성 요소에 대해 `b-<10-character-string>` 형식으로 HTML 특성이 추가됩니다. 식별자는 각 앱에 대해 고유하고 다릅니다. 렌더링된 `Counter` 구성 요소에서 Blazor는 `h1` 요소에 범위 식별자를 추가합니다.

```html
<h1 b-3xxtam6d07>
```

`MyProjectName.styles.css` 파일은 범위 식별자를 사용하여 해당 구성 요소로 스타일 선언을 그룹화합니다. 다음 예제에서는 위의 `<h1>` 요소에 대한 스타일을 제공합니다.

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

빌드 시간에 `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` 규칙을 사용하여 프로젝트 번들을 만듭니다. 여기서 `{STATIC WEB ASSETS BASE PATH}` 자리 표시자는 정적 웹 자산 기본 경로입니다.

NuGet 패키지 또는 [Razor 클래스 라이브러리](xref:blazor/components/class-libraries) 같은 다른 프로젝트를 활용하는 경우 묶은 파일은 다음과 같습니다.

* CSS 가져오기를 사용하여 스타일을 참조합니다.
* 스타일을 사용하는 앱의 정적 웹 자산으로 게시되지 않습니다.

## <a name="child-component-support"></a>자식 구성 요소 지원

기본적으로 CSS 격리는 `{COMPONENT NAME}.razor.css` 형식으로 연결하는 구성 요소에만 적용됩니다. 여기서 `{COMPONENT NAME}` 자리 표시자는 일반적으로 구성 요소 이름입니다. 자식 구성 요소에 변경 내용을 적용하려면 부모 구성 요소의 `razor.css` 파일에 있는 모든 하위 항목 요소에 `::deep` 조합기를 사용합니다. `::deep` 조합기는 요소의 생성된 범위 식별자의 ‘하위 항목’인 요소를 선택합니다. 

다음 예제에서는 `Child`라는 자식 구성 요소가 있는 `Parent`라는 부모 구성 요소를 보여 줍니다.

`Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Child.razor`:

```razor
<h1>Child Component</h1>
```

`::deep` 조합기로 `Parent.razor.css`의 `h1` 선언을 업데이트하여 `h1` 스타일 선언이 부모 구성 요소 및 해당 자식에 적용되어야 함을 나타냅니다.

```css
::deep h1 { 
    color: red;
}
```

이제 자식 구성 요소에 대해 별도의 범위가 지정된 CSS 파일을 만들지 않아도 `h1` 스타일이 `Parent` 및 `Child` 구성 요소에 적용됩니다.

> [!NOTE]
> `::deep` 조합기는 하위 항목 요소에서만 작동합니다. 다음 HTML 구조는 예상대로 `h1` 스타일을 구성 요소에 적용합니다.
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> 이 시나리오에서 ASP.NET Core는 부모 구성 요소의 범위 식별자를 `div` 요소에 적용하므로 브라우저는 부모 구성 요소에서 스타일이 상속됨을 알고 있습니다.
>
> 그러나 `div` 요소를 제외하면 하위 항목 관계가 제거되고 스타일이 자식 구성 요소에 적용되지 **않습니다** .
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a>CSS 전처리기 지원

CSS 전처리기는 변수, 중첩, 모듈, 믹스인 및 상속과 같은 기능을 활용하여 CSS 개발을 개선하는 데 유용합니다. CSS 격리는 Sass 또는 Less 같은 CSS 전처리기를 기본적으로 지원하지 않지만 Blazor가 빌드 프로세스 중 CSS 선택기를 다시 작성하기 전에 전처리기 컴파일이 수행되는 한 CSS 전처리기를 원활하게 통합할 수 있습니다. 예를 들어 Visual Studio를 사용하여 Visual Studio 작업 실행기 탐색기에서 **빌드 전** 작업으로 기존 전처리기 컴파일을 구성합니다.

[Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)와 같은 많은 타사 NuGet 패키지는 CSS 격리가 발생하기 전 빌드 프로세스 시작 부분에서 SASS/SCSS 파일을 컴파일할 수 있으며, 추가 구성이 필요하지 않습니다.

## <a name="css-isolation-configuration"></a>CSS 격리 구성

CSS 격리는 바로 작동 가능하도록 설계되었지만 기존 도구나 워크플로에 대한 종속성이 있는 경우처럼 일부 고급 시나리오에 대한 구성을 제공합니다.

### <a name="customize-scope-identifier-format"></a>범위 식별자 형식 사용자 지정

기본적으로 범위 식별자는 `b-<10-character-string>` 형식을 사용합니다. 범위 식별자 형식을 사용자 지정하려면 프로젝트 파일을 원하는 패턴으로 업데이트합니다.

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

위의 예제에서 `MyComponent.Razor.css`에 대해 생성된 CSS는 범위 식별자를 `b-<10-character-string>`에서 `my-custom-scope-identifier`로 변경합니다.

### <a name="change-base-path-for-static-web-assets"></a>정적 웹 자산에 대한 기본 경로 변경

`scoped.styles.css` 파일은 앱의 루트에 생성됩니다. 프로젝트 파일에서 `StaticWebAssetBasePath` 속성을 사용하여 기본 경로를 변경합니다. 다음 예제에서는 `_content` 경로에 `scoped.styles.css` 파일과 앱의 나머지 자산을 배치합니다.

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>자동 묶음 사용 안 함

Blazor가 런타임에 범위가 지정된 파일을 게시하고 로드하는 방법을 옵트아웃하려면 `DisableScopedCssBundling` 속성을 사용합니다. 이 속성을 사용하면 다른 도구나 프로세스가 `obj` 디렉터리에서 격리된 CSS 파일을 가져와 런타임에 게시하고 로드한다는 의미입니다.

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
