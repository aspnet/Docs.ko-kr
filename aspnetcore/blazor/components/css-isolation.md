---
title: ASP.NET Core Blazor CSS 격리
author: daveabrock
description: CSS 격리를 통해 CSS의 범위를 구성 요소로 지정하여 CSS를 간소화하고 다른 구성 요소나 라이브러리와의 충돌을 방지할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 92545eab4004f6b67080f79d64b94bb424d5a102
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96320085"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="ca05b-103">ASP.NET Core Blazor CSS 격리</span><span class="sxs-lookup"><span data-stu-id="ca05b-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="ca05b-104">작성자: [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="ca05b-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="ca05b-105">CSS 격리는 전역 스타일에 대한 종속성을 방지하여 앱의 CSS 공간을 간소화하고 구성 요소와 라이브러리 간에 스타일 충돌을 방지하는 데 도움을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="ca05b-106">CSS 격리 사용</span><span class="sxs-lookup"><span data-stu-id="ca05b-106">Enable CSS isolation</span></span> 

<span data-ttu-id="ca05b-107">구성 요소별 스타일을 정의하려면 구성 요소에 대한 `.razor` 파일의 이름과 일치하는 `.razor.css` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="ca05b-108">이 `.razor.css` 파일은 ‘범위가 지정된 CSS 파일’입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="ca05b-109">`MyComponent.razor` 파일이 있는 `MyComponent` 구성 요소에 대해서는 `MyComponent.razor.css`라는 구성 요소와 함께 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="ca05b-110">`.razor.css` 파일 이름의 `MyComponent` 값은 대/소문자를 구분하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ca05b-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="ca05b-111">예를 들어 기본 Blazor 프로젝트 템플릿의 `Counter` 구성 요소에 CSS 격리를 추가하려면 `Counter.razor` 파일과 함께 `Counter.razor.css`라는 새 파일을 추가하고 다음 CSS를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="ca05b-112">`Counter.razor.css`에 정의된 스타일은 `Counter` 구성 요소의 렌더링된 출력에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="ca05b-113">앱의 다른 위치에 정의된 모든 `h1` CSS 선언이 `Counter` 스타일과 충돌하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="ca05b-114">묶음이 발생할 때 스타일 격리를 보장하기 위해 범위가 지정된 CSS 파일에서는 `@import` Razor 블록이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="ca05b-115">CSS 격리 묶음</span><span class="sxs-lookup"><span data-stu-id="ca05b-115">CSS isolation bundling</span></span>

<span data-ttu-id="ca05b-116">CSS 격리는 빌드 시간에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="ca05b-117">이 프로세스 중에 Blazor는 구성 요소에 의해 렌더링된 태그와 일치하도록 CSS 선택기를 다시 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="ca05b-118">이렇게 다시 작성된 CSS 스타일은 `{PROJECT NAME}.styles.css`에서 정적 자산으로 묶여 생성됩니다. 여기서 `{PROJECT NAME}` 자리 표시자는 참조된 패키지 또는 제품 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="ca05b-119">이러한 정적 파일은 기본적으로 앱의 루트 경로에서 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="ca05b-120">앱에서 생성된 HTML의 `<head>` 태그 내에서 참조를 검사하여 묶은 파일을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="ca05b-121">묶은 파일 내에서 각 구성 요소는 범위 식별자와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="ca05b-122">스타일이 지정된 각 구성 요소에 대해 `b-<10-character-string>` 형식으로 HTML 특성이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="ca05b-123">식별자는 각 앱에 대해 고유하고 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="ca05b-124">렌더링된 `Counter` 구성 요소에서 Blazor는 `h1` 요소에 범위 식별자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="ca05b-125">`MyProjectName.styles.css` 파일은 범위 식별자를 사용하여 해당 구성 요소로 스타일 선언을 그룹화합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="ca05b-126">다음 예제에서는 위의 `<h1>` 요소에 대한 스타일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="ca05b-127">빌드 시간에 `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` 규칙을 사용하여 프로젝트 번들을 만듭니다. 여기서 `{STATIC WEB ASSETS BASE PATH}` 자리 표시자는 정적 웹 자산 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="ca05b-128">NuGet 패키지 또는 [Razor 클래스 라이브러리](xref:blazor/components/class-libraries) 같은 다른 프로젝트를 활용하는 경우 묶은 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="ca05b-129">CSS 가져오기를 사용하여 스타일을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="ca05b-130">스타일을 사용하는 앱의 정적 웹 자산으로 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="ca05b-131">자식 구성 요소 지원</span><span class="sxs-lookup"><span data-stu-id="ca05b-131">Child component support</span></span>

<span data-ttu-id="ca05b-132">기본적으로 CSS 격리는 `{COMPONENT NAME}.razor.css` 형식으로 연결하는 구성 요소에만 적용됩니다. 여기서 `{COMPONENT NAME}` 자리 표시자는 일반적으로 구성 요소 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="ca05b-133">자식 구성 요소에 변경 내용을 적용하려면 부모 구성 요소의 `.razor.css` 파일에 있는 모든 하위 항목 요소에 `::deep` 조합기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="ca05b-134">`::deep` 조합기는 요소의 생성된 범위 식별자의 ‘하위 항목’인 요소를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="ca05b-135">다음 예제에서는 `Child`라는 자식 구성 요소가 있는 `Parent`라는 부모 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="ca05b-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="ca05b-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="ca05b-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="ca05b-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="ca05b-138">`::deep` 조합기로 `Parent.razor.css`의 `h1` 선언을 업데이트하여 `h1` 스타일 선언이 부모 구성 요소 및 해당 자식에 적용되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="ca05b-139">이제 자식 구성 요소에 대해 별도의 범위가 지정된 CSS 파일을 만들지 않아도 `h1` 스타일이 `Parent` 및 `Child` 구성 요소에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="ca05b-140">`::deep` 조합기는 하위 항목 요소에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="ca05b-141">다음 HTML 구조는 예상대로 `h1` 스타일을 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="ca05b-142">이 시나리오에서 ASP.NET Core는 부모 구성 요소의 범위 식별자를 `div` 요소에 적용하므로 브라우저는 부모 구성 요소에서 스타일이 상속됨을 알고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="ca05b-143">그러나 `div` 요소를 제외하면 하위 항목 관계가 제거되고 스타일이 자식 구성 요소에 적용되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ca05b-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="ca05b-144">CSS 전처리기 지원</span><span class="sxs-lookup"><span data-stu-id="ca05b-144">CSS preprocessor support</span></span>

<span data-ttu-id="ca05b-145">CSS 전처리기는 변수, 중첩, 모듈, 믹스인 및 상속과 같은 기능을 활용하여 CSS 개발을 개선하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="ca05b-146">CSS 격리는 Sass 또는 Less 같은 CSS 전처리기를 기본적으로 지원하지 않지만 Blazor가 빌드 프로세스 중 CSS 선택기를 다시 작성하기 전에 전처리기 컴파일이 수행되는 한 CSS 전처리기를 원활하게 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="ca05b-147">예를 들어 Visual Studio를 사용하여 Visual Studio 작업 실행기 탐색기에서 **빌드 전** 작업으로 기존 전처리기 컴파일을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="ca05b-148">[Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)와 같은 많은 타사 NuGet 패키지는 CSS 격리가 발생하기 전 빌드 프로세스 시작 부분에서 SASS/SCSS 파일을 컴파일할 수 있으며, 추가 구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="ca05b-149">CSS 격리 구성</span><span class="sxs-lookup"><span data-stu-id="ca05b-149">CSS isolation configuration</span></span>

<span data-ttu-id="ca05b-150">CSS 격리는 바로 작동 가능하도록 설계되었지만 기존 도구나 워크플로에 대한 종속성이 있는 경우처럼 일부 고급 시나리오에 대한 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="ca05b-151">범위 식별자 형식 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ca05b-151">Customize scope identifier format</span></span>

<span data-ttu-id="ca05b-152">기본적으로 범위 식별자는 `b-<10-character-string>` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="ca05b-153">범위 식별자 형식을 사용자 지정하려면 프로젝트 파일을 원하는 패턴으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="ca05b-154">위의 예제에서 `MyComponent.Razor.css`에 대해 생성된 CSS는 범위 식별자를 `b-<10-character-string>`에서 `my-custom-scope-identifier`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="ca05b-155">정적 웹 자산에 대한 기본 경로 변경</span><span class="sxs-lookup"><span data-stu-id="ca05b-155">Change base path for static web assets</span></span>

<span data-ttu-id="ca05b-156">`scoped.styles.css` 파일은 앱의 루트에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="ca05b-157">프로젝트 파일에서 `StaticWebAssetBasePath` 속성을 사용하여 기본 경로를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="ca05b-158">다음 예제에서는 `_content` 경로에 `scoped.styles.css` 파일과 앱의 나머지 자산을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="ca05b-159">자동 묶음 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="ca05b-159">Disable automatic bundling</span></span>

<span data-ttu-id="ca05b-160">Blazor가 런타임에 범위가 지정된 파일을 게시하고 로드하는 방법을 옵트아웃하려면 `DisableScopedCssBundling` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="ca05b-161">이 속성을 사용하면 다른 도구나 프로세스가 `obj` 디렉터리에서 격리된 CSS 파일을 가져와 런타임에 게시하고 로드한다는 의미입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="ca05b-162">RCL(Razor 클래스 라이브러리) 지원</span><span class="sxs-lookup"><span data-stu-id="ca05b-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="ca05b-163">[RCL(Razor 클래스 라이브러리)](xref:razor-pages/ui-class)이 격리된 스타일을 제공하는 경우 `<link>` 태그의 `href` 특성은 `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`를 가리키며, 여기서 자리 표시자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="ca05b-164">`{STATIC WEB ASSET BASE PATH}`: 정적 웹 자산 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="ca05b-165">`{ASSEMBLY NAME}`: 클래스 라이브러리의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="ca05b-166">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="ca05b-166">In the following example:</span></span>

* <span data-ttu-id="ca05b-167">정적 웹 자산 기본 경로가 `_content/ClassLib`입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="ca05b-168">클래스 라이브러리의 어셈블리 이름이 `ClassLib`입니다.</span><span class="sxs-lookup"><span data-stu-id="ca05b-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="ca05b-169">RCL 및 구성 요소 라이브러리에 대 한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca05b-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="ca05b-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="ca05b-170"><xref:blazor/components/class-libraries>.</span></span>
