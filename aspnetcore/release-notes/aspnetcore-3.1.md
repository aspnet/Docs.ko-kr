---
title: ASP.NET Core 3.1의 새로운 기능
author: rick-anderson
description: ASP.NET Core 3.1의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: 15240df978d9389af08030521adaf491f81d3a77
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051176"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3.1의 새로운 기능

이 문서에서는 ASP.NET Core 3.1의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.

## <a name="partial-class-support-for-no-locrazor-components"></a>Razor 구성 요소에 대한 partial 클래스 지원

이제 Razor 구성 요소가 partial 클래스로 생성됩니다. 단일 파일에서 구성 요소의 모든 코드를 정의하는 대신 partial 클래스로 정의된 코드 숨김 파일을 사용하여 Razor 구성 요소의 코드를 작성할 수 있습니다. 자세한 내용은 [partial 클래스 지원](xref:blazor/components/index#partial-class-support)을 참조하세요.

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor 구성 요소 태그 도우미 및 최상위 구성 요소에 매개 변수 전달

ASP.NET Core 3.0을 사용하는 Blazor에서, 구성 요소가 HTML 도우미(`Html.RenderComponentAsync`)를 사용하여 페이지와 보기로 렌더링되었습니다. ASP.NET Core 3.1에서, 새 구성 요소 태그 도우미를 사용하여 페이지 또는 보기에서 구성 요소를 렌더링합니다.

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

HTML 도우미는 ASP.NET Core 3.1에서 계속 지원되지만, 구성 요소 태그 도우미를 권장합니다.

Blazor Server 앱에서 이제 초기 렌더링 중에 최상위 수준 구성 요소에 매개 변수를 전달할 수 있습니다. 이전에는 [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)을 사용하여 최상위 구성 요소에만 매개 변수를 전달할 수 있었습니다. 이 릴리스에서는 [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) 및 [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered)가 모두 지원됩니다. 지정된 모든 매개 변수 값은 JSON으로 직렬화되고 초기 응답에 포함됩니다.

예를 들어, 증분 크기(`IncrementAmount`)를 사용하여 `Counter` 구성 요소를 미리 렌더링합니다.

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

자세한 내용은 [구성 요소를 Razor Pages 및 MVC 앱에 통합](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps)을 참조하세요.

## <a name="support-for-shared-queues-in-httpsys"></a>HTTP.sys에서 공유 큐에 대한 지원

[HTTP.sys](xref:fundamentals/servers/httpsys)에서 익명 요청 큐를 만들 수 있습니다. ASP.NET Core 3.1에서는 명명된 기존 HTTP.sys 요청 큐를 만들거나 이 큐에 연결하는 기능을 추가했습니다. 명명된 기존 HTTP.sys 요청 큐를 만들거나 이 큐에 연결하면 해당 큐가 있는 HTTP.sys 컨트롤러 프로세스가 수신기 프로세스와 독립적인 시나리오를 활성화합니다. 이와 같이 독립되어 있으므로 수신기 프로세스가 다시 시작하는 사이에 기존 연결 및 큐에 대기 중인 요청을 유지할 수 있습니다.

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>SameSite cookie의 호환성이 손상되는 변경

SameSite cookie의 동작이 예정된 브라우저 변경 내용을 반영하도록 변경되었습니다. 이는 AzureAd, OpenIdConnect 또는 WsFederation 등의 인증 시나리오에 영향을 줄 수 있습니다. 자세한 내용은 <xref:security/samesite>를 참조하세요.

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Blazor 앱에서 이벤트의 기본 동작 방지

이벤트의 기본 동작을 방지하려면 `@on{EVENT}:preventDefault` 지시문 특성을 사용합니다. 다음 예에서는, 텍스트 상자에 키 문자를 표시하는 기본 작업을 수행할 수 없습니다.

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

자세한 내용은 [기본 작업 방지](xref:blazor/components/event-handling#prevent-default-actions)를 참조하세요.

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Blazor 앱에서 이벤트 전파 중지

`@on{EVENT}:stopPropagation` 지시문 특성을 사용하여 이벤트 전파를 중지합니다. 다음 예제에서, 확인란을 선택하면 하위 `<div>`의 클릭 이벤트가 상위 `<div>`에 전파되지 않도록 합니다.

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

자세한 내용은 [이벤트 전파 중지](xref:blazor/components/event-handling#stop-event-propagation)를 참조하세요.

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Blazor 앱을 개발 중에 발생한 자세한 오류 정보

Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다. 오류가 발생하면 Blazor 앱의 화면 아래쪽에 금색 막대가 표시됩니다.

* 개발 중에 금색 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.
* 프로덕션에서, 금색 막대는 오류가 발생했음을 알려주고 브라우저를 새로 고치도록 권장합니다.

자세한 내용은 [개발 중에 발생한 자세한 오류 정보](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)를 참조하세요.
