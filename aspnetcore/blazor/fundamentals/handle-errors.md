---
title: 'ASP.NET Core :::no-loc(Blazor)::: 앱에서 오류 처리'
author: guardrex
description: 이 문서에서는 ASP.NET Core :::no-loc(Blazor):::가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: c789928252417ef1cf95c60deb7edef24d58126e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055999"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a><span data-ttu-id="7b757-103">ASP.NET Core :::no-loc(Blazor)::: 앱에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="7b757-103">Handle errors in ASP.NET Core :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="7b757-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="7b757-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="7b757-105">이 문서에서는 :::no-loc(Blazor):::가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다</span><span class="sxs-lookup"><span data-stu-id="7b757-105">This article describes how :::no-loc(Blazor)::: manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="7b757-106">개발 중에 발생한 자세한 오류</span><span class="sxs-lookup"><span data-stu-id="7b757-106">Detailed errors during development</span></span>

<span data-ttu-id="7b757-107">:::no-loc(Blazor)::: 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-107">When a :::no-loc(Blazor)::: app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="7b757-108">오류가 발생하면 :::no-loc(Blazor)::: 앱의 화면 아래쪽에 금색 막대가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-108">When an error occurs, :::no-loc(Blazor)::: apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="7b757-109">개발 중에 금색 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="7b757-110">프로덕션에서, 금색 막대는 오류가 발생했음을 알려주고 브라우저를 새로 고치도록 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="7b757-111">이 오류 처리 환경의 UI는 :::no-loc(Blazor)::: 프로젝트 템플릿의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-111">The UI for this error handling experience is part of the :::no-loc(Blazor)::: project templates.</span></span>

