---
title: ASP.NET Core Razor 구성 요소 만들기 및 사용
author: guardrex
description: 데이터에 바인딩하고, 이벤트를 처리하고, 구성 요소 수명 주기를 관리하는 방법을 비롯하여 Razor 구성 요소를 만들고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/components/index
ms.openlocfilehash: d8838a458943599890420adec4551ad87e43d328
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637706"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="368bf-103">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="368bf-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="368bf-104">작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) 및 [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="368bf-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="368bf-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="368bf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="368bf-106">Blazor 앱은 *구성 요소* 를 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="368bf-107">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="368bf-108">구성 요소는 데이터를 주입하거나 UI 이벤트에 응답하는 데 필요한 HTML 태그와 처리 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="368bf-109">구성 요소는 유연하고 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="368bf-110">프로젝트 간에 중첩, 재사용 및 공유될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="368bf-111">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="368bf-111">Component classes</span></span>

<span data-ttu-id="368bf-112">구성 요소는 C# 및 HTML 태그 조합을 사용하여 [Razor](xref:mvc/views/razor) 구성 요소 파일(`.razor`)에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="368bf-113">Blazor의 구성 요소는 공식적으로 ‘Razor 구성 요소’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="368bf-114">Razor 구문</span><span class="sxs-lookup"><span data-stu-id="368bf-114">Razor syntax</span></span>

<span data-ttu-id="368bf-115">Blazor 앱의 Razor 구성 요소는 Razor 구문을 광범위하게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="368bf-116">Razor 태그 언어에 익숙하지 않은 경우 계속하기 전에 <xref:mvc/views/razor>를 읽는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="368bf-117">Razor 구문에서 콘텐츠에 액세스하는 경우 다음 섹션에 특히 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="368bf-118">[지시문](xref:mvc/views/razor#directives): 일반적으로 구성 요소 태그가 구문 분석되거나 작동하는 방식을 변경하는 `@` 접두사가 있는 예약 키워드입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="368bf-119">[지시문 특성](xref:mvc/views/razor#directive-attributes): 일반적으로 구성 요소가 구문 분석되거나 작동하는 방식을 변경하는 `@` 접두사가 있는 예약 키워드입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="368bf-120">이름</span><span class="sxs-lookup"><span data-stu-id="368bf-120">Names</span></span>

<span data-ttu-id="368bf-121">구성 요소의 이름은 대문자로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="368bf-122">예를 들어, `MyCoolComponent.razor`는 유효하고 `myCoolComponent.razor`는 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="368bf-123">라우팅</span><span class="sxs-lookup"><span data-stu-id="368bf-123">Routing</span></span>

<span data-ttu-id="368bf-124">Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="368bf-125">[`@page`][9] 지시문을 포함하는 Razor 파일이 컴파일되면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="368bf-126">런타임에 라우터는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>를 사용하여 구성 요소 클래스를 검색하고, 요청된 URL과 일치하는 경로 템플릿을 포함하는 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="368bf-127">자세한 내용은 <xref:blazor/fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="368bf-128">태그</span><span class="sxs-lookup"><span data-stu-id="368bf-128">Markup</span></span>

<span data-ttu-id="368bf-129">구성 요소의 UI는 HTML을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="368bf-130">동적 렌더링 논리(예: 루프, 조건, 식)는 *Razor* 라고 하는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="368bf-131">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리는 구성 요소 클래스로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="368bf-132">생성된 클래스의 이름은 파일 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="368bf-133">구성 요소 클래스의 멤버는 [`@code`][1] 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="368bf-134">[`@code`][1] 블록에서 구성 요소 상태(속성, 필드)는 이벤트 처리 또는 다른 구성 요소 논리 정의를 위한 메서드로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="368bf-135">두 개 이상의 [`@code`][1] 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="368bf-136">구성 요소 멤버는 `@`으로 시작되는 C# 식을 사용하는 구성 요소 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="368bf-137">예를 들어, C# 필드는 필드 이름에 앞에 `@`을 붙여 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="368bf-138">다음 예제는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="368bf-139">`headingFontStyle`을 평가하고 `font-style`에 대한 CSS 속성 값으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="368bf-140">`headingText`를 평가하고 `<h1>` 요소의 내용으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="368bf-141">구성 요소가 처음 렌더링되면 구성 요소는 이벤트에 대한 응답으로 렌더링 트리를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="368bf-142">그런 후 Blazor는 새로운 렌더링 트리를 이전 렌터링 트리와 비교하여 수정 사항을 브라우저 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="368bf-143">구성 요소는 일반 C# 클래스이며 프로젝트 내의 어느 위치에나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="368bf-144">웹 페이지를 생성하는 구성 요소는 일반적으로 `Pages` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="368bf-145">페이지와 무관한 구성 요소는 `Shared` 폴더 또는 프로젝트에 추가된 사용자 지정 폴더에 자주 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="368bf-146">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="368bf-146">Namespaces</span></span>

<span data-ttu-id="368bf-147">일반적으로 구성 요소의 네임스페이스는 앱의 루트 네임스페이스와 앱 내의 구성 요소 위치(폴더)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="368bf-148">앱의 루트 네임스페이스가 `BlazorSample`이고 `Counter` 구성 요소가 `Pages` 폴더에 있다면 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="368bf-149">`Counter` 구성 요소의 네임스페이스는 `BlazorSample.Pages`입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="368bf-150">구성 요소의 정규화된 형식 이름은 `BlazorSample.Pages.Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="368bf-151">구성 요소를 포함하는 사용자 지정 폴더의 경우 부모 구성 요소 또는 앱의 `_Imports.razor` 파일에 [`@using`][2] 지시문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="368bf-152">다음 예에서는 `Components` 폴더의 구성 요소를 사용할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="368bf-153">구성 요소는 정규화된 이름을 사용하여 참조할 수도 있습니다. 이 경우에는 [`@using`][2] 지시문이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="368bf-154">Razor로 작성된 구성 요소의 네임스페이스는 다음을 기준으로 합니다(우선 순위에 따름).</span><span class="sxs-lookup"><span data-stu-id="368bf-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="368bf-155">Razor 파일(`.razor`) 태그(`@namespace BlazorSample.MyNamespace`)의 [`@namespace`][8] 지정</span><span class="sxs-lookup"><span data-stu-id="368bf-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="368bf-156">프로젝트 파일(`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`</span><span class="sxs-lookup"><span data-stu-id="368bf-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="368bf-157">프로젝트 파일의 파일 이름(`.csproj`)에서 가져온 프로젝트 이름, 프로젝트 루트에서 구성 요소로의 경로.</span><span class="sxs-lookup"><span data-stu-id="368bf-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="368bf-158">예를 들어 프레임워크는 `{PROJECT ROOT}/Pages/Index.razor`(`BlazorSample.csproj`)를 `BlazorSample.Pages` 네임스페이스로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="368bf-159">구성 요소는 C# 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="368bf-160">이 예의 `Index` 구성 요소에서는 범위의 구성 요소가 모든 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="368bf-161">동일한 폴더의 `Pages`.</span><span class="sxs-lookup"><span data-stu-id="368bf-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="368bf-162">다른 네임스페이스를 명시적으로 지정하지 않는 프로젝트 루트의 구성 요소</span><span class="sxs-lookup"><span data-stu-id="368bf-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="368bf-163">`global::` 한정자는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="368bf-164">별칭이 지정된 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 문(예: `@using Foo = Bar`)을 사용하여 구성 요소를 가져오는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="368bf-165">부분적으로 정규화된 이름은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="368bf-166">예를 들어 `<Shared.NavMenu></Shared.NavMenu>`를 사용하여 `@using BlazorSample`을 추가하고 `NavMenu` 구성 요소(`NavMenu.razor`)를 참조하는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="368bf-167">Partial 클래스 지원</span><span class="sxs-lookup"><span data-stu-id="368bf-167">Partial class support</span></span>

<span data-ttu-id="368bf-168">Razor 구성 요소가 partial 클래스로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="368bf-169">Razor 구성 요소는 다음 방법 중 하나를 사용하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="368bf-170">C# 코드는 단일 파일에서 HTML 태그와 Razor 코드를 사용하여 [`@code`][1] 블록에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="368bf-171">Blazor 템플릿은 이 접근 방식을 사용하여 Razor 구성 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="368bf-172">C# 코드는 partial 클래스로 정의된 코드 숨김 파일에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="368bf-173">다음 예제에서는 Blazor 템플릿에서 생성된 앱에서 [`@code`][1] 블록을 포함하는 기본 `Counter` 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="368bf-174">HTML 태그, Razor 코드 및 C# 코드는 다음과 같은 동일한 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="368bf-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="368bf-176">Partial 클래스에서 코드 숨김 파일을 사용하여 `Counter` 구성 요소를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="368bf-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="368bf-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="368bf-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="368bf-179">필요한 경우 partial 클래스 파일에 필요한 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="368bf-180">Razor 구성 요소에 사용되는 일반적인 네임스페이스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="368bf-181">[`@using`][2] `_Imports.razor` 파일의 지시문은 C# 파일(`.cs`)이 아닌 Razor 파일(`.razor`)에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="368bf-182">기본 클래스 지정</span><span class="sxs-lookup"><span data-stu-id="368bf-182">Specify a base class</span></span>

<span data-ttu-id="368bf-183">[`@inherits`][6] 지시어를 사용하여 구성 요소에 대한 기본 클래스를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="368bf-184">다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속하여 구성 요소의 속성과 메서드를 제공하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="368bf-185">기본 클래스는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="368bf-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="368bf-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="368bf-187">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="368bf-188">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="368bf-188">Use components</span></span>

<span data-ttu-id="368bf-189">구성 요소는 HTML 요소 구문을 사용하여 선언함으로써 다른 구성 요소를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="368bf-190">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="368bf-191">`Pages/Index.razor`의 다음 태그는 `HeadingComponent` 인스턴스를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="368bf-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="368bf-193">구성 요소에 구성 요소 이름과 일치하지 않는 HTML 요소(첫 글자가 대문자임)가 포함되면 요소에 예기치 않은 이름이 있음을 나타내는 경고가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="368bf-194">구성 요소 네임스페이스에 대한 [`@using`][2] 지시문을 추가하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="368bf-195">매개 변수</span><span class="sxs-lookup"><span data-stu-id="368bf-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="368bf-196">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="368bf-196">Route parameters</span></span>

<span data-ttu-id="368bf-197">구성 요소는 [`@page`][9] 지시문에 제공된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="368bf-198">라우터는 경로 매개 변수를 사용하여 해당하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="368bf-199">선택적 매개 변수가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-199">Optional parameters are supported.</span></span> <span data-ttu-id="368bf-200">다음 예제에서 `text` 선택적 매개 변수는 경로 세그먼트의 값을 구성 요소의 `Text` 속성에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-200">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="368bf-201">세그먼트가 없으면 `Text` 값이 `fantastic`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-201">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="368bf-202">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-202">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="368bf-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="368bf-204">선택적 매개 변수는 지원되지 않으므로 앞의 예제에서 두 개의 [`@page`][9] 지시문이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-204">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="368bf-205">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-205">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="368bf-206">두 번째 [`@page`][9] 지시문은 `{text}` 경로 매개 변수를 받고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-206">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="368bf-207">여러 폴더 경계에서 경로를 캡처하는 범용 경로 매개 변수(`{*pageRoute}`)에 대한 자세한 내용은 <xref:blazor/fundamentals/routing#catch-all-route-parameters>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-207">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="368bf-208">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="368bf-208">Component parameters</span></span>

<span data-ttu-id="368bf-209">구성 요소에는 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성을 사용하여 구성 요소 클래스의 퍼블릭 속성을 사용하여 정의되는 구성 요소 매개 변수가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-209">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="368bf-210">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-210">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="368bf-211">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-211">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="368bf-212">샘플 앱의 다음 예제에서는 `ParentComponent`는 `ChildComponent`의 `Title` 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-212">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="368bf-213">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-213">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="368bf-214">규칙에 따라 C# 코드로 구성되는 특성 값은 [Razor의 예약된 `@` 기호](xref:mvc/views/razor#razor-syntax)를 사용하여 매개 변수에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-214">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="368bf-215">부모 필드 또는 속성: `Title="@{FIELD OR PROPERTY}`, 여기서 `{FIELD OR PROPERTY}` 자리 표시자는 부모 구성 요소의 C# 필드 또는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-215">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="368bf-216">메서드의 결과: `Title="@{METHOD}"`, 여기서 `{METHOD}` 자리 표시자는 부모 구성 요소의 C# 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-216">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="368bf-217">[암시적 또는 명시적 식](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, 여기서 `{EXPRESSION}` 자리 표시자는 C# 식입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-217">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="368bf-218">자세한 내용은 <xref:mvc/views/razor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-218">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="368bf-219">자체 *구성 요소 매개 변수* 에 쓰는 구성 요소를 만들지 말고 대신 private 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-219">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="368bf-220">자세한 내용은 [덮어쓴 매개 변수](#overwritten-parameters) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-220">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="368bf-221">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="368bf-221">Child content</span></span>

<span data-ttu-id="368bf-222">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-222">Components can set the content of another component.</span></span> <span data-ttu-id="368bf-223">할당 구성 요소는 받는 구성 요소를 지정하는 태그 간 콘텐츠를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-223">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="368bf-224">다음 예제에서 `ChildComponent`에는 렌더링할 UI의 세그먼트를 나타내는 <xref:Microsoft.AspNetCore.Components.RenderFragment>를 나타내는 `ChildContent` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-224">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="368bf-225">`ChildContent`의 값은 콘텐츠를 렌더링해야 하는 구성 요소의 태그에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-225">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="368bf-226">`ChildContent` 값은 부모 구성 요소에서 수신되고 부트스트랩 패널의 `panel-body` 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-226">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="368bf-227">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-227">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="368bf-228"><xref:Microsoft.AspNetCore.Components.RenderFragment> 콘텐츠를 받는 속성은 규칙에 따라 이름 `ChildContent`가 지정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-228">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="368bf-229">샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치하여 `ChildComponent`를 렌더링하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-229">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="368bf-230">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-230">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="368bf-231">Blazor가 자식 콘텐츠를 렌더링하는 방식 때문에 `for` 루프 내에서 구성 요소를 렌더링하려면 자식 구성 요소 콘텐츠에서 증분 루프 변수를 사용할 경우 로컬 인덱스 변수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-231">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="368bf-232">또는 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>를 활용하여 `foreach` 루프를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-232">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="368bf-233">특성 스플래팅 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="368bf-233">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="368bf-234">구성 요소는 구성 요소의 선언된 매개 변수 외에, 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-234">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="368bf-235">추가 특성을 사전에 캡처한 다음, [`@attributes`][3] Razor 지시문을 사용하여 구성 요소를 렌더링할 때 요소에 ‘스플래팅’할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-235">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="368bf-236">이 시나리오는 다양한 사용자 지정을 지원하는 태그 요소를 생성하는 구성 요소를 정의할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-236">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="368bf-237">예를 들어, 많은 매개 변수를 지원하는 `<input>`에 대해 개별적으로 특성을 정의하는 것이 번거로울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-237">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="368bf-238">다음 예제에서 첫 번째 `<input>` 요소(`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용하지만 두 번째 `<input>` 요소(`id="useAttributesDict"`)는 특성 스플래팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-238">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="368bf-239">매개 변수의 형식은 문자열 키를 사용하여 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-239">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="368bf-240">두 방법을 사용하여 렌더링되는 `<input>` 요소는 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-240">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="368bf-241">임의 특성을 허용하려면 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 속성이 `true`로 설정된 상태로 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성을 사용하여 구성 요소 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-241">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="368bf-242">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)의 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 속성을 사용하면 해당 매개 변수는 다른 매개 변수와 일치하지 않는 모든 특성을 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-242">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="368bf-243">구성 요소는 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>를 사용하여 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-243">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="368bf-244"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>에 사용되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-244">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="368bf-245">이 시나리오에서는 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-245">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="368bf-246">요소 특성의 위치에 상대적인 [`@attributes`][3]의 위치는 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-246">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="368bf-247">요소에 [`@attributes`][3]가 스플래팅되면 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 특성이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-247">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="368bf-248">`Child` 구성 요소를 사용하는 구성 요소의 다음 예를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-248">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="368bf-249">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-249">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="368bf-250">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-250">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="368bf-251">`Child` 구성 요소의 `extra` 특성은 [`@attributes`][3] 오른쪽으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-251">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="368bf-252">특성은 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 처리되기 때문에 `Parent` 구성 요소의 렌더링된 `<div>`는 추가 특성을 통해 전달될 때 `extra="5"`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-252">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="368bf-253">다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 [`@attributes`][3]의 순서가 반대로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-253">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="368bf-254">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-254">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="368bf-255">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="368bf-255">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="368bf-256">`Parent` 구성 요소의 렌더링된 `<div>`에는 추가 특성을 통해 전달될 경우 `extra="10"`을 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-256">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="368bf-257">구성 요소에 대한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="368bf-257">Capture references to components</span></span>

<span data-ttu-id="368bf-258">구성 요소 참조에서는 해당 인스턴스에 대해 명령(예: (`Show` 또는 `Reset`)을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-258">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="368bf-259">구성 요소 참조를 캡처하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-259">To capture a component reference:</span></span>

* <span data-ttu-id="368bf-260">자식 구성 요소에 [`@ref`][4] 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-260">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="368bf-261">자식 구성 요소와 동일한 유형으로 필드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-261">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="368bf-262">구성 요소가 렌더링되면 `loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-262">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="368bf-263">그런 다음, 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-263">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="368bf-264">`loginDialog` 변수는 구성 요소가 렌더링된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-264">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="368bf-265">구성 요소가 렌더링될 때까지는 참조할 요소가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-265">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="368bf-266">구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-266">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="368bf-267">이벤트 처리기에서 참조 변수를 사용하려면 람다 식을 사용하거나 [`OnAfterRenderAsync` 또는 `OnAfterRender` 메서드](xref:blazor/components/lifecycle#after-component-render)에 이벤트 처리기 대리자를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-267">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="368bf-268">이렇게 하면 이벤트 처리기가 할당되기 전에 참조 변수가 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-268">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="368bf-269">루프의 구성 요소를 참조하려면 [Capture references to multiple similar child-components(dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)(여러 비슷한 자식 구성 요소에 대한 참조 캡처)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-269">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="368bf-270">구성 요소 참조 캡처에는 [요소 참조 캡처](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)와 유사한 구문을 사용하지만 JavaScript interop 기능이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-270">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="368bf-271">구성 요소 참조가 JavaScript 코드로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-271">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="368bf-272">구성 요소 참조는 .NET 코드에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-272">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="368bf-273">구성 요소 참조를 사용하여 자식 구성 요소의 상태를 변경하지 **않도록 합니다**.</span><span class="sxs-lookup"><span data-stu-id="368bf-273">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="368bf-274">대신, 일반 선언적 매개 변수를 사용하여 자식 구성 요소에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-274">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="368bf-275">일반 선언적 매개 변수를 사용하면 자식 구성 요소가 올바른 시간에 자동으로 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-275">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="368bf-276">동기화 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="368bf-276">Synchronization context</span></span>

<span data-ttu-id="368bf-277">Blazor는 동기화 컨텍스트(<xref:System.Threading.SynchronizationContext>)를 사용하여 단일 논리적 실행 스레드를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-277">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="368bf-278">구성 요소의 [수명 주기 메서드](xref:blazor/components/lifecycle) 및 Blazor에서 발생하는 모든 이벤트 콜백은 이 동기화 컨텍스트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-278">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="368bf-279">Blazor Server의 동기화 컨텍스트는 단일 스레드인 브라우저의 WebAssembly 모델과 거의 일치하도록 단일 스레드 환경 에뮬레이션을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-279">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="368bf-280">지정된 시점에서 작업이 정확히 하나의 스레드에서만 수행되어 단일 논리적 스레드의 느낌을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-280">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="368bf-281">두 작업이 동시에 실행되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-281">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="368bf-282">스레드 차단 호출 방지</span><span class="sxs-lookup"><span data-stu-id="368bf-282">Avoid thread-blocking calls</span></span>

<span data-ttu-id="368bf-283">일반적으로 다음 메서드를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-283">Generally, don't call the following methods.</span></span> <span data-ttu-id="368bf-284">다음 메서드는 스레드를 차단하므로 기본 <xref:System.Threading.Tasks.Task>가 완료될 때까지 앱이 작업을 다시 시작하지 못하게 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-284">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="368bf-285">외부에서 구성 요소 메서드를 호출하여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="368bf-285">Invoke component methods externally to update state</span></span>

<span data-ttu-id="368bf-286">외부 이벤트(예: 타이머 또는 다른 알림)를 기준으로 구성 요소를 업데이트해야 하는 경우 Blazor의 동기화 컨텍스트에 디스패치되는 `InvokeAsync` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-286">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="368bf-287">예를 들어, 업데이트된 상태를 수신 구성 요소에 알릴 수 있는 *알림 서비스* 을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-287">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="368bf-288">`NotifierService`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-288">Register the `NotifierService`:</span></span>

* <span data-ttu-id="368bf-289">Blazor WebAssembly에서 `Program.Main`의 singleton으로 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-289">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="368bf-290">Blazor Server에서 `Startup.ConfigureServices`에 지정된 범위대로 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-290">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="368bf-291">`NotifierService`를 사용하여 구성 요소를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-291">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="368bf-292">위의 예제에서 `NotifierService`는 Blazor의 동기화 컨텍스트 외부에서 구성 요소의 `OnNotify` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-292">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="368bf-293">`InvokeAsync`는 올바른 컨텍스트로 전환하고 렌더링을 큐에 대기하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-293">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="368bf-294">\@ 키를 사용하여 요소 및 구성 요소 유지</span><span class="sxs-lookup"><span data-stu-id="368bf-294">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="368bf-295">요소 또는 구성 요소 목록을 렌더링하고, 이후에 요소 또는 구성 요소가 변경되는 경우 Blazor의 Diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-295">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="368bf-296">일반적으로 이 프로세스는 자동이며 무시해도 되지만 프로세스를 제어하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-296">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="368bf-297">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-297">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="368bf-298">`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬된 항목으로 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-298">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="368bf-299">구성 요소가 다시 렌더링되면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 수신하도록 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-299">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="368bf-300">이로 인해 다시 렌더링이 예상보다 더 복잡해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-300">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="368bf-301">경우에 따라 다시 렌더링을 수행하면 요소 포커스 손실과 같은 동작 차이가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-301">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="368bf-302">매핑 프로세스는 [`@key`][5] 지시문 특성을 사용하여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-302">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="368bf-303">[`@key`][5]를 사용하면 diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소가 유지되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-303">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="368bf-304">`People` 컬렉션이 변경되면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간 연결을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-304">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="368bf-305">`People` 목록에서 `Person`이 삭제된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-305">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="368bf-306">다른 인스턴스는 변경되지 않은 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-306">Other instances are left unchanged.</span></span>
* <span data-ttu-id="368bf-307">`Person`이 목록의 특정 위치에 삽입되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-307">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="368bf-308">다른 인스턴스는 변경되지 않은 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-308">Other instances are left unchanged.</span></span>
* <span data-ttu-id="368bf-309">`Person` 항목이 다시 정렬되면 해당 `<DetailsEditor>` 인스턴스가 유지되고 UI에서 다시 정렬됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-309">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="368bf-310">일부 시나리오에서는 [`@key`][5]를 사용하여 재랜더링의 복잡성을 최소화하고, DOM의 상태 저장 부분(예: 포커스 위치)이 변경될 수 있는 잠재적 문제를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-310">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="368bf-311">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-311">Keys are local to each container element or component.</span></span> <span data-ttu-id="368bf-312">문서 전체에서 키가 전역적으로 비교되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-312">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="368bf-313">\@ 키를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="368bf-313">When to use \@key</span></span>

<span data-ttu-id="368bf-314">일반적으로 목록이 렌더링될 때마다(예: [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 블록에서) [`@key`][5]를 사용하는 것이 적절하며 [`@key`][5]를 정의하기 위한 적절한 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-314">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="368bf-315">[`@key`][5]를 사용하여 개체가 변경될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지하지 않도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-315">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="368bf-316">`@currentPerson`이 변경되면 [`@key`][5] 특성 지시문은 Blazor가 강제로 전체 `<div>` 및 해당 하위 항목을 삭제하고 새 요소와 구성 요소를 사용하여 UI 내에서 하위 트리를 다시 빌드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-316">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="368bf-317">이것은 `@currentPerson`이 변경될 때 UI 상태가 유지되지 않도록 해야 하는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-317">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="368bf-318">\@ 키를 사용하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="368bf-318">When not to use \@key</span></span>

<span data-ttu-id="368bf-319">[`@key`][5]로 diff를 수행할 때 성능 비용이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-319">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="368bf-320">성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어할 때 앱에 도움이 되는 경우에만 [`@key`][5]를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-320">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="368bf-321">[`@key`][5]가 사용되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-321">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="368bf-322">[`@key`][5]를 사용할 때의 유일한 장점은 매핑을 선택하는 diff 알고리즘 대신, 모델 인스턴스가 유지된 구성 요소 인스턴스에 매핑되는 *방법* 을 제어할 수 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-322">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="368bf-323">\@ 키에 사용할 값</span><span class="sxs-lookup"><span data-stu-id="368bf-323">What values to use for \@key</span></span>

<span data-ttu-id="368bf-324">일반적으로 [`@key`][5]에 대해 다음과 같은 종류의 값 중 하나를 제공하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-324">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="368bf-325">모델 개체 인스턴스(예: 이전 예제와 같은 `Person` 인스턴스)</span><span class="sxs-lookup"><span data-stu-id="368bf-325">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="368bf-326">이렇게 하면 개체 참조 같음에 따라 보존이 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-326">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="368bf-327">고유 식별자(예: `int`, `string` 또는 `Guid` 형식의 기본 키 값)</span><span class="sxs-lookup"><span data-stu-id="368bf-327">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="368bf-328">[`@key`][5]에 사용되는 값이 충돌하지 않는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-328">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="368bf-329">동일한 부모 요소 내에서 충돌하는 값이 감지되면 이전 요소나 구성 요소를 새 요소나 구성 요소에 확정적으로 매핑할 수 없으므로 Blazor는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-329">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="368bf-330">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-330">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="368bf-331">덮어쓴 매개 변수</span><span class="sxs-lookup"><span data-stu-id="368bf-331">Overwritten parameters</span></span>

<span data-ttu-id="368bf-332">Blazor 프레임워크는 일반적으로 안전한 부모-자식 매개 변수 할당을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-332">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="368bf-333">매개 변수는 예기치 않게 덮어쓰지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-333">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="368bf-334">부작용이 최소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-334">Side-effects are minimized.</span></span> <span data-ttu-id="368bf-335">예를 들어 추가 렌더링은 무한 렌더링 루프를 만들 수 있으므로 피합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-335">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="368bf-336">자식 구성 요소는 부모 구성 요소가 렌더링될 때 기존 값을 덮어쓸 수 있는 새 매개 변수 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-336">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="368bf-337">자식 구성 요소의 매개 변수 값을 실수로 덮어쓰게 되면 하나 이상의 데이터 바인딩 매개 변수가 있는 구성 요소를 개발하고 개발자가 자식의 매개 변수에 직접 쓸 때 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-337">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="368bf-338">자식 구성 요소는 부모 구성 요소의 매개 변수 값을 하나 이상 사용하여 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-338">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="368bf-339">자식은 매개 변수 값에 직접 씁니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-339">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="368bf-340">부모 구성 요소는 자식 매개 변수의 값을 렌더링하고 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-340">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="368bf-341">매개 변수 값을 덮어쓸 수 있는 가능성은 자식 구성 요소의 속성 setter로도 확장됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-341">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="368bf-342">**일반적인 지침은 자체 매개 변수에 직접 쓰는 구성 요소를 만드는 것이 아닙니다.**</span><span class="sxs-lookup"><span data-stu-id="368bf-342">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="368bf-343">다음과 같은 잘못된 `Expander` 구성 요소를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-343">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="368bf-344">자식 콘텐츠를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-344">Renders child content.</span></span>
* <span data-ttu-id="368bf-345">구성 요소 매개 변수(`Expanded`)로 자식 콘텐츠 표시를 설정/해제합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-345">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="368bf-346">구성 요소는 `Expanded` 매개 변수에 직접 기록하는데, 이는 덮어쓴 매개 변수의 문제를 보여 주므로 피해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-346">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="368bf-347">`Expander` 구성 요소는 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출할 수 있는 부모 구성 요소에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-347">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="368bf-348">처음에 `Expander` 구성 요소는 `Expanded` 속성이 전환될 때 독립적으로 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-348">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="368bf-349">자식 구성 요소는 상태를 예상대로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-349">The child components maintain their states as expected.</span></span> <span data-ttu-id="368bf-350">부모에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출되면 첫 번째 자식 구성 요소의 `Expanded` 매개 변수가 초기 값(`true`)으로 다시 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-350">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="368bf-351">두 번째 `Expander` 구성 요소에서는 렌더링디는 자식 콘텐츠가 없으므로 구성 요소의 `Expanded` 값이 다시 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-351">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="368bf-352">앞의 시나리오에서 상태를 유지하려면 `Expander` 구성 요소에서 ‘private 필드’를 사용하여 전환된 상태를 유지합니다 *.*</span><span class="sxs-lookup"><span data-stu-id="368bf-352">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="368bf-353">다음은 수정된 `Expander` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-353">The following revised `Expander` component:</span></span>

* <span data-ttu-id="368bf-354">부모의 `Expanded` 구성 요소 매개 변수 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-354">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="368bf-355">[OnInitialized 이벤트](xref:blazor/components/lifecycle#component-initialization-methods)에서 구성 요소 매개 변수 값을 ‘private 필드’(`expanded`)에 할당합니다 *.*</span><span class="sxs-lookup"><span data-stu-id="368bf-355">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="368bf-356">프라이빗 필드를 사용하여 내부 설정/해제 상태를 유지합니다. 이 상태는 매개 변수에 직접 쓰는 것을 방지하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-356">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

<span data-ttu-id="368bf-357">자세한 내용은 [Blazor 양방향 바인딩 오류(dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-357">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="368bf-358">특성 적용</span><span class="sxs-lookup"><span data-stu-id="368bf-358">Apply an attribute</span></span>

<span data-ttu-id="368bf-359">[`@attribute`][7] 지시문을 사용하여 Razor 구성 요소에 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-359">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="368bf-360">다음 예제에서는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 구성 요소 클래스에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-360">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="368bf-361">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="368bf-361">Conditional HTML element attributes</span></span>

<span data-ttu-id="368bf-362">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-362">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="368bf-363">값이 `false` 또는 `null`이면 특성이 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-363">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="368bf-364">값이 `true`이면 특성이 최소화된 상태로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-364">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="368bf-365">다음 예제에서 `IsCompleted`는 `checked`가 요소의 태그에서 렌더링되는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-365">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="368bf-366">`IsCompleted`가 `true`이면 확인란이 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-366">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="368bf-367">`IsCompleted`가 `false`이면 확인란이 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-367">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="368bf-368">자세한 내용은 <xref:mvc/views/razor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-368">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="368bf-369">[`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) 같은 일부 HTML 특성은 .NET 형식이 `bool`일 경우 제대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-369">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="368bf-370">이러한 경우 `bool` 대신 `string` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-370">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="368bf-371">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="368bf-371">Raw HTML</span></span>

<span data-ttu-id="368bf-372">일반적으로 문자열은 DOM 텍스트 노드를 사용하여 렌더링됩니다. 즉, 포함될 수 있는 모든 태그는 무시되고 리터럴 텍스트로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-372">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="368bf-373">원시 HTML을 렌더링하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-373">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="368bf-374">값은 HTML 또는 SVG로 구문 분석되고 DOM에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-374">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="368bf-375">신뢰할 수 없는 원본에서 생성된 원시 HTML을 렌더링할 경우 **보안 위험** 이 있으므로 피해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-375">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="368bf-376">다음 예제에서는 `MarkupString` 형식을 사용하여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링된 출력에 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-376">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="368bf-377">Razor 템플릿</span><span class="sxs-lookup"><span data-stu-id="368bf-377">Razor templates</span></span>

<span data-ttu-id="368bf-378">렌더링 조각은 Razor 템플릿 구문을 사용하여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-378">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="368bf-379">Razor 템플릿은 UI 코드 조각을 정의하는 방법으로, 다음 형식을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-379">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="368bf-380">다음 예제에서는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 및 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 값을 지정하고 구성 요소에서 직접 템플릿을 렌더링하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-380">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="368bf-381">렌더링 조각은 [템플릿 구성 요소](xref:blazor/components/templated-components)에 인수로 전달될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-381">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="368bf-382">이전 코드의 렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="368bf-382">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="368bf-383">정적 자산</span><span class="sxs-lookup"><span data-stu-id="368bf-383">Static assets</span></span>

<span data-ttu-id="368bf-384">Blazor는 프로젝트의 [`web root (wwwroot)` 폴더](xref:fundamentals/index#web-root) 아래에 정적 자산을 배치하는 ASP.NET Core 앱의 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-384">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="368bf-385">기본 상대 경로(`/`)를 사용하여 정적 자산의 웹 루트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-385">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="368bf-386">다음 예제에서 `logo.png`는 실제로 `{PROJECT ROOT}/wwwroot/images` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-386">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="368bf-387">Razor 구성 요소는 물결표-슬래시 표기법(`~/`)을 지원하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="368bf-387">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="368bf-388">앱의 기본 경로를 설정하는 방법에 대한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-388">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="368bf-389">태그 도우미는 구성 요소에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-389">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="368bf-390">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)는 Razor 구성 요소(`.razor` 파일)에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-390">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="368bf-391">Blazor에서 태그 도우미와 유사한 기능을 제공하려면 대신, 태그 도우미와 동일한 기능을 포함하는 구성 요소를 만들고 해당 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-391">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="368bf-392">SVG(Scalable Vector Graphics) 이미지</span><span class="sxs-lookup"><span data-stu-id="368bf-392">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="368bf-393">Blazor는 HTML을 렌더링하므로 SVG(Scalable Vector Graphics) 이미지(`.svg`)를 포함하는 브라우저 지원 이미지는 `<img>` 태그를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-393">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="368bf-394">마찬가지로, 스타일시트 파일(`.css`)의 CSS 규칙에서는 SVG 이미지가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-394">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="368bf-395">그러나 인라인 SVG 태그는 일부 시나리오에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-395">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="368bf-396">`<svg>` 태그를 구성 요소 파일(`.razor`)에 직접 배치하면 기본 이미지 렌더링이 지원되지만 고급 시나리오 중 아직 지원되지 않는 시나리오가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-396">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="368bf-397">예를 들어, `<use>` 태그는 현재 적용되지 않으며 [`@bind`][10]를 일부 SVG 태그에서는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-397">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="368bf-398">자세한 내용은 [Blazor(dotnet/aspnetcore #18271)에서 SVG 지원](https://github.com/dotnet/aspnetcore/issues/18271)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="368bf-398">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="368bf-399">추가 자료</span><span class="sxs-lookup"><span data-stu-id="368bf-399">Additional resources</span></span>

* <span data-ttu-id="368bf-400"><xref:blazor/security/server/threat-mitigation>: 리소스를 소모하지 않도록 하는 Blazor Server 앱을 빌드하는 방법에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="368bf-400"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
