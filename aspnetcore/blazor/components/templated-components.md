---
title: ASP.NET Core Blazor 템플릿 기반 구성 요소
author: guardrex
description: 템플릿 기반 구성 요소에서 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
ms.openlocfilehash: 579cabd9e6b7141ec6af4c6e221b805272a2fe40
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280034"
---
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core Blazor 템플릿 기반 구성 요소

템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 구성 요소입니다. 템플릿 기반 구성 요소를 사용하면 일반 구성 요소보다 재사용하기 쉬운 상위 수준 구성 요소를 작성할 수 있습니다. 몇 가지 예는 다음과 같습니다.

* 사용자가 테이블의 헤더, 행, 바닥글의 템플릿을 지정할 수 있게 해주는 테이블 구성 요소
* 사용자가 목록으로 항목을 렌더링하기 위한 템플릿을 지정할 수 있게 해주는 목록 구성 요소

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="template-parameters"></a>템플릿 매개 변수

템플릿 기반 구성 요소는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 또는 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 매개 변수를 하나 이상 지정하여 정의합니다. 렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다. <xref:Microsoft.AspNetCore.Components.RenderFragment%601>는 렌더링 조각을 호출할 때 지정할 수 있는 형식 매개 변수를 사용합니다.

`TableTemplate` 구성 요소(`TableTemplate.razor`):

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

템플릿 기반 구성 요소를 사용하는 경우, 매개 변수 이름(다음 예제에서 `TableHeader` 및 `RowTemplate`)과 일치하는 자식 요소를 사용하여 템플릿 매개 변수를 지정할 수 있습니다.

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
> 제네릭 형식 제약 조건은 향후 릴리스에서 지원될 예정입니다. 자세한 내용은 [Allow generic type constraints(dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)(제네릭 형식 제약 조건 허용)를 참조하세요.

## <a name="template-context-parameters"></a>템플릿 컨텍스트 매개 변수

요소로 전달된 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 인수에는 `context`라는 암시적 매개 변수(예: 위의 코드 샘플에서 `@context.PetId`)가 있지만, 자식 요소의 `Context` 특성을 사용하여 매개 변수 이름을 변경할 수 있습니다. 다음 예제에서 `RowTemplate` 요소의 `Context` 특성은 `pet` 매개 변수를 지정합니다.

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

또는 구성 요소 요소에 `Context` 특성을 지정할 수 있습니다. 지정한 `Context` 특성은 지정한 모든 템플릿 매개 변수에 적용됩니다. 이 기능은 래핑 자식 요소 없이 암시적 자식 콘텐츠의 콘텐츠 매개 변수 이름을 지정하려는 경우에 유용할 수 있습니다. 다음 예제에서 `Context` 특성은 `TableTemplate` 요소에 표시되고 모든 템플릿 매개 변수에 적용됩니다.

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

## <a name="generic-typed-components"></a>제네릭 형식 구성 요소

템플릿 기반 구성 요소는 제네릭 형식인 경우가 많습니다. 예를 들어 제네릭 `ListViewTemplate` 구성 요소(`ListViewTemplate.razor`)를 사용하여 `IEnumerable<T>` 값을 렌더링할 수 있습니다. 제네릭 구성 요소를 정의하려면 [`@typeparam`](xref:mvc/views/razor#typeparam) 지시문을 사용하여 형식 매개 변수를 지정합니다.

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

제네릭 형식 구성 요소를 사용하는 경우, 가능하면 형식 매개 변수가 유추됩니다.

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

불가능한 경우 형식 매개 변수 이름과 일치하는 특성을 사용하여 형식 매개 변수를 명시적으로 지정해야 합니다. 다음 예제에서 `TItem="Pet"`은 형식을 지정합니다.

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

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
