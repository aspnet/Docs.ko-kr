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
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529984"
---
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="8c372-103">ASP.NET Core Blazor CSS 격리</span><span class="sxs-lookup"><span data-stu-id="8c372-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="8c372-104">작성자: [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="8c372-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="8c372-105">CSS 격리는 전역 스타일에 대한 종속성을 방지하여 앱의 CSS 공간을 간소화하고 구성 요소와 라이브러리 간에 스타일 충돌을 방지하는 데 도움을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="8c372-106">CSS 격리 사용</span><span class="sxs-lookup"><span data-stu-id="8c372-106">Enable CSS isolation</span></span> 

<span data-ttu-id="8c372-107">구성 요소별 스타일을 정의하려면 구성 요소에 대한 `.razor` 파일의 이름과 일치하는 `.razor.css` 파일을 같은 폴더에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="8c372-108">`.razor.css` 파일은 범위가 지정된 CSS 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="8c372-109">`Example.razor` 파일의 `Example` 구성 요소에 대해서는 `Example.razor.css`라는 구성 요소와 함께 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="8c372-110">`Example.razor.css` 파일은 `Example` 구성 요소(`Example.razor`)와 같은 폴더에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="8c372-111">파일의 "`Example`" 기본 이름은 대/소문자를 구분하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="8c372-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="8c372-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="8c372-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="8c372-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="8c372-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="8c372-114">**`Example.razor.css`에 정의된 스타일은 `Example` 구성 요소의 렌더링된 출력에만 적용됩니다.**</span><span class="sxs-lookup"><span data-stu-id="8c372-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="8c372-115">CSS 격리는 일치하는 Razor 파일의 HTML 요소에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="8c372-116">앱의 다른 위치에 정의된 모든 `h1` CSS 선언이 `Example` 구성 요소의 스타일과 충돌하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="8c372-117">묶음이 발생할 때 스타일 격리를 보장하기 위해 Razor 블록에서 CSS 가져오기는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="8c372-118">CSS 격리 묶음</span><span class="sxs-lookup"><span data-stu-id="8c372-118">CSS isolation bundling</span></span>

<span data-ttu-id="8c372-119">CSS 격리는 빌드 시간에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="8c372-120">이 프로세스 중에 Blazor는 구성 요소에 의해 렌더링된 태그와 일치하도록 CSS 선택기를 다시 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="8c372-121">이렇게 다시 작성된 CSS 스타일은 `{PROJECT NAME}.styles.css`에서 정적 자산으로 묶여 생성됩니다. 여기서 `{PROJECT NAME}` 자리 표시자는 참조된 패키지 또는 제품 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="8c372-122">이러한 정적 파일은 기본적으로 앱의 루트 경로에서 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="8c372-123">앱에서 생성된 HTML의 `<head>` 태그 내에서 참조를 검사하여 묶은 파일을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="8c372-124">묶은 파일 내에서 각 구성 요소는 범위 식별자와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="8c372-125">스타일이 지정된 각 구성 요소에 대해 `b-<10-character-string>` 형식으로 HTML 특성이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="8c372-126">식별자는 각 앱에 대해 고유하고 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="8c372-127">렌더링된 `Counter` 구성 요소에서 Blazor는 `h1` 요소에 범위 식별자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="8c372-128">`ProjectName.styles.css` 파일은 범위 식별자를 사용하여 해당 구성 요소로 스타일 선언을 그룹화합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="8c372-129">다음 예제에서는 위의 `<h1>` 요소에 대한 스타일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="8c372-130">빌드 시간에 `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` 규칙을 사용하여 프로젝트 번들을 만듭니다. 여기서 `{STATIC WEB ASSETS BASE PATH}` 자리 표시자는 정적 웹 자산 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="8c372-131">NuGet 패키지 또는 [Razor 클래스 라이브러리](xref:blazor/components/class-libraries) 같은 다른 프로젝트를 활용하는 경우 묶은 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="8c372-132">CSS 가져오기를 사용하여 스타일을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="8c372-133">스타일을 사용하는 앱의 정적 웹 자산으로 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="8c372-134">자식 구성 요소 지원</span><span class="sxs-lookup"><span data-stu-id="8c372-134">Child component support</span></span>

<span data-ttu-id="8c372-135">기본적으로 CSS 격리는 `{COMPONENT NAME}.razor.css` 형식으로 연결하는 구성 요소에만 적용됩니다. 여기서 `{COMPONENT NAME}` 자리 표시자는 일반적으로 구성 요소 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="8c372-136">자식 구성 요소에 변경 내용을 적용하려면 부모 구성 요소의 `.razor.css` 파일에 있는 모든 하위 항목 요소에 `::deep` 조합기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="8c372-137">`::deep` 조합기는 요소의 생성된 범위 식별자의 ‘하위 항목’인 요소를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="8c372-138">다음 예제에서는 `Child`라는 자식 구성 요소가 있는 `Parent`라는 부모 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="8c372-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8c372-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="8c372-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="8c372-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="8c372-141">`::deep` 조합기로 `Parent.razor.css`의 `h1` 선언을 업데이트하여 `h1` 스타일 선언이 부모 구성 요소 및 해당 자식에 적용되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="8c372-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="8c372-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="8c372-143">이제 자식 구성 요소에 대해 별도의 범위가 지정된 CSS 파일을 만들지 않아도 `h1` 스타일이 `Parent` 및 `Child` 구성 요소에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="8c372-144">`::deep` 조합기는 하위 항목 요소에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="8c372-145">다음 태그는 예상대로 `h1` 스타일을 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="8c372-146">부모 구성 요소의 범위 식별자가 `div` 요소에 적용되므로 브라우저는 부모 구성 요소에서 스타일이 상속됨을 알고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="8c372-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8c372-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="8c372-148">그러나 `div`를 제외하면 하위 항목 관계가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="8c372-149">다음 예제에서 스타일은 자식 구성 요소에 적용되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="8c372-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="8c372-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8c372-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="8c372-151">CSS 전처리기 지원</span><span class="sxs-lookup"><span data-stu-id="8c372-151">CSS preprocessor support</span></span>

