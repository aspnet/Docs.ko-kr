---
title: 'ASP.NET Core :::no-loc(Blazor)::: 템플릿 기반 구성 요소'
author: guardrex
description: 템플릿 기반 구성 요소에서 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/templated-components
ms.openlocfilehash: ee38040a1edc8db43b8d5bf0202fd519b153ea04
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056246"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="aa0a4-103">ASP.NET Core :::no-loc(Blazor)::: 템플릿 기반 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aa0a4-103">ASP.NET Core :::no-loc(Blazor)::: templated components</span></span>

<span data-ttu-id="aa0a4-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="aa0a4-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="aa0a4-105">템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="aa0a4-106">템플릿 기반 구성 요소를 사용하면 일반 구성 요소보다 재사용하기 쉬운 상위 수준 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="aa0a4-107">몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-107">A couple of examples include:</span></span>

* <span data-ttu-id="aa0a4-108">사용자가 테이블의 헤더, 행, 바닥글의 템플릿을 지정할 수 있게 해주는 테이블 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aa0a4-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="aa0a4-109">사용자가 목록으로 항목을 렌더링하기 위한 템플릿을 지정할 수 있게 해주는 목록 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aa0a4-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="aa0a4-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa0a4-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="aa0a4-111">템플릿 매개 변수</span><span class="sxs-lookup"><span data-stu-id="aa0a4-111">Template parameters</span></span>

<span data-ttu-id="aa0a4-112">템플릿 기반 구성 요소는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 또는 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 매개 변수를 하나 이상 지정하여 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="aa0a4-113">렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="aa0a4-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601>는 렌더링 조각을 호출할 때 지정할 수 있는 형식 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="aa0a4-115">`TableTemplate` 구성 요소(`TableTemplate.razor`):</span><span class="sxs-lookup"><span data-stu-id="aa0a4-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="aa0a4-116">템플릿 기반 구성 요소를 사용하는 경우, 매개 변수 이름(다음 예제에서 `TableHeader` 및 `RowTemplate`)과 일치하는 자식 요소를 사용하여 템플릿 매개 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="aa0a4-117">제네릭 형식 제약 조건은 향후 릴리스에서 지원될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="aa0a4-118">자세한 내용은 [Allow generic type constraints(dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)(제네릭 형식 제약 조건 허용)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="aa0a4-119">템플릿 컨텍스트 매개 변수</span><span class="sxs-lookup"><span data-stu-id="aa0a4-119">Template context parameters</span></span>

<span data-ttu-id="aa0a4-120">요소로 전달된 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 인수에는 `context`라는 암시적 매개 변수(예: 위의 코드 샘플에서 `@context.PetId`)가 있지만, 자식 요소의 `Context` 특성을 사용하여 매개 변수 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="aa0a4-121">다음 예제에서 `RowTemplate` 요소의 `Context` 특성은 `pet` 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

<span data-ttu-id="aa0a4-122">또는 구성 요소 요소에 `Context` 특성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="aa0a4-123">지정한 `Context` 특성은 지정한 모든 템플릿 매개 변수에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="aa0a4-124">이 기능은 래핑 자식 요소 없이 암시적 자식 콘텐츠의 콘텐츠 매개 변수 이름을 지정하려는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="aa0a4-125">다음 예제에서 `Context` 특성은 `TableTemplate` 요소에 표시되고 모든 템플릿 매개 변수에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="aa0a4-126">제네릭 형식 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aa0a4-126">Generic-typed components</span></span>

<span data-ttu-id="aa0a4-127">템플릿 기반 구성 요소는 제네릭 형식인 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-127">Templated components are often generically typed.</span></span> <span data-ttu-id="aa0a4-128">예를 들어 제네릭 `ListViewTemplate` 구성 요소(`ListViewTemplate.razor`)를 사용하여 `IEnumerable<T>` 값을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="aa0a4-129">제네릭 구성 요소를 정의하려면 [`@typeparam`](xref:mvc/views/razor#typeparam) 지시문을 사용하여 형식 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="aa0a4-130">제네릭 형식 구성 요소를 사용하는 경우, 가능하면 형식 매개 변수가 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="aa0a4-131">불가능한 경우 형식 매개 변수 이름과 일치하는 특성을 사용하여 형식 매개 변수를 명시적으로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="aa0a4-132">다음 예제에서 `TItem="Pet"`은 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa0a4-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
