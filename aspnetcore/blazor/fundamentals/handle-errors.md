---
title: ASP.NET Core Blazor 앱에서 오류 처리
author: guardrex
description: 이 문서에서는 ASP.NET Core Blazor가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: 5a255c2d3535311cecd6b7219447e80d1ae78877
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758255"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="5b73e-103">ASP.NET Core Blazor 앱에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="5b73e-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="5b73e-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="5b73e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="5b73e-105">이 문서에서는 Blazor가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다</span><span class="sxs-lookup"><span data-stu-id="5b73e-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="5b73e-106">개발 중에 발생한 자세한 오류</span><span class="sxs-lookup"><span data-stu-id="5b73e-106">Detailed errors during development</span></span>

<span data-ttu-id="5b73e-107">Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="5b73e-108">오류가 발생하면 Blazor 앱의 화면 아래쪽에 금색 막대가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="5b73e-109">개발 중에 금색 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="5b73e-110">프로덕션에서, 금색 막대는 오류가 발생했음을 알려주고 브라우저를 새로 고치도록 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="5b73e-111">이 오류 처리 환경의 UI는 Blazor 프로젝트 템플릿의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="5b73e-112">Blazor WebAssembly 앱에서 `wwwroot/index.html` 파일의 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="5b73e-113">Blazor Server 앱에서 `Pages/_Host.cshtml` 파일의 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

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

