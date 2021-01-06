---
title: ASP.NET Core Blazor 레이아웃
author: guardrex
description: Blazor 앱의 재사용 가능한 레이아웃 구성 요소를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: 3cb7c6184c13a003b4f4294f887d8938caa42f97
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506905"
---
# <a name="aspnet-core-no-locblazor-layouts"></a>ASP.NET Core Blazor 레이아웃

작성자: [Rainer Stropek](https://www.timecockpit.com) 및 [Luke Latham](https://github.com/guardrex)

메뉴, 저작권 메시지, 회사 로고 등의 일부 앱 요소는 일반적으로 앱의 전체 레이아웃에 포함되며 앱의 모든 구성 요소에서 사용됩니다. 이러한 요소의 코드를 앱의 모든 구성 요소에 복사하는 것은 효율적이지 않습니다. 요소 중 하나에 업데이트가 필요할 때마다 모든 구성 요소를 업데이트해야 합니다. 해당 중복은 유지 관리하기 어렵고, 시간이 지남에 따라 일관성 없는 콘텐츠가 발생할 수 있습니다. ‘레이아웃’을 통해 이 문제를 해결할 수 있습니다.

기술적으로 레이아웃은 또 다른 구성 요소입니다. 레이아웃은 Razor 템플릿 또는 C# 코드에서 정의되며 [데이터 바인딩](xref:blazor/components/data-binding), [종속성 주입](xref:blazor/fundamentals/dependency-injection) 및 기타 구성 요소 시나리오를 사용할 수 있습니다.

구성 요소를 레이아웃으로 변환하려면 다음을 수행합니다.

* <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>에서 구성 요소를 상속합니다. <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>는 레이아웃 내부에 렌더링된 콘텐츠의 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 속성을 정의합니다.
* Razor 구문 `@Body`를 사용하여 콘텐츠가 렌더링되는 위치를 레이아웃 태그에 지정합니다.

다음 코드 샘플은 레이아웃 구성 요소인 `MainLayout.razor`의 Razor 템플릿을 보여 줍니다. 레이아웃은 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>를 상속하고 탐색 모음과 바닥글 사이에 `@Body`를 설정합니다.

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a>`MainLayout` 구성 요소

Blazor 프로젝트 템플릿 중 하나를 기반으로 하는 앱에서 `MainLayout` 구성 요소(`MainLayout.razor`)는 앱의 `Shared` 폴더에 있습니다.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a>기본 레이아웃

앱의 `App.razor` 파일에 있는 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에서 기본 앱 레이아웃을 지정합니다. 기본 Blazor 템플릿에서 제공하는 다음 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 `MainLayout` 구성 요소에 기본 레이아웃을 설정합니다.

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠의 기본 레이아웃을 제공하려면 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠에 대해 <xref:Microsoft.AspNetCore.Components.LayoutView>를 지정합니다.

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대한 자세한 내용은 <xref:blazor/fundamentals/routing>을 참조하세요.

라우터에서 레이아웃을 기본 레이아웃으로 지정하는 경우 구성 요소별 또는 폴더별로 재정의할 수 있기 때문에 유용합니다. 가장 일반적인 방법이므로 라우터를 사용하여 앱의 기본 레이아웃을 설정하는 것이 좋습니다.

## <a name="specify-a-layout-in-a-component"></a>구성 요소에서 레이아웃 지정

Razor 지시문 `@layout`을 사용하여 구성 요소에 레이아웃을 적용할 수 있습니다. 컴파일러는 `@layout`을 구성 요소 클래스에 적용되는 <xref:Microsoft.AspNetCore.Components.LayoutAttribute>로 변환합니다.

다음 `MasterList` 구성 요소의 콘텐츠는 `MasterLayout`의 `@Body` 위치에 삽입됩니다.

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

구성 요소에서 직접 레이아웃을 지정하면 라우터에서 설정된 ‘기본 레이아웃’이나 `_Imports.razor`에서 가져온 `@layout` 지시문이 재정의됩니다.

## <a name="centralized-layout-selection"></a>중앙에서 레이아웃 선택

앱의 모든 폴더에 이름이 `_Imports.razor`인 템플릿 파일을 선택적으로 포함할 수 있습니다. 컴파일러는 imports 파일에 지정된 지시문을 동일한 폴더와 모든 하위 폴더의 모든 Razor 템플릿에 포함합니다. 따라서 `_Imports.razor` 파일에 `@layout MyCoolLayout`을 포함하면 폴더의 모든 구성 요소가 `MyCoolLayout`을 사용하게 됩니다. 폴더와 하위 폴더에 있는 모든 `.razor` 파일에 `@layout MyCoolLayout`을 반복적으로 추가할 필요가 없습니다. `@using` 지시문은 구성 요소에도 동일한 방식으로 적용됩니다.

다음 `_Imports.razor` 파일은 아래 항목을 가져옵니다.

* `MyCoolLayout`.
* 동일한 폴더와 하위 폴더에 있는 모든 Razor 구성 요소.
* `BlazorApp1.Data` 네임스페이스.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

`_Imports.razor` 파일은 [Razor 뷰 및 Pages용 _ViewImports.cshtml 파일](xref:mvc/views/layout#importing-shared-directives)과 유사하지만, 구체적으로 Razor 구성 요소 파일에 적용됩니다.

`_Imports.razor`에서 레이아웃을 지정하면 라우터의 ‘기본 레이아웃’으로 지정된 레이아웃이 재정의됩니다.

> [!WARNING]
> 루트 `_Imports.razor` 파일에 Razor `@layout` 지시어를 추가하지 **마세요**. 그러면 앱에서 레이아웃의 무한 루프가 발생합니다. 기본 앱 레이아웃을 제어하려면 `Router` 구성 요소에서 레이아웃을 지정합니다. 자세한 내용은 [기본 레이아웃](#default-layout) 섹션을 참조하세요.

## <a name="nested-layouts"></a>중첩된 레이아웃

앱은 중첩된 레이아웃으로 구성될 수 있습니다. 구성 요소는 레이아웃을 참조할 수 있고, 레이아웃은 자기 다른 레이아웃을 참조합니다. 예를 들어 레이아웃 중첩은 여러 수준의 메뉴 구조를 만드는 데 사용됩니다.

다음 예제에서는 중첩된 레이아웃을 사용하는 방법을 보여 줍니다. `EpisodesComponent.razor` 파일은 표시할 구성 요소입니다. 이 구성 요소는 `MasterListLayout`을 참조합니다.

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

`MasterListLayout.razor` 파일은 `MasterListLayout`을 제공합니다. 레이아웃은 해당 레이아웃이 렌더링되는 다른 레이아웃인 `MasterLayout`을 참조합니다. `EpisodesComponent`는 `@Body`가 표시되는 위치에 렌더링됩니다.

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

최종적으로, `MasterLayout.razor`의 `MasterLayout`에는 헤더, 주 메뉴, 바닥글 등의 최상위 레이아웃 요소가 포함됩니다. `EpisodesComponent`를 포함하는 `MasterListLayout`은 `@Body`가 표시되는 위치에 렌더링됩니다.

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a>통합 구성 요소와 Razor Pages 레이아웃 공유

라우팅 가능한 구성 요소가 Razor Pages 앱에 통합된 경우 구성 요소와 함께 앱의 공유 레이아웃을 사용할 수 있습니다. 자세한 내용은 <xref:blazor/components/prerendering-and-integration>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/layout>