<span data-ttu-id="7b757-112">:::no-loc(Blazor WebAssembly)::: 앱에서 `wwwroot/index.html` 파일의 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-112">In a :::no-loc(Blazor WebAssembly)::: app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="7b757-113">:::no-loc(Blazor Server)::: 앱에서 `Pages/_Host.cshtml` 파일의 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-113">In a :::no-loc(Blazor Server)::: app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="7b757-114">`blazor-error-ui` 요소는 :::no-loc(Blazor)::: 템플릿에 포함된 스타일(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에 의해 숨겨지고 오류가 발생할 때 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-114">The `blazor-error-ui` element is hidden by the styles included in the :::no-loc(Blazor)::: templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="7b757-115">:::no-loc(Blazor Server)::: 앱이 처리되지 않은 예외에 반응하는 방법</span><span class="sxs-lookup"><span data-stu-id="7b757-115">How a :::no-loc(Blazor Server)::: app reacts to unhandled exceptions</span></span>

<span data-ttu-id="7b757-116">:::no-loc(Blazor Server):::는 상태 저장 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-116">:::no-loc(Blazor Server)::: is a stateful framework.</span></span> <span data-ttu-id="7b757-117">사용자가 앱과 상호 작용하는 동안 *회로* 라는 서버에 대한 연결이 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="7b757-118">회로는 활성 구성 요소 인스턴스와 다음과 같은 상태의 여러 다양한 측면을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="7b757-119">구성 요소의 가장 최근에 렌더링된 출력</span><span class="sxs-lookup"><span data-stu-id="7b757-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="7b757-120">클라이언트 쪽 이벤트에 의해 트리거될 수 있는 현재 이벤트 처리 대리자 세트</span><span class="sxs-lookup"><span data-stu-id="7b757-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="7b757-121">사용자가 여러 브라우저 탭에서 앱을 여는 경우 여러 개의 독립적인 회로가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="7b757-122">:::no-loc(Blazor):::는 발생하는 대부분의 처리되지 않은 예외를 회로에 치명적인 것으로 취급합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-122">:::no-loc(Blazor)::: treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="7b757-123">처리되지 않은 예외로 인해 회로가 종료되는 경우 사용자는 페이지를 다시 로드하여 새 회로를 만드는 방식으로 앱과 계속 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="7b757-124">다른 사용자 또는 다른 브라우저 탭을 위한 회로이면서 종료된 회로의 외부에 있는 회로는 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="7b757-125">이 시나리오는 충돌하는 데스크톱 앱과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="7b757-126">작동을 중단한 앱을 다시 시작해야 하지만 다른 앱은 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="7b757-127">다음과 같은 이유로 인해 처리되지 않은 예외가 발생하면 회로가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="7b757-128">처리되지 않은 예외는 종종 회로를 정의되지 않은 상태로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="7b757-129">처리되지 않은 예외가 발생한 후에는 앱의 일반 작업을 보장할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="7b757-130">회로가 계속되면 앱에서 보안 취약성이 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="7b757-131">개발자 코드에서 처리되지 않은 예외 관리</span><span class="sxs-lookup"><span data-stu-id="7b757-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="7b757-132">오류가 발생한 후에도 앱을 계속하려면 앱에 오류 처리 논리가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="7b757-133">이 문서의 나중 섹션에서는 처리되지 않은 예외의 잠재적 원인을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="7b757-134">프로덕션 환경에서는 UI에서 프레임워크 예외 메시지 또는 스택 추적을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="7b757-135">예외 메시지 또는 스택 추적을 렌더링하면 다음이 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="7b757-136">최종 사용자에게 중요한 정보를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="7b757-137">악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상시킬 수 있는 앱의 약점을 찾아내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="7b757-138">영구 공급자가 있는 오류 로깅</span><span class="sxs-lookup"><span data-stu-id="7b757-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="7b757-139">처리되지 않은 예외가 발생하는 경우 예외가 서비스 컨테이너에 구성된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="7b757-140">기본적으로 :::no-loc(Blazor)::: 앱은 콘솔 로깅 공급자의 콘솔 출력에 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-140">By default, :::no-loc(Blazor)::: apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="7b757-141">로그 크기와 로그 순환을 관리하는 공급자의 보다 영구적인 위치에 로깅하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="7b757-142">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b757-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="7b757-143">개발하는 동안 :::no-loc(Blazor):::는 일반적으로 디버깅에 도움을 주기 위해 브라우저 콘솔에 예외의 전체 세부 정보를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-143">During development, :::no-loc(Blazor)::: usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="7b757-144">프로덕션에서 브라우저 콘솔의 자세한 오류는 기본적으로 사용하지 않도록 설정되어 있습니다. 즉, 오류가 클라이언트로 전송되지 않지만 예외의 전체 세부 정보는 여전히 서버 쪽에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="7b757-145">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b757-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="7b757-146">로깅할 인시던트 및 로깅된 인시던트의 심각도 수준을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="7b757-147">악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="7b757-148">예를 들어, 제품 정보를 표시하는 구성 요소의 URL에서 알 수 없는 `ProductId`가 제공되는 오류의 경우 인시던트를 로깅하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="7b757-149">일부 오류는 로깅에 대한 심각도가 높은 인시던트로 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="7b757-150">자세한 내용은 <xref:blazor/fundamentals/logging>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b757-150">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="7b757-151">오류가 발생할 수 있는 위치</span><span class="sxs-lookup"><span data-stu-id="7b757-151">Places where errors may occur</span></span>

<span data-ttu-id="7b757-152">프레임워크 및 앱 코드는 다음 위치에서 처리되지 않은 예외를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="7b757-153">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="7b757-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="7b757-154">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="7b757-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="7b757-155">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="7b757-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="7b757-156">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="7b757-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="7b757-157">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="7b757-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="7b757-158">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="7b757-158">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="7b757-159">:::no-loc(Blazor Server)::: 다시 렌더링</span><span class="sxs-lookup"><span data-stu-id="7b757-159">:::no-loc(Blazor Server)::: rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="7b757-160">위의 처리되지 않은 예외는 이 문서의 다음 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="7b757-161">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="7b757-161">Component instantiation</span></span>

<span data-ttu-id="7b757-162">:::no-loc(Blazor):::가 구성 요소의 인스턴스를 만들 경우</span><span class="sxs-lookup"><span data-stu-id="7b757-162">When :::no-loc(Blazor)::: creates an instance of a component:</span></span>

* <span data-ttu-id="7b757-163">구성 요소의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="7b757-164">[`@inject`](xref:mvc/views/razor#inject) 지시어 또는 [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) 특성을 통해 구성 요소 생성자에 제공된 단일 싱글톤 DI 서비스의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="7b757-165">모든 `[Inject]` 속성에 대해 실행된 생성자 또는 setter가 처리되지 않은 예외를 throw하는 경우 :::no-loc(Blazor Server)::: 회로가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-165">A :::no-loc(Blazor Server)::: circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="7b757-166">이 예외는 프레임워크에서 구성 요소를 인스턴스화할 수 없기 때문에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="7b757-167">생성자 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="7b757-168">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="7b757-168">Lifecycle methods</span></span>

<span data-ttu-id="7b757-169">구성 요소의 수명 동안 :::no-loc(Blazor):::는 다음과 같은 [수명 주기 메서드](xref:blazor/components/lifecycle)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-169">During the lifetime of a component, :::no-loc(Blazor)::: invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="7b757-170">수명 주기 메서드가 동기적 또는 비동기적으로 예외를 throw하는 경우 이 예외는 :::no-loc(Blazor Server)::: 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="7b757-171">구성 요소가 수명 주기 메서드의 오류를 처리하려면 오류 처리 논리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="7b757-172"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>가 제품을 가져오는 메서드를 호출하는 다음 예제에서는 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-172">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="7b757-173">`ProductRepository.GetProductByIdAsync` 메서드에서 throw된 예외는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="7b757-174">`catch` 블록이 실행될 때 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="7b757-175">`loadFailed`가 사용자에게 오류 메시지를 표시하는 데 사용되는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="7b757-176">오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="7b757-177">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="7b757-177">Rendering logic</span></span>

<span data-ttu-id="7b757-178">`.razor` 구성 요소 파일의 선언적 태그는 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>라는 C# 메서드로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-178">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="7b757-179">구성 요소가 렌더링되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>가 실행되고, 렌더링된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명하는 데이터 구조를 구축합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-179">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="7b757-180">렌더링 논리는 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="7b757-181">이 시나리오의 예는 `@someObject.PropertyName`이 평가되지만 `@someObject`가 `null`인 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="7b757-182">렌더링 논리에 따라 throw된 처리되지 않은 예외는 :::no-loc(Blazor Server)::: 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-182">An unhandled exception thrown by rendering logic is fatal to a :::no-loc(Blazor Server)::: circuit.</span></span>

<span data-ttu-id="7b757-183">렌더링 논리에서 null 참조 예외가 발생하지 않도록 하려면 해당 멤버에 액세스하기 전에 `null` 개체를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="7b757-184">다음 예제에서 `person.Address`가 `null`인 경우 `person.Address` 속성에 액세스되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="7b757-185">위의 코드는 `person`이 `null`이 아니라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="7b757-186">일반적으로 이 코드의 구조에 따르면 구성 요소가 렌더링될 때 개체가 확실히 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="7b757-187">이러한 경우에는 렌더링 논리에서 `null`이 있는지 확인하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="7b757-188">이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`가 존재한다고 보장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="7b757-189">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="7b757-189">Event handlers</span></span>

<span data-ttu-id="7b757-190">클라이언트 쪽 코드는 다음을 사용하여 이벤트 처리기를 만들 때 C# 코드의 호출을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="7b757-191">기타 `@on...` 특성</span><span class="sxs-lookup"><span data-stu-id="7b757-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="7b757-192">이벤트 처리기 코드는 이러한 시나리오에서 처리되지 않은 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="7b757-193">이벤트 처리기가 처리되지 않은 예외(예: 데이터베이스 쿼리 실패)를 throw하는 경우 예외는 :::no-loc(Blazor Server)::: 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="7b757-194">앱에서 외부 이유로 인해 실패할 수 있는 코드를 호출하는 경우 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-194">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="7b757-195">사용자 코드에서 예외를 트랩 및 처리하지 않는 경우 프레임워크는 예외를 로깅하고 회로를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="7b757-196">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="7b757-196">Component disposal</span></span>

<span data-ttu-id="7b757-197">예를 들어, 사용자가 다른 페이지로 이동했으므로 UI에서 구성 요소가 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="7b757-198"><xref:System.IDisposable?displayProperty=fullName>을 구현하는 구성 요소가 UI에서 제거되면 프레임워크는 구성 요소의 <xref:System.IDisposable.Dispose%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="7b757-199">구성 요소의 `Dispose` 메서드가 처리되지 않은 예외를 throw하는 경우 예외는 :::no-loc(Blazor Server)::: 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="7b757-200">삭제 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅이 포함된 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="7b757-201">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b757-201">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="7b757-202">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="7b757-202">JavaScript interop</span></span>

<span data-ttu-id="7b757-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>는 .NET 코드가 사용자의 브라우저에서 JavaScript 런타임에 대한 비동기 호출을 수행할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="7b757-204">다음 조건은 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>의 오류 처리에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-204">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="7b757-205"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 동기적으로 실패하면 .NET 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-205">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="7b757-206">예를 들어, 제공된 인수를 직렬화 할 수 없기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-206">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="7b757-207">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-207">Developer code must catch the exception.</span></span> <span data-ttu-id="7b757-208">이벤트 처리기 또는 구성 요소 수명 주기 메서드의 앱 코드가 예외를 처리하지 않는 경우 결과 예외는 :::no-loc(Blazor Server)::: 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a :::no-loc(Blazor Server)::: circuit.</span></span>
* <span data-ttu-id="7b757-209"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 비동기적으로 실패하면 .NET <xref:System.Threading.Tasks.Task>는 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-209">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="7b757-210">예를 들어, JavaScript 쪽 코드에서 예외를 throw하거나 완료된 `Promise`를 `rejected`로 반환하기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-210">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="7b757-211">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-211">Developer code must catch the exception.</span></span> <span data-ttu-id="7b757-212">[`await`](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용하는 경우 오류 처리 및 로깅을 사용하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-212">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="7b757-213">이 연산자를 사용하지 않는 경우 오류 코드는 :::no-loc(Blazor Server)::: 회로에 치명적인 처리되지 않은 예외를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-213">Otherwise, the failing code results in an unhandled exception that's fatal to a :::no-loc(Blazor Server)::: circuit.</span></span>
* <span data-ttu-id="7b757-214">기본적으로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 호출은 특정 기간 내에 완료되어야 합니다. 그렇지 않으면 호출 시간이 초과됩니다. 기본 제한 시간은 1분입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-214">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="7b757-215">제한 시간은 네트워크 연결이 끊어진 코드 또는 완료 메시지를 다시 전송하지 않는 JavaScript 코드를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="7b757-216">호출 시간이 초과되면 결과 <xref:System.Threading.Tasks>는 <xref:System.OperationCanceledException>을 나타내며 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-216">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="7b757-217">로깅을 사용하여 예외를 트랩하고 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="7b757-218">마찬가지로 JavaScript 코드는 [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) 특성으로 표시되는 .NET 메서드에 대한 호출을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="7b757-219">이러한 .NET 메서드에서 처리되지 않은 예외를 throw하는 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="7b757-220">이 예외는 :::no-loc(Blazor Server)::: 회로에 치명적으로 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-220">The exception isn't treated as fatal to a :::no-loc(Blazor Server)::: circuit.</span></span>
* <span data-ttu-id="7b757-221">JavaScript 쪽 `Promise`는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="7b757-222">메서드 호출의 .NET 쪽 또는 JavaScript 쪽에서 오류 처리 코드를 사용하는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="7b757-223">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b757-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a><span data-ttu-id="7b757-224">:::no-loc(Blazor Server)::: 미리 렌더링</span><span class="sxs-lookup"><span data-stu-id="7b757-224">:::no-loc(Blazor Server)::: prerendering</span></span>

<span data-ttu-id="7b757-225">렌더링된 HTML 태그가 사용자의 초기 HTTP 요청 일부로 반환되도록 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 :::no-loc(Blazor)::: 구성 요소를 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-225">:::no-loc(Blazor)::: components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="7b757-226">이 작업은 다음을 통해 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-226">This works by:</span></span>

* <span data-ttu-id="7b757-227">동일한 페이지의 일부인 미리 렌더링된 모든 구성 요소에 대해 새 회로 생성</span><span class="sxs-lookup"><span data-stu-id="7b757-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="7b757-228">초기 HTML 생성</span><span class="sxs-lookup"><span data-stu-id="7b757-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="7b757-229">사용자의 브라우저가 동일한 서버에 대해 :::no-loc(SignalR)::: 연결을 다시 설정할 때까지 회로를 `disconnected`로 처리.</span><span class="sxs-lookup"><span data-stu-id="7b757-229">Treating the circuit as `disconnected` until the user's browser establishes a :::no-loc(SignalR)::: connection back to the same server.</span></span> <span data-ttu-id="7b757-230">연결이 설정되면 회로의 상호 작용이 다시 시작되고 구성 요소의 HTML 태그가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="7b757-231">사전 렌더링 동안 구성 요소가 처리되지 않은 예외를 throw하는 경우(예: 수명 주기 방법 동안 또는 렌더링 논리에서)</span><span class="sxs-lookup"><span data-stu-id="7b757-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="7b757-232">이 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="7b757-233"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미의 호출 스택에 대해 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-233">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="7b757-234">따라서 개발자 코드에서 예외를 명시적으로 catch하지 않으면 전체 HTTP 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="7b757-235">일반적인 경우에는 사전 렌더링에 실패하면 작업 구성 요소를 렌더링할 수 없기 때문에 구성 요소를 계속 빌드 및 렌더링하는 것은 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="7b757-236">렌더링 중에 발생할 수 있는 오류를 허용하려면 예외를 throw할 수 있는 오류 처리 논리를 구성 요소 내부에 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="7b757-237">오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-237">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="7b757-238">[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미를 래핑하는 대신, <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미에 의해 렌더링되는 구성 요소에 오류 처리 논리를 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-238">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="7b757-239">고급 시나리오</span><span class="sxs-lookup"><span data-stu-id="7b757-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="7b757-240">재귀 렌더링</span><span class="sxs-lookup"><span data-stu-id="7b757-240">Recursive rendering</span></span>

<span data-ttu-id="7b757-241">구성 요소는 재귀적으로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-241">Components can be nested recursively.</span></span> <span data-ttu-id="7b757-242">이것은 재귀적 데이터 구조를 나타내는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="7b757-243">예를 들어, `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="7b757-244">반복적으로 렌더링하는 경우 무한 재귀가 발생하는 코딩 패턴을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="7b757-245">주기가 포함된 데이터 구조는 재귀적으로 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="7b757-246">예를 들어, 자식에 자기 자신이 포함된 트리 노드를 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="7b757-247">주기가 포함된 레이아웃 체인을 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="7b757-248">예를 들어, 레이아웃만 있는 레이아웃은 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="7b757-249">최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 항목(규칙)을 위반할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="7b757-250">렌더링 중 무한 루프:</span><span class="sxs-lookup"><span data-stu-id="7b757-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="7b757-251">렌더링 프로세스가 계속 지속되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="7b757-252">종료되지 않는 루프를 만드는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="7b757-253">이 시나리오에서 영향을 받는 :::no-loc(Blazor Server)::: 회로는 실패하고 스레드는 일반적으로 다음을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-253">In these scenarios, an affected :::no-loc(Blazor Server)::: circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="7b757-254">운영 체제에서 허용하는 만큼의 CPU 시간을 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="7b757-255">서버 메모리를 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="7b757-256">무제한 메모리를 사용하는 것은 종료되지 않는 루프가 반복될 때마다 컬렉션에 항목을 추가하는 시나리오와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="7b757-257">무한 재귀 패턴을 방지하려면 재귀 렌더링 코드에 적절한 중지 조건이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="7b757-258">사용자 지정 렌더링 트리 논리</span><span class="sxs-lookup"><span data-stu-id="7b757-258">Custom render tree logic</span></span>

<span data-ttu-id="7b757-259">대부분의 :::no-loc(Blazor)::: 구성 요소는 `.razor` 파일로 구현되며 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>에 작동하여 출력을 렌더링하는 논리를 생성하도록 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-259">Most :::no-loc(Blazor)::: components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="7b757-260">개발자는 프로시저 C# 코드를 사용하여 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 수동으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-260">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="7b757-261">자세한 내용은 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b757-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="7b757-262">수동 렌더링 트리 작성기 논리를 사용하는 것은 일반적인 구성 요소 개발에는 권장되지 않는 안전하지 않은 고급 시나리오로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="7b757-263"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 코드를 작성하는 경우 개발자는 코드의 정확성을 보장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-263">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="7b757-264">예를 들어, 개발자는 다음을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="7b757-265"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 및 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A>에 대한 호출은 올바르게 균형이 조정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-265">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="7b757-266">특성은 올바른 위치에만 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="7b757-267">잘못된 수동 렌더링 트리 작성기 논리가 있으면 충돌, 서버 중단 및 보안 취약성을 포함하는 정의되지 않은 임의 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="7b757-268">어셈블리 코드 또는 MSIL 명령을 직접 작성하는 것과 동일한 수준의 복잡성과 동일한 수준의 위험에서 수동 렌더링 트리 작성기 논리를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="7b757-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
