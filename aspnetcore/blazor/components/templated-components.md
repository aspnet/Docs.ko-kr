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
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core Blazor 템플릿 기반 구성 요소

템플릿 기반 구성 요소는 하나 이상의 UI 템플릿을 매개 변수로 허용한 다음, 이 매개 변수를 구성 요소 렌더링 논리의 일부로 사용할 수 있는 구성 요소입니다. 템플릿 기반 구성 요소를 사용하면 일반 구성 요소보다 재사용하기 쉬운 상위 수준 구성 요소를 작성할 수 있습니다. 몇 가지 예는 다음과 같습니다.

* 사용자가 테이블의 헤더, 행, 바닥글의 템플릿을 지정할 수 있게 해주는 테이블 구성 요소
* 사용자가 목록으로 항목을 렌더링하기 위한 템플릿을 지정할 수 있게 해주는 목록 구성 요소

템플릿 기반 구성 요소는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 또는 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 매개 변수를 하나 이상 지정하여 정의합니다. 렌더링 조각은 렌더링할 UI 세그먼트를 나타냅니다. <xref:Microsoft.AspNetCore.Components.RenderFragment%601>는 렌더링 조각을 호출할 때 지정할 수 있는 형식 매개 변수를 사용합니다.

다음 `TableTemplate` 구성 요소에서 보여 주는 것처럼 템플릿 기반 구성 요소는 일반적으로 형식이 지정되는 경우가 많습니다. 이 예에서 제네릭 형식 `<T>`는 `IReadOnlyList<T>` 값을 렌더링하는 데 사용됩니다. 이 경우에는 애완동물(pets) 테이블을 표시하는 구성 요소의 일련의 애완동물 행입니다.

`Shared/TableTemplate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

템플릿 기반 구성 요소를 사용하는 경우, 매개 변수 이름과 일치하는 자식 요소를 사용하여 템플릿 매개 변수를 지정할 수 있습니다. 다음 예제에서는 `<TableHeader>...</TableHeader>` 및 `<RowTemplate>...<RowTemplate>`이 `TableTemplate` 구성 요소의 `TableHeader` 및 `RowTemplate`용 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 템플릿을 제공합니다.

래핑 자식 요소 없이 암시적 자식 콘텐츠의 콘텐츠 매개 변수 이름을 지정하려는 경우 구성 요소에서 `Context` 특성을 지정합니다. 다음 예제에서 `Context` 특성은 `TableTemplate` 요소에 표시되고 모든 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 템플릿 매개 변수에 적용됩니다.

`Pages/Pets.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

또는 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 자식 요소의 `Context` 특성을 사용하여 매개 변수 이름을 변경할 수 있습니다. 다음 예제에서는 `RowTemplate`에서 `TableTemplate` 대신 `Context`가 설정됩니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<xref:Microsoft.AspNetCore.Components.RenderFragment%601> 형식의 구성 요소 인수에 `context`라는 암시적 매개 변수가 있는데, 이것을 사용할 수 있습니다. 다음 예제에서는 `Context`가 설정되어 있지 않습니다. `@context.{PROPERTY}`는 템플릿에 애완동물 값을 제공합니다. 여기서 `{PROPERTY}`는 `Pet` 속성입니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

제네릭 형식 구성 요소를 사용할 때 가능한 경우 형식 매개 변수가 유추됩니다. 그러나 형식 매개 변수와 일치하는 이름이 있는 특성을 사용하여 형식을 명시적으로 지정할 수 있습니다(이전 예제의 `TItem`).

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a>제네릭 형식 제약 조건

> [!NOTE]
> 제네릭 형식 제약 조건은 향후 릴리스에서 지원될 예정입니다. 자세한 내용은 [Allow generic type constraints(dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)(제네릭 형식 제약 조건 허용)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