<span data-ttu-id="8c372-152">CSS 전처리기는 변수, 중첩, 모듈, 믹스인 및 상속과 같은 기능을 활용하여 CSS 개발을 개선하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="8c372-153">CSS 격리는 Sass 또는 Less 같은 CSS 전처리기를 기본적으로 지원하지 않지만 Blazor가 빌드 프로세스 중 CSS 선택기를 다시 작성하기 전에 전처리기 컴파일이 수행되는 한 CSS 전처리기를 원활하게 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="8c372-154">예를 들어 Visual Studio를 사용하여 Visual Studio 작업 실행기 탐색기에서 **빌드 전** 작업으로 기존 전처리기 컴파일을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="8c372-155">[Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)와 같은 많은 타사 NuGet 패키지는 CSS 격리가 발생하기 전 빌드 프로세스 시작 부분에서 SASS/SCSS 파일을 컴파일할 수 있으며, 추가 구성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="8c372-156">CSS 격리 구성</span><span class="sxs-lookup"><span data-stu-id="8c372-156">CSS isolation configuration</span></span>

<span data-ttu-id="8c372-157">CSS 격리는 바로 작동 가능하도록 설계되었지만 기존 도구나 워크플로에 대한 종속성이 있는 경우처럼 일부 고급 시나리오에 대한 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="8c372-158">범위 식별자 형식 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="8c372-158">Customize scope identifier format</span></span>

<span data-ttu-id="8c372-159">기본적으로 범위 식별자는 `b-<10-character-string>` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="8c372-160">범위 식별자 형식을 사용자 지정하려면 프로젝트 파일을 원하는 패턴으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="8c372-161">위의 예제에서 `Example.Razor.css`에 대해 생성된 CSS는 범위 식별자를 `b-<10-character-string>`에서 `my-custom-scope-identifier`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="8c372-162">범위가 지정된 CSS 파일을 사용하여 상속을 수행하려면 범위 식별자를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="8c372-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="8c372-163">다음 프로젝트 파일 예제에서 `BaseComponent.razor.css` 파일은 구성 요소에 걸쳐 일반 스타일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="8c372-164">`DerivedComponent.razor.css` 파일은 이러한 스타일을 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="8c372-165">여러 파일에 걸쳐 범위 식별자를 공유하려면 와일드카드(`*`) 연산자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="8c372-166">정적 웹 자산에 대한 기본 경로 변경</span><span class="sxs-lookup"><span data-stu-id="8c372-166">Change base path for static web assets</span></span>

<span data-ttu-id="8c372-167">`scoped.styles.css` 파일은 앱의 루트에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="8c372-168">프로젝트 파일에서 `StaticWebAssetBasePath` 속성을 사용하여 기본 경로를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="8c372-169">다음 예제에서는 `_content` 경로에 `scoped.styles.css` 파일과 앱의 나머지 자산을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="8c372-170">자동 묶음 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="8c372-170">Disable automatic bundling</span></span>

<span data-ttu-id="8c372-171">Blazor가 런타임에 범위가 지정된 파일을 게시하고 로드하는 방법을 옵트아웃하려면 `DisableScopedCssBundling` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="8c372-172">이 속성을 사용하면 다른 도구나 프로세스가 `obj` 디렉터리에서 격리된 CSS 파일을 가져와 런타임에 게시하고 로드한다는 의미입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="8c372-173">RCL(Razor 클래스 라이브러리) 지원</span><span class="sxs-lookup"><span data-stu-id="8c372-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="8c372-174">[RCL(Razor 클래스 라이브러리)](xref:razor-pages/ui-class)이 격리된 스타일을 제공하는 경우 `<link>` 태그의 `href` 특성은 `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`를 가리키며, 여기서 자리 표시자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="8c372-175">`{STATIC WEB ASSET BASE PATH}`: 정적 웹 자산 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="8c372-176">`{ASSEMBLY NAME}`: 클래스 라이브러리의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="8c372-177">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="8c372-177">In the following example:</span></span>

* <span data-ttu-id="8c372-178">정적 웹 자산 기본 경로가 `_content/ClassLib`입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="8c372-179">클래스 라이브러리의 어셈블리 이름이 `ClassLib`입니다.</span><span class="sxs-lookup"><span data-stu-id="8c372-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="8c372-180">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages_Host.cshtml`(Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="8c372-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="8c372-181">RCL 및 구성 요소 라이브러리에 대 한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c372-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="8c372-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="8c372-182"><xref:blazor/components/class-libraries>.</span></span>
