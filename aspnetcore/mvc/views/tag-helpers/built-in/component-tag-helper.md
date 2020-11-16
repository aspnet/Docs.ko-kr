---
title: ASP.NET Core의 구성 요소 태그 도우미
author: guardrex
ms.author: riande
description: 'ASP.NET Core 구성 요소 태그 도우미를 사용 하 여 Razor 페이지 및 뷰에서 구성 요소를 렌더링 하는 방법을 알아봅니다.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595456"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="3369d-103">ASP.NET Core의 구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="3369d-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3369d-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3369d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3369d-105">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3369d-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3369d-106">다음 중 하나에 대 한 *구성* 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="3369d-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3369d-107">문서의 *구성* 섹션에 있는 지침을 따르세요 <xref:blazor/components/prerendering-and-integration?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="3369d-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="3369d-108">구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="3369d-108">Component Tag Helper</span></span>

<span data-ttu-id="3369d-109">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 [구성 요소 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` 태그)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="3369d-110">Razor Razor *호스트 된 Blazor WebAssembly 앱* 에서 페이지 및 MVC 앱에 구성 요소를 통합 하는 것은 .net 5.0 이상에서 ASP.NET Core 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-110">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="3369d-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3369d-112">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="3369d-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="3369d-113">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="3369d-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3369d-114">Blazor WebAssembly 다음 표에서는 앱 렌더링 모드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-114">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="3369d-115">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="3369d-115">Render Mode</span></span> | <span data-ttu-id="3369d-116">설명</span><span class="sxs-lookup"><span data-stu-id="3369d-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="3369d-117">Blazor WebAssembly브라우저에 로드 될 때 대화형 구성 요소를 포함 하는 데 사용할 응용 프로그램의 표식을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-117">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="3369d-118">구성 요소가 미리 렌더링 된 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-118">The component isn't prerendered.</span></span> <span data-ttu-id="3369d-119">이 옵션을 사용 하면 서로 다른 페이지에서 다른 구성 요소를 보다 쉽게 렌더링할 수 Blazor WebAssembly 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-119">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="3369d-120">구성 요소를 정적 HTML로 Prerenders Blazor WebAssembly 나중에 브라우저에서 로드할 때 구성 요소를 대화형으로 만드는 데 사용할 수 있도록 응용 프로그램에 대 한 표식을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-120">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="3369d-121">Blazor Server 다음 표에서는 앱 렌더링 모드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-121">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="3369d-122">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="3369d-122">Render Mode</span></span> | <span data-ttu-id="3369d-123">설명</span><span class="sxs-lookup"><span data-stu-id="3369d-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3369d-124">구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-124">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3369d-125">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-125">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3369d-126">Blazor Server 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-126">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3369d-127">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-127">Output from the component isn't included.</span></span> <span data-ttu-id="3369d-128">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-128">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3369d-129">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3369d-130">Blazor Server 다음 표에서는 앱 렌더링 모드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-130">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="3369d-131">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="3369d-131">Render Mode</span></span> | <span data-ttu-id="3369d-132">설명</span><span class="sxs-lookup"><span data-stu-id="3369d-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3369d-133">구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-133">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3369d-134">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-134">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3369d-135">Blazor Server 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-135">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3369d-136">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-136">Output from the component isn't included.</span></span> <span data-ttu-id="3369d-137">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3369d-138">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="3369d-139">추가 특성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-139">Additional characteristics include:</span></span>

* <span data-ttu-id="3369d-140">여러 구성 요소를 렌더링 하 Razor 는 여러 구성 요소가 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-140">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="3369d-141">앱이 시작 된 후에는 구성 요소를 동적으로 렌더링할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="3369d-142">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="3369d-143">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 기능을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="3369d-144">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소로 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="3369d-145">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="3369d-146">다음 구성 요소 태그 도우미는 `Counter` Blazor Server 를 사용 하 여 응용 프로그램의 페이지 또는 뷰에서 구성 요소를 렌더링 합니다 `ServerPrerendered` .</span><span class="sxs-lookup"><span data-stu-id="3369d-146">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="3369d-147">위의 예제에서는 `Counter` 구성 요소가 앱의 *Pages* 폴더에 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="3369d-148">자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름입니다 (예: `@using BlazorSample.Pages` `@using BlazorSample.Client.Pages` 호스팅된 솔루션의 또는 Blazor ).</span><span class="sxs-lookup"><span data-stu-id="3369d-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="3369d-149">구성 요소 태그 도우미는 구성 요소에 매개 변수를 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="3369d-150">`ColorfulCheckbox`확인란 레이블의 색 및 크기를 설정 하는 다음 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="3369d-151">`Size`( `int` ) 및 `Color` ( `string` ) [구성 요소 매개 변수](xref:blazor/components/index#component-parameters) 는 구성 요소 태그 도우미를 통해 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="3369d-152">위의 예제에서는 `ColorfulCheckbox` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="3369d-153">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `@using BlazorSample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="3369d-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="3369d-154">페이지 또는 뷰에서 렌더링 되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="3369d-155">앞의 예제에서와 같이 따옴표 붙은 문자열을 전달 하려면 [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요 `param-Color` 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-155">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="3369d-156">Razor특성이 형식이 기 때문에 형식 값에 대 한 구문 분석 동작은 `string` 특성에 적용 되지 않습니다 `param-*` `object` .</span><span class="sxs-lookup"><span data-stu-id="3369d-156">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="3369d-157">다음을 제외 하 고 모든 형식의 매개 변수가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="3369d-158">제네릭 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-158">Generic parameters.</span></span>
* <span data-ttu-id="3369d-159">Serialize 할 수 없는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="3369d-160">컬렉션 매개 변수의 상속입니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="3369d-161">형식이 Blazor WebAssembly 앱 외부에서 정의 되거나 지연 로드 된 어셈블리 내에 정의 된 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-161">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="3369d-162">매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="3369d-163">예를 들어 `Size` `Color` 및의 형식이 `Size` `Color` `int` `string` JSON serializer에서 지원 되는 기본 형식 (및) 이기 때문에 앞의 예제에서 및에 대 한 값을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="3369d-164">다음 예제에서는 클래스 개체가 구성 요소에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="3369d-165">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="3369d-165">*MyClass.cs* :</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="3369d-166">**클래스에는 매개 변수가 없는 public 생성자가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="3369d-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="3369d-167">*Shared/MyComponent* :</span><span class="sxs-lookup"><span data-stu-id="3369d-167">*Shared/MyComponent.razor* :</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="3369d-168">*Pages/m* :</span><span class="sxs-lookup"><span data-stu-id="3369d-168">*Pages/MyPage.cshtml* :</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="3369d-169">위의 예제에서는 `MyComponent` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="3369d-170">자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름 (예: `@using BlazorSample` 및)입니다 `@using BlazorSample.Shared` .</span><span class="sxs-lookup"><span data-stu-id="3369d-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="3369d-171">`MyClass` 는 응용 프로그램의 네임 스페이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3369d-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3369d-172">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3369d-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
