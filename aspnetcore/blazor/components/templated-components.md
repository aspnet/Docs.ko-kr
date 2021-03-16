---
title: ASP.NET Core Blazor 템플릿 기반 구성 요소
author: guardrex
description: 템플릿 기반 구성 요소에서 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2021
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
uid: blazor/components/templated-components
ms.openlocfilehash: 6c94218f3808baca18f23a53688bafdd6354e760
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589480"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="26302-103">ASP.NET Core Blazor 템플릿 기반 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26302-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="26302-104">템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="26302-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="26302-105">템플릿 기반 구성 요소를 사용하면 일반 구성 요소보다 재사용하기 쉬운 상위 수준 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="26302-106">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-106">A couple of examples include:</span></span>

* <span data-ttu-id="26302-107">사용자가 테이블의 헤더, 행, 바닥글의 템플릿을 지정할 수 있게 해주는 테이블 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26302-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="26302-108">사용자가 목록으로 항목을 렌더링하기 위한 템플릿을 지정할 수 있게 해주는 목록 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26302-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="26302-109">템플릿 기반 구성 요소는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 또는 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 매개 변수를 하나 이상 지정하여 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="26302-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="26302-110">렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="26302-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="26302-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601>는 렌더링 조각을 호출할 때 지정할 수 있는 형식 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="26302-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="26302-112">다음 `TableTemplate` 구성 요소에서 보여 주는 것처럼 템플릿 기반 구성 요소는 일반적으로 형식이 지정되는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="26302-113">이 예에서 제네릭 형식 `<T>`는 `IReadOnlyList<T>` 값을 렌더링하는 데 사용됩니다. 이 경우에는 애완동물(pets) 테이블을 표시하는 구성 요소의 일련의 애완동물 행입니다.</span><span class="sxs-lookup"><span data-stu-id="26302-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="26302-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="26302-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="26302-115">템플릿 기반 구성 요소를 사용하는 경우, 매개 변수 이름과 일치하는 자식 요소를 사용하여 템플릿 매개 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="26302-116">다음 예제에서는 `<TableHeader>...</TableHeader>` 및 `<RowTemplate>...<RowTemplate>`이 `TableTemplate` 구성 요소의 `TableHeader` 및 `RowTemplate`용 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26302-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="26302-117">래핑 자식 요소 없이 암시적 자식 콘텐츠의 콘텐츠 매개 변수 이름을 지정하려는 경우 구성 요소에서 `Context` 특성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="26302-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="26302-118">다음 예제에서 `Context` 특성은 `TableTemplate` 요소에 표시되고 모든 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 템플릿 매개 변수에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="26302-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="26302-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="26302-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="26302-120">또는 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 자식 요소의 `Context` 특성을 사용하여 매개 변수 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="26302-121">다음 예제에서는 `RowTemplate`에서 `TableTemplate` 대신 `Context`가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="26302-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="26302-122"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 인수에 `context`라는 암시적 매개 변수가 있는데, 이것을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="26302-123">다음 예제에서는 `Context`가 설정되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="26302-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="26302-124">`@context.{PROPERTY}`는 템플릿에 애완동물 값을 제공합니다. 여기서 `{PROPERTY}`는 `Pet` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="26302-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="26302-125">제네릭 형식 구성 요소를 사용할 때 가능한 경우 형식 매개 변수가 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="26302-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="26302-126">그러나 형식 매개 변수와 일치하는 이름이 있는 특성을 사용하여 형식을 명시적으로 지정할 수 있습니다(이전 예제의 `TItem`).</span><span class="sxs-lookup"><span data-stu-id="26302-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a><span data-ttu-id="26302-127">제네릭 형식 제약 조건</span><span class="sxs-lookup"><span data-stu-id="26302-127">Generic type constraints</span></span>

> [!NOTE]
> <span data-ttu-id="26302-128">제네릭 형식 제약 조건은 향후 릴리스에서 지원될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="26302-128">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="26302-129">자세한 내용은 [Allow generic type constraints(dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)(제네릭 형식 제약 조건 허용)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="26302-129">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26302-130">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="26302-130">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
