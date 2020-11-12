---
title: ASP.NET Core 3.1의 새로운 기능
author: rick-anderson
description: ASP.NET Core 3.1의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: dd012a2104f574865ed577ab3c0e81dc9cc9596d
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431019"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="aa804-103">ASP.NET Core 3.1의 새로운 기능</span><span class="sxs-lookup"><span data-stu-id="aa804-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="aa804-104">이 문서에서는 ASP.NET Core 3.1의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-no-locrazor-components"></a><span data-ttu-id="aa804-105">:::no-loc(Razor)::: 구성 요소에 대한 partial 클래스 지원</span><span class="sxs-lookup"><span data-stu-id="aa804-105">Partial class support for :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="aa804-106">이제 :::no-loc(Razor)::: 구성 요소가 partial 클래스로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-106">:::no-loc(Razor)::: components are now generated as partial classes.</span></span> <span data-ttu-id="aa804-107">단일 파일에서 구성 요소의 모든 코드를 정의하는 대신 partial 클래스로 정의된 코드 숨김 파일을 사용하여 :::no-loc(Razor)::: 구성 요소의 코드를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-107">Code for a :::no-loc(Razor)::: component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="aa804-108">자세한 내용은 [partial 클래스 지원](xref:blazor/components/index#partial-class-support)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa804-108">For more information, see [Partial class support](xref:blazor/components/index#partial-class-support).</span></span>

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a><span data-ttu-id="aa804-109">:::no-loc(Blazor)::: 구성 요소 태그 도우미 및 최상위 구성 요소에 매개 변수 전달</span><span class="sxs-lookup"><span data-stu-id="aa804-109">:::no-loc(Blazor)::: Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="aa804-110">ASP.NET Core 3.0을 사용하는 :::no-loc(Blazor):::에서, 구성 요소가 HTML 도우미(`Html.RenderComponentAsync`)를 사용하여 페이지와 보기로 렌더링되었습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-110">In :::no-loc(Blazor)::: with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="aa804-111">ASP.NET Core 3.1에서, 새 구성 요소 태그 도우미를 사용하여 페이지 또는 보기에서 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="aa804-112">HTML 도우미는 ASP.NET Core 3.1에서 계속 지원되지만, 구성 요소 태그 도우미를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="aa804-113">:::no-loc(Blazor Server)::: 앱에서 이제 초기 렌더링 중에 최상위 수준 구성 요소에 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-113">:::no-loc(Blazor Server)::: apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="aa804-114">이전에는 [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)을 사용하여 최상위 구성 요소에만 매개 변수를 전달할 수 있었습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="aa804-115">이 릴리스에서는 [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) 및 [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered)가 모두 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="aa804-116">지정된 모든 매개 변수 값은 JSON으로 직렬화되고 초기 응답에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="aa804-117">예를 들어, 증분 크기(`IncrementAmount`)를 사용하여 `Counter` 구성 요소를 미리 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="aa804-118">자세한 내용은 [구성 요소를 :::no-loc(Razor)::: Pages 및 MVC 앱에 통합](xref:blazor/components/prerendering-and-integration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa804-118">For more information, see [Integrate components into :::no-loc(Razor)::: Pages and MVC apps](xref:blazor/components/prerendering-and-integration).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="aa804-119">HTTP.sys에서 공유 큐에 대한 지원</span><span class="sxs-lookup"><span data-stu-id="aa804-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="aa804-120">[HTTP.sys](xref:fundamentals/servers/httpsys)에서 익명 요청 큐를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="aa804-121">ASP.NET Core 3.1에서는 명명된 기존 HTTP.sys 요청 큐를 만들거나 이 큐에 연결하는 기능을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="aa804-122">명명된 기존 HTTP.sys 요청 큐를 만들거나 이 큐에 연결하면 해당 큐가 있는 HTTP.sys 컨트롤러 프로세스가 수신기 프로세스와 독립적인 시나리오를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="aa804-123">이와 같이 독립되어 있으므로 수신기 프로세스가 다시 시작하는 사이에 기존 연결 및 큐에 대기 중인 요청을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a><span data-ttu-id="aa804-124">SameSite :::no-loc(cookie):::의 호환성이 손상되는 변경</span><span class="sxs-lookup"><span data-stu-id="aa804-124">Breaking changes for SameSite :::no-loc(cookie):::s</span></span>

<span data-ttu-id="aa804-125">SameSite :::no-loc(cookie):::의 동작이 예정된 브라우저 변경 내용을 반영하도록 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-125">The behavior of SameSite :::no-loc(cookie):::s has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="aa804-126">이는 AzureAd, OpenIdConnect 또는 WsFederation 등의 인증 시나리오에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="aa804-127">자세한 내용은 <xref:security/samesite>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa804-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a><span data-ttu-id="aa804-128">:::no-loc(Blazor)::: 앱에서 이벤트의 기본 동작 방지</span><span class="sxs-lookup"><span data-stu-id="aa804-128">Prevent default actions for events in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="aa804-129">이벤트의 기본 동작을 방지하려면 `@on{EVENT}:preventDefault` 지시문 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="aa804-130">다음 예에서는, 텍스트 상자에 키 문자를 표시하는 기본 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="aa804-131">자세한 내용은 [기본 작업 방지](xref:blazor/components/event-handling#prevent-default-actions)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa804-131">For more information, see [Prevent default actions](xref:blazor/components/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-no-locblazor-apps"></a><span data-ttu-id="aa804-132">:::no-loc(Blazor)::: 앱에서 이벤트 전파 중지</span><span class="sxs-lookup"><span data-stu-id="aa804-132">Stop event propagation in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="aa804-133">`@on{EVENT}:stopPropagation` 지시문 특성을 사용하여 이벤트 전파를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="aa804-134">다음 예제에서, 확인란을 선택하면 하위 `<div>`의 클릭 이벤트가 상위 `<div>`에 전파되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="aa804-135">자세한 내용은 [이벤트 전파 중지](xref:blazor/components/event-handling#stop-event-propagation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa804-135">For more information, see [Stop event propagation](xref:blazor/components/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-no-locblazor-app-development"></a><span data-ttu-id="aa804-136">:::no-loc(Blazor)::: 앱을 개발 중에 발생한 자세한 오류 정보</span><span class="sxs-lookup"><span data-stu-id="aa804-136">Detailed errors during :::no-loc(Blazor)::: app development</span></span>

<span data-ttu-id="aa804-137">:::no-loc(Blazor)::: 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-137">When a :::no-loc(Blazor)::: app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="aa804-138">오류가 발생하면 :::no-loc(Blazor)::: 앱의 화면 아래쪽에 금색 막대가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-138">When an error occurs, :::no-loc(Blazor)::: apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="aa804-139">개발 중에 금색 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="aa804-140">프로덕션에서, 금색 막대는 오류가 발생했음을 알려주고 브라우저를 새로 고치도록 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="aa804-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="aa804-141">자세한 내용은 [개발 중에 발생한 자세한 오류 정보](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa804-141">For more information, see [Detailed errors during development](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span></span>
