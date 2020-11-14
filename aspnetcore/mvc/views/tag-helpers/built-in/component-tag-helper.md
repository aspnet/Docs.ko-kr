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
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431045"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="4c103-103">ASP.NET Core의 구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="4c103-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="4c103-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4c103-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4c103-105">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="4c103-105">Prerequisites</span></span>

<span data-ttu-id="4c103-106">다음 중 하나에 대 한 *구성* 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="4c103-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="4c103-107">구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="4c103-107">Component Tag Helper</span></span>

<span data-ttu-id="4c103-108">페이지 또는 뷰에서 구성 요소를 렌더링 하려면 [구성 요소 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` 태그)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="4c103-109">Razor Razor *호스트 된 Blazor WebAssembly 앱* 에서 페이지 및 MVC 앱에 구성 요소를 통합 하는 것은 .net 5.0 이상에서 ASP.NET Core 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-109">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="4c103-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4c103-111">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="4c103-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="4c103-112">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="4c103-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4c103-113">Blazor WebAssembly 다음 표에서는 앱 렌더링 모드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-113">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="4c103-114">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="4c103-114">Render Mode</span></span> | <span data-ttu-id="4c103-115">설명</span><span class="sxs-lookup"><span data-stu-id="4c103-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="4c103-116">Blazor WebAssembly브라우저에 로드 될 때 대화형 구성 요소를 포함 하는 데 사용할 응용 프로그램의 표식을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-116">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="4c103-117">구성 요소가 미리 렌더링 된 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-117">The component isn't prerendered.</span></span> <span data-ttu-id="4c103-118">이 옵션을 사용 하면 서로 다른 페이지에서 다른 구성 요소를 보다 쉽게 렌더링할 수 Blazor WebAssembly 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-118">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="4c103-119">구성 요소를 정적 HTML로 Prerenders Blazor WebAssembly 나중에 브라우저에서 로드할 때 구성 요소를 대화형으로 만드는 데 사용할 수 있도록 응용 프로그램에 대 한 표식을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-119">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="4c103-120">Blazor Server 다음 표에서는 앱 렌더링 모드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-120">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="4c103-121">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="4c103-121">Render Mode</span></span> | <span data-ttu-id="4c103-122">설명</span><span class="sxs-lookup"><span data-stu-id="4c103-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4c103-123">구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-123">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c103-124">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4c103-125">Blazor Server 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-125">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c103-126">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-126">Output from the component isn't included.</span></span> <span data-ttu-id="4c103-127">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-127">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4c103-128">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4c103-129">Blazor Server 다음 표에서는 앱 렌더링 모드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-129">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="4c103-130">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="4c103-130">Render Mode</span></span> | <span data-ttu-id="4c103-131">설명</span><span class="sxs-lookup"><span data-stu-id="4c103-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4c103-132">구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c103-133">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4c103-134">Blazor Server 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c103-135">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-135">Output from the component isn't included.</span></span> <span data-ttu-id="4c103-136">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4c103-137">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="4c103-138">추가 특성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-138">Additional characteristics include:</span></span>

* <span data-ttu-id="4c103-139">여러 구성 요소를 렌더링 하 Razor 는 여러 구성 요소가 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-139">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="4c103-140">앱이 시작 된 후에는 구성 요소를 동적으로 렌더링할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="4c103-141">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="4c103-142">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 기능을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="4c103-143">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소로 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="4c103-144">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="4c103-145">다음 구성 요소 태그 도우미는 `Counter` Blazor Server 를 사용 하 여 응용 프로그램의 페이지 또는 뷰에서 구성 요소를 렌더링 합니다 `ServerPrerendered` .</span><span class="sxs-lookup"><span data-stu-id="4c103-145">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4c103-146">위의 예제에서는 `Counter` 구성 요소가 앱의 *Pages* 폴더에 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="4c103-147">자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름입니다 (예: `@using BlazorSample.Pages` `@using BlazorSample.Client.Pages` 호스팅된 솔루션의 또는 Blazor ).</span><span class="sxs-lookup"><span data-stu-id="4c103-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="4c103-148">구성 요소 태그 도우미는 구성 요소에 매개 변수를 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="4c103-149">`ColorfulCheckbox`확인란 레이블의 색 및 크기를 설정 하는 다음 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="4c103-150">`Size`( `int` ) 및 `Color` ( `string` ) [구성 요소 매개 변수](xref:blazor/components/index#component-parameters) 는 구성 요소 태그 도우미를 통해 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="4c103-151">위의 예제에서는 `ColorfulCheckbox` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="4c103-152">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `@using BlazorSample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="4c103-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="4c103-153">페이지 또는 뷰에서 렌더링 되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="4c103-154">앞의 예제에서와 같이 따옴표 붙은 문자열을 전달 하려면 [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요 `param-Color` 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-154">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="4c103-155">Razor특성이 형식이 기 때문에 형식 값에 대 한 구문 분석 동작은 `string` 특성에 적용 되지 않습니다 `param-*` `object` .</span><span class="sxs-lookup"><span data-stu-id="4c103-155">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="4c103-156">다음을 제외 하 고 모든 형식의 매개 변수가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="4c103-157">제네릭 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-157">Generic parameters.</span></span>
* <span data-ttu-id="4c103-158">Serialize 할 수 없는 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="4c103-159">컬렉션 매개 변수의 상속입니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="4c103-160">형식이 Blazor WebAssembly 앱 외부에서 정의 되거나 지연 로드 된 어셈블리 내에 정의 된 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-160">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="4c103-161">매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="4c103-162">예를 들어 `Size` `Color` 및의 형식이 `Size` `Color` `int` `string` JSON serializer에서 지원 되는 기본 형식 (및) 이기 때문에 앞의 예제에서 및에 대 한 값을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="4c103-163">다음 예제에서는 클래스 개체가 구성 요소에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="4c103-164">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="4c103-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="4c103-165">**클래스에는 매개 변수가 없는 public 생성자가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="4c103-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="4c103-166">*Shared/MyComponent* :</span><span class="sxs-lookup"><span data-stu-id="4c103-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="4c103-167">*Pages/m* :</span><span class="sxs-lookup"><span data-stu-id="4c103-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="4c103-168">위의 예제에서는 `MyComponent` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="4c103-169">자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름 (예: `@using BlazorSample` 및)입니다 `@using BlazorSample.Shared` .</span><span class="sxs-lookup"><span data-stu-id="4c103-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="4c103-170">`MyClass` 는 응용 프로그램의 네임 스페이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4c103-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4c103-171">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4c103-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