<span data-ttu-id="5b73e-114">`blazor-error-ui` 요소는 Blazor 템플릿에 포함된 스타일(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에 의해 숨겨지고 오류가 발생할 때 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

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

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="5b73e-115">Blazor Server 자세한 회로 오류</span><span class="sxs-lookup"><span data-stu-id="5b73e-115">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="5b73e-116">클라이언트 쪽 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-116">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="5b73e-117">개발 단계에서는 자세한 오류를 사용하도록 설정하여 클라이언트에서 중요한 회로 오류 정보를 볼 수 있도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-117">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="5b73e-118">다음 방법을 사용하여 Blazor Server 자세한 오류를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-118">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="5b73e-119"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="5b73e-119"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="5b73e-120">`DetailedErrors` 구성 키를 `true`로 설정합니다. 구성 키는 앱의 개발 설정 파일(`appsettings.Development.json`)에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-120">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="5b73e-121">`ASPNETCORE_DETAILEDERRORS` 환경 변수의 값을 `true`로 설정하여 키를 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-121">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="5b73e-122">자세한 SignalR 로깅을 위해 [SignalR 서버 쪽 로깅](xref:signalr/diagnostics#server-side-logging)(`Microsoft.AspNetCore.SignalR`)을 [디버그](xref:Microsoft.Extensions.Logging.LogLevel) 또는 [추적](xref:Microsoft.Extensions.Logging.LogLevel)으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-122">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="5b73e-123">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="5b73e-123">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> [!WARNING]
> <span data-ttu-id="5b73e-124">인터넷에서 사용되는 클라이언트로 오류 정보를 노출하는 것은 항상 피해야 하는 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-124">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="5b73e-125">Blazor Server 앱이 처리되지 않은 예외에 반응하는 방법</span><span class="sxs-lookup"><span data-stu-id="5b73e-125">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="5b73e-126">Blazor Server는 상태 저장 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-126">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="5b73e-127">사용자가 앱과 상호 작용하는 동안 *회로* 라는 서버에 대한 연결이 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-127">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="5b73e-128">회로는 활성 구성 요소 인스턴스와 다음과 같은 상태의 여러 다양한 측면을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-128">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="5b73e-129">구성 요소의 가장 최근에 렌더링된 출력</span><span class="sxs-lookup"><span data-stu-id="5b73e-129">The most recent rendered output of components.</span></span>
* <span data-ttu-id="5b73e-130">클라이언트 쪽 이벤트에 의해 트리거될 수 있는 현재 이벤트 처리 대리자 세트</span><span class="sxs-lookup"><span data-stu-id="5b73e-130">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="5b73e-131">사용자가 여러 브라우저 탭에서 앱을 여는 경우 여러 개의 독립적인 회로가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-131">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="5b73e-132">Blazor는 발생하는 대부분의 처리되지 않은 예외를 회로에 치명적인 것으로 취급합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-132">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="5b73e-133">처리되지 않은 예외로 인해 회로가 종료되는 경우 사용자는 페이지를 다시 로드하여 새 회로를 만드는 방식으로 앱과 계속 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-133">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="5b73e-134">다른 사용자 또는 다른 브라우저 탭을 위한 회로이면서 종료된 회로의 외부에 있는 회로는 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-134">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="5b73e-135">이 시나리오는 충돌하는 데스크톱 앱과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-135">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="5b73e-136">작동을 중단한 앱을 다시 시작해야 하지만 다른 앱은 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-136">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="5b73e-137">다음과 같은 이유로 인해 처리되지 않은 예외가 발생하면 회로가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-137">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="5b73e-138">처리되지 않은 예외는 종종 회로를 정의되지 않은 상태로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-138">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="5b73e-139">처리되지 않은 예외가 발생한 후에는 앱의 일반 작업을 보장할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-139">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="5b73e-140">회로가 계속되면 앱에서 보안 취약성이 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-140">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="5b73e-141">개발자 코드에서 처리되지 않은 예외 관리</span><span class="sxs-lookup"><span data-stu-id="5b73e-141">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="5b73e-142">오류가 발생한 후에도 앱을 계속하려면 앱에 오류 처리 논리가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-142">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="5b73e-143">이 문서의 나중 섹션에서는 처리되지 않은 예외의 잠재적 원인을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-143">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="5b73e-144">프로덕션 환경에서는 UI에서 프레임워크 예외 메시지 또는 스택 추적을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-144">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="5b73e-145">예외 메시지 또는 스택 추적을 렌더링하면 다음이 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-145">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="5b73e-146">최종 사용자에게 중요한 정보를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-146">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="5b73e-147">악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상시킬 수 있는 앱의 약점을 찾아내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-147">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="5b73e-148">영구 공급자가 있는 오류 로깅</span><span class="sxs-lookup"><span data-stu-id="5b73e-148">Log errors with a persistent provider</span></span>

<span data-ttu-id="5b73e-149">처리되지 않은 예외가 발생하는 경우 예외가 서비스 컨테이너에 구성된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-149">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="5b73e-150">기본적으로 Blazor 앱은 콘솔 로깅 공급자의 콘솔 출력에 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-150">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="5b73e-151">로그 크기와 로그 순환을 관리하는 공급자의 보다 영구적인 위치에 로깅하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-151">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="5b73e-152">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b73e-152">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="5b73e-153">개발하는 동안 Blazor는 일반적으로 디버깅에 도움을 주기 위해 브라우저 콘솔에 예외의 전체 세부 정보를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-153">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="5b73e-154">프로덕션에서 브라우저 콘솔의 자세한 오류는 기본적으로 사용하지 않도록 설정되어 있습니다. 즉, 오류가 클라이언트로 전송되지 않지만 예외의 전체 세부 정보는 여전히 서버 쪽에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-154">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="5b73e-155">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b73e-155">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="5b73e-156">로깅할 인시던트 및 로깅된 인시던트의 심각도 수준을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-156">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="5b73e-157">악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-157">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="5b73e-158">예를 들어, 제품 정보를 표시하는 구성 요소의 URL에서 알 수 없는 `ProductId`가 제공되는 오류의 경우 인시던트를 로깅하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-158">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="5b73e-159">일부 오류는 로깅에 대한 심각도가 높은 인시던트로 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-159">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="5b73e-160">자세한 내용은 <xref:blazor/fundamentals/logging>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b73e-160">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="5b73e-161">오류가 발생할 수 있는 위치</span><span class="sxs-lookup"><span data-stu-id="5b73e-161">Places where errors may occur</span></span>

<span data-ttu-id="5b73e-162">프레임워크 및 앱 코드는 다음 위치에서 처리되지 않은 예외를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-162">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="5b73e-163">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="5b73e-163">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="5b73e-164">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="5b73e-164">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="5b73e-165">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="5b73e-165">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="5b73e-166">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="5b73e-166">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="5b73e-167">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="5b73e-167">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="5b73e-168">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="5b73e-168">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="5b73e-169">Blazor Server 다시 렌더링</span><span class="sxs-lookup"><span data-stu-id="5b73e-169">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="5b73e-170">위의 처리되지 않은 예외는 이 문서의 다음 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-170">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="5b73e-171">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="5b73e-171">Component instantiation</span></span>

<span data-ttu-id="5b73e-172">Blazor가 구성 요소의 인스턴스를 만들 경우</span><span class="sxs-lookup"><span data-stu-id="5b73e-172">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="5b73e-173">구성 요소의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-173">The component's constructor is invoked.</span></span>
* <span data-ttu-id="5b73e-174">[`@inject`](xref:mvc/views/razor#inject) 지시어 또는 [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) 특성을 통해 구성 요소 생성자에 제공된 단일 싱글톤 DI 서비스의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-174">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="5b73e-175">모든 `[Inject]` 속성에 대해 실행된 생성자 또는 setter가 처리되지 않은 예외를 throw하는 경우 Blazor Server 회로가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-175">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="5b73e-176">이 예외는 프레임워크에서 구성 요소를 인스턴스화할 수 없기 때문에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-176">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="5b73e-177">생성자 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-177">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="5b73e-178">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="5b73e-178">Lifecycle methods</span></span>

<span data-ttu-id="5b73e-179">구성 요소의 수명 동안 Blazor는 다음과 같은 [수명 주기 메서드](xref:blazor/components/lifecycle)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-179">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="5b73e-180">수명 주기 메서드가 동기적 또는 비동기적으로 예외를 throw하는 경우 이 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-180">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="5b73e-181">구성 요소가 수명 주기 메서드의 오류를 처리하려면 오류 처리 논리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-181">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="5b73e-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>가 제품을 가져오는 메서드를 호출하는 다음 예제에서는 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-182">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="5b73e-183">`ProductRepository.GetProductByIdAsync` 메서드에서 throw된 예외는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-183">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="5b73e-184">`catch` 블록이 실행될 때 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-184">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="5b73e-185">`loadFailed`가 사용자에게 오류 메시지를 표시하는 데 사용되는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-185">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="5b73e-186">오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-186">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="5b73e-187">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="5b73e-187">Rendering logic</span></span>

<span data-ttu-id="5b73e-188">`.razor` 구성 요소 파일의 선언적 태그는 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>라는 C# 메서드로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-188">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="5b73e-189">구성 요소가 렌더링되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>가 실행되고, 렌더링된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명하는 데이터 구조를 구축합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-189">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="5b73e-190">렌더링 논리는 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-190">Rendering logic can throw an exception.</span></span> <span data-ttu-id="5b73e-191">이 시나리오의 예는 `@someObject.PropertyName`이 평가되지만 `@someObject`가 `null`인 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-191">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="5b73e-192">렌더링 논리에 따라 throw된 처리되지 않은 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-192">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="5b73e-193">렌더링 논리에서 null 참조 예외가 발생하지 않도록 하려면 해당 멤버에 액세스하기 전에 `null` 개체를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-193">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="5b73e-194">다음 예제에서 `person.Address`가 `null`인 경우 `person.Address` 속성에 액세스되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-194">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="5b73e-195">위의 코드는 `person`이 `null`이 아니라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-195">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="5b73e-196">일반적으로 이 코드의 구조에 따르면 구성 요소가 렌더링될 때 개체가 확실히 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-196">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="5b73e-197">이러한 경우에는 렌더링 논리에서 `null`이 있는지 확인하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-197">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="5b73e-198">이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`가 존재한다고 보장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-198">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="5b73e-199">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="5b73e-199">Event handlers</span></span>

<span data-ttu-id="5b73e-200">클라이언트 쪽 코드는 다음을 사용하여 이벤트 처리기를 만들 때 C# 코드의 호출을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-200">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="5b73e-201">기타 `@on...` 특성</span><span class="sxs-lookup"><span data-stu-id="5b73e-201">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="5b73e-202">이벤트 처리기 코드는 이러한 시나리오에서 처리되지 않은 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-202">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="5b73e-203">이벤트 처리기가 처리되지 않은 예외(예: 데이터베이스 쿼리 실패)를 throw하는 경우 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-203">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="5b73e-204">앱에서 외부 이유로 인해 실패할 수 있는 코드를 호출하는 경우 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-204">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="5b73e-205">사용자 코드에서 예외를 트랩 및 처리하지 않는 경우 프레임워크는 예외를 로깅하고 회로를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-205">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="5b73e-206">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="5b73e-206">Component disposal</span></span>

<span data-ttu-id="5b73e-207">예를 들어, 사용자가 다른 페이지로 이동했으므로 UI에서 구성 요소가 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-207">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="5b73e-208"><xref:System.IDisposable?displayProperty=fullName>을 구현하는 구성 요소가 UI에서 제거되면 프레임워크는 구성 요소의 <xref:System.IDisposable.Dispose%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-208">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="5b73e-209">구성 요소의 `Dispose` 메서드가 처리되지 않은 예외를 throw하는 경우 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-209">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="5b73e-210">삭제 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅이 포함된 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-210">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="5b73e-211">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b73e-211">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="5b73e-212">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="5b73e-212">JavaScript interop</span></span>

<span data-ttu-id="5b73e-213"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>는 .NET 코드가 사용자의 브라우저에서 JavaScript 런타임에 대한 비동기 호출을 수행할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-213"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="5b73e-214">다음 조건은 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>의 오류 처리에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-214">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="5b73e-215"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 동기적으로 실패하면 .NET 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-215">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="5b73e-216">예를 들어, 제공된 인수를 직렬화 할 수 없기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-216">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="5b73e-217">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-217">Developer code must catch the exception.</span></span> <span data-ttu-id="5b73e-218">이벤트 처리기 또는 구성 요소 수명 주기 메서드의 앱 코드가 예외를 처리하지 않는 경우 결과 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-218">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="5b73e-219"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 비동기적으로 실패하면 .NET <xref:System.Threading.Tasks.Task>는 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-219">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="5b73e-220">예를 들어, JavaScript 쪽 코드에서 예외를 throw하거나 완료된 `Promise`를 `rejected`로 반환하기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-220">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="5b73e-221">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-221">Developer code must catch the exception.</span></span> <span data-ttu-id="5b73e-222">[`await`](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용하는 경우 오류 처리 및 로깅을 사용하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-222">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="5b73e-223">이 연산자를 사용하지 않는 경우 오류 코드는 Blazor Server 회로에 치명적인 처리되지 않은 예외를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-223">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="5b73e-224">기본적으로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 호출은 특정 기간 내에 완료되어야 합니다. 그렇지 않으면 호출 시간이 초과됩니다. 기본 제한 시간은 1분입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-224">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="5b73e-225">제한 시간은 네트워크 연결이 끊어진 코드 또는 완료 메시지를 다시 전송하지 않는 JavaScript 코드를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-225">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="5b73e-226">호출 시간이 초과되면 결과 <xref:System.Threading.Tasks>는 <xref:System.OperationCanceledException>을 나타내며 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-226">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="5b73e-227">로깅을 사용하여 예외를 트랩하고 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-227">Trap and process the exception with logging.</span></span>

<span data-ttu-id="5b73e-228">마찬가지로 JavaScript 코드는 [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) 특성으로 표시되는 .NET 메서드에 대한 호출을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-228">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="5b73e-229">이러한 .NET 메서드에서 처리되지 않은 예외를 throw하는 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-229">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="5b73e-230">이 예외는 Blazor Server 회로에 치명적으로 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-230">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="5b73e-231">JavaScript 쪽 `Promise`는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-231">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="5b73e-232">메서드 호출의 .NET 쪽 또는 JavaScript 쪽에서 오류 처리 코드를 사용하는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-232">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="5b73e-233">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b73e-233">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a><span data-ttu-id="5b73e-234">Blazor Server 미리 렌더링</span><span class="sxs-lookup"><span data-stu-id="5b73e-234">Blazor Server prerendering</span></span>

<span data-ttu-id="5b73e-235">렌더링된 HTML 태그가 사용자의 초기 HTTP 요청 일부로 반환되도록 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 Blazor 구성 요소를 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-235">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="5b73e-236">이 작업은 다음을 통해 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-236">This works by:</span></span>

* <span data-ttu-id="5b73e-237">동일한 페이지의 일부인 미리 렌더링된 모든 구성 요소에 대해 새 회로 생성</span><span class="sxs-lookup"><span data-stu-id="5b73e-237">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="5b73e-238">초기 HTML 생성</span><span class="sxs-lookup"><span data-stu-id="5b73e-238">Generating the initial HTML.</span></span>
* <span data-ttu-id="5b73e-239">사용자의 브라우저가 동일한 서버에 대해 SignalR 연결을 다시 설정할 때까지 회로를 `disconnected`로 처리.</span><span class="sxs-lookup"><span data-stu-id="5b73e-239">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="5b73e-240">연결이 설정되면 회로의 상호 작용이 다시 시작되고 구성 요소의 HTML 태그가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-240">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="5b73e-241">사전 렌더링 동안 구성 요소가 처리되지 않은 예외를 throw하는 경우(예: 수명 주기 방법 동안 또는 렌더링 논리에서)</span><span class="sxs-lookup"><span data-stu-id="5b73e-241">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="5b73e-242">이 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-242">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="5b73e-243"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미의 호출 스택에 대해 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-243">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="5b73e-244">따라서 개발자 코드에서 예외를 명시적으로 catch하지 않으면 전체 HTTP 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-244">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="5b73e-245">일반적인 경우에는 사전 렌더링에 실패하면 작업 구성 요소를 렌더링할 수 없기 때문에 구성 요소를 계속 빌드 및 렌더링하는 것은 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-245">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="5b73e-246">렌더링 중에 발생할 수 있는 오류를 허용하려면 예외를 throw할 수 있는 오류 처리 논리를 구성 요소 내부에 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-246">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="5b73e-247">오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-247">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="5b73e-248">[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미를 래핑하는 대신, <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미에 의해 렌더링되는 구성 요소에 오류 처리 논리를 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-248">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="5b73e-249">고급 시나리오</span><span class="sxs-lookup"><span data-stu-id="5b73e-249">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="5b73e-250">재귀 렌더링</span><span class="sxs-lookup"><span data-stu-id="5b73e-250">Recursive rendering</span></span>

<span data-ttu-id="5b73e-251">구성 요소는 재귀적으로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-251">Components can be nested recursively.</span></span> <span data-ttu-id="5b73e-252">이것은 재귀적 데이터 구조를 나타내는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-252">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="5b73e-253">예를 들어, `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-253">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="5b73e-254">반복적으로 렌더링하는 경우 무한 재귀가 발생하는 코딩 패턴을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-254">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="5b73e-255">주기가 포함된 데이터 구조는 재귀적으로 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-255">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="5b73e-256">예를 들어, 자식에 자기 자신이 포함된 트리 노드를 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-256">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="5b73e-257">주기가 포함된 레이아웃 체인을 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-257">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="5b73e-258">예를 들어, 레이아웃만 있는 레이아웃은 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-258">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="5b73e-259">최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 항목(규칙)을 위반할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-259">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="5b73e-260">렌더링 중 무한 루프:</span><span class="sxs-lookup"><span data-stu-id="5b73e-260">Infinite loops during rendering:</span></span>

* <span data-ttu-id="5b73e-261">렌더링 프로세스가 계속 지속되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-261">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="5b73e-262">종료되지 않는 루프를 만드는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-262">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="5b73e-263">이 시나리오에서 영향을 받는 Blazor Server 회로는 실패하고 스레드는 일반적으로 다음을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-263">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="5b73e-264">운영 체제에서 허용하는 만큼의 CPU 시간을 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-264">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="5b73e-265">서버 메모리를 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-265">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="5b73e-266">무제한 메모리를 사용하는 것은 종료되지 않는 루프가 반복될 때마다 컬렉션에 항목을 추가하는 시나리오와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-266">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="5b73e-267">무한 재귀 패턴을 방지하려면 재귀 렌더링 코드에 적절한 중지 조건이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-267">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="5b73e-268">사용자 지정 렌더링 트리 논리</span><span class="sxs-lookup"><span data-stu-id="5b73e-268">Custom render tree logic</span></span>

<span data-ttu-id="5b73e-269">대부분의 Blazor 구성 요소는 `.razor` 파일로 구현되며 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>에 작동하여 출력을 렌더링하는 논리를 생성하도록 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-269">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="5b73e-270">개발자는 프로시저 C# 코드를 사용하여 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 수동으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-270">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="5b73e-271">자세한 내용은 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b73e-271">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="5b73e-272">수동 렌더링 트리 작성기 논리를 사용하는 것은 일반적인 구성 요소 개발에는 권장되지 않는 안전하지 않은 고급 시나리오로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-272">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="5b73e-273"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 코드를 작성하는 경우 개발자는 코드의 정확성을 보장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-273">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="5b73e-274">예를 들어, 개발자는 다음을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-274">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="5b73e-275"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 및 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A>에 대한 호출은 올바르게 균형이 조정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-275">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="5b73e-276">특성은 올바른 위치에만 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-276">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="5b73e-277">잘못된 수동 렌더링 트리 작성기 논리가 있으면 충돌, 서버 중단 및 보안 취약성을 포함하는 정의되지 않은 임의 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-277">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="5b73e-278">어셈블리 코드 또는 MSIL 명령을 직접 작성하는 것과 동일한 수준의 복잡성과 동일한 수준의 위험에서 수동 렌더링 트리 작성기 논리를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="5b73e-278">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
