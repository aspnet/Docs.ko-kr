---
title: ASP.NET Core Blazor 앱에서 오류 처리
author: guardrex
description: 이 문서에서는 ASP.NET Core Blazor가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395099"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="8f48b-103">ASP.NET Core Blazor 앱에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="8f48b-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="8f48b-104">이 문서에서는 Blazor가 처리되지 않은 예외를 관리하는 방법과 오류를 감지 및 처리하는 앱을 개발하는 방법을 설명합니다</span><span class="sxs-lookup"><span data-stu-id="8f48b-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="8f48b-105">개발 중에 발생한 자세한 오류</span><span class="sxs-lookup"><span data-stu-id="8f48b-105">Detailed errors during development</span></span>

<span data-ttu-id="8f48b-106">Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="8f48b-107">오류가 발생하면 Blazor 앱의 화면 아래쪽에 연한 노란색 막대가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-107">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="8f48b-108">개발 중에 이 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-108">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="8f48b-109">프로덕션에서 이 막대는 오류가 발생했음을 알려 주고 브라우저를 새로 고치도록 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-109">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="8f48b-110">이 오류 처리 환경의 UI는 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-110">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="8f48b-111">Blazor WebAssembly 앱에서 `wwwroot/index.html` 파일의 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="8f48b-112">앱의 스타일시트(`wwwroot/css/app.css`)에 `blazor-error-ui` CSS 클래스의 `display: none` 스타일이 있기 때문에 `blazor-error-ui` 요소는 일반적으로 숨겨집니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-112">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the app's stylesheet (`wwwroot/css/app.css`).</span></span> <span data-ttu-id="8f48b-113">오류가 발생하면 프레임워크가 `display: block`을 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-113">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="8f48b-114">개발자 코드에서 처리되지 않은 예외 관리</span><span class="sxs-lookup"><span data-stu-id="8f48b-114">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="8f48b-115">오류가 발생한 후에도 앱을 계속하려면 앱에 오류 처리 논리가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-115">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="8f48b-116">이 문서의 나중 섹션에서는 처리되지 않은 예외의 잠재적 원인을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-116">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="8f48b-117">프로덕션 환경에서는 UI에서 프레임워크 예외 메시지 또는 스택 추적을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-117">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="8f48b-118">예외 메시지 또는 스택 추적을 렌더링하면 다음이 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-118">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="8f48b-119">최종 사용자에게 중요한 정보를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-119">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="8f48b-120">악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상시킬 수 있는 앱의 약점을 찾아내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-120">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="8f48b-121">전역 예외 처리</span><span class="sxs-lookup"><span data-stu-id="8f48b-121">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="8f48b-122">영구 공급자가 있는 오류 로깅</span><span class="sxs-lookup"><span data-stu-id="8f48b-122">Log errors with a persistent provider</span></span>

<span data-ttu-id="8f48b-123">처리되지 않은 예외가 발생하는 경우 예외가 서비스 컨테이너에 구성된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-123">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="8f48b-124">기본적으로 Blazor 앱은 콘솔 로깅 공급자의 콘솔 출력에 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-124">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="8f48b-125">로그 크기 관리 및 로그 회전과 함께 로깅 공급자를 사용하는 백 엔드 웹 API에 오류 정보를 보냄으로써 서버의 좀 더 영구적인 위치에 로깅하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-125">Consider logging to a more permanent location on the server by sending error information to a backend web API that uses a logging provider with log size management and log rotation.</span></span> <span data-ttu-id="8f48b-126">또는 백 엔드 웹 API 앱은 [Azure Application Insights(Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview) 같은 APM(애플리케이션 성능 관리) 서비스를 사용하여 클라이언트에서 수신하는 오류 정보를 로깅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-126">Alternatively, the backend web API app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview), to record error information that it receives from clients.</span></span>

<span data-ttu-id="8f48b-127">로깅할 인시던트 및 로깅된 인시던트의 심각도 수준을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-127">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="8f48b-128">악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-128">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="8f48b-129">예를 들어, 제품 정보를 표시하는 구성 요소의 URL에서 알 수 없는 `ProductId`가 제공되는 오류의 경우 인시던트를 로깅하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-129">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="8f48b-130">모든 오류가 로깅이 필요한 인시던트로 취급되는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-130">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="8f48b-131">자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-131">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

<span data-ttu-id="8f48b-132">&dagger;Blazor WebAssembly 앱을 지원하기 위한 네이티브 [Application Insights](/azure/azure-monitor/app/app-insights-overview) 기능과 [Google 애널리틱스](https://analytics.google.com/analytics/web/)에 대한 네이티브 Blazor 프레임워크 지원은 이러한 기술의 향후 릴리스에서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-132">&dagger;Native [Application Insights](/azure/azure-monitor/app/app-insights-overview) features to support Blazor WebAssembly apps and native Blazor framework support for [Google Analytics](https://analytics.google.com/analytics/web/) might become available in future releases of these technologies.</span></span> <span data-ttu-id="8f48b-133">자세한 내용은 [Blazor WASM 클라이언트 쪽 App Insights 지원(microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143)과 [웹 분석 및 진단(커뮤니티 구현에 대한 링크 포함)(dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-133">For more information, see [Support App Insights in Blazor WASM Client Side (microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) and [Web analytics and diagnostics (includes links to community implementations) (dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461).</span></span> <span data-ttu-id="8f48b-134">한편 클라이언트 쪽 Blazor WebAssembly 앱은 [JS interop](xref:blazor/call-javascript-from-dotnet)과 함께 [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript)를 사용하여 클라이언트 쪽 앱에서 Application Insights에 직접 오류를 로깅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-134">In the meantime, a client-side Blazor WebAssembly app can use the [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript) with [JS interop](xref:blazor/call-javascript-from-dotnet) to log errors directly to Application Insights from a client-side app.</span></span>

<span data-ttu-id="8f48b-135">&Dagger;Blazor 앱용 웹 API 백 엔드 앱인 서버 쪽 ASP.NET Core 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-135">&Dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span> <span data-ttu-id="8f48b-136">클라이언트 쪽 앱은 오류 정보를 트랩하고 웹 API로 보냅니다. 웹 API는 이 정보를 영구 로깅 공급자에 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-136">Client-side apps trap and send error information to a web API, which logs the error information to a persistent logging provider.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="8f48b-137">오류가 발생할 수 있는 위치</span><span class="sxs-lookup"><span data-stu-id="8f48b-137">Places where errors may occur</span></span>

<span data-ttu-id="8f48b-138">프레임워크 및 앱 코드는 다음 위치 중 하나에서 처리되지 않은 예외를 트리거할 수 있습니다. 자세한 내용은 이 문서의 아래 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-138">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="8f48b-139">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="8f48b-139">Component instantiation</span></span>](#component-instantiation-webassembly)
* [<span data-ttu-id="8f48b-140">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="8f48b-140">Lifecycle methods</span></span>](#lifecycle-methods-webassembly)
* [<span data-ttu-id="8f48b-141">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="8f48b-141">Rendering logic</span></span>](#rendering-logic-webassembly)
* [<span data-ttu-id="8f48b-142">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="8f48b-142">Event handlers</span></span>](#event-handlers-webassembly)
* [<span data-ttu-id="8f48b-143">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="8f48b-143">Component disposal</span></span>](#component-disposal-webassembly)
* [<span data-ttu-id="8f48b-144">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="8f48b-144">JavaScript interop</span></span>](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly"><span data-ttu-id="8f48b-145">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="8f48b-145">Component instantiation</span></span></h3>

<span data-ttu-id="8f48b-146">Blazor가 구성 요소의 인스턴스를 만들 경우</span><span class="sxs-lookup"><span data-stu-id="8f48b-146">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="8f48b-147">구성 요소의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-147">The component's constructor is invoked.</span></span>
* <span data-ttu-id="8f48b-148">[`@inject`](xref:mvc/views/razor#inject) 지시문 또는 [`[Inject]` 특성](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)을 통해 구성 요소 생성자에 제공된 singleton이 아닌 DI 서비스의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-148">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="8f48b-149">실행된 생성자 또는 `[Inject]` 속성의 setter에 오류가 발생하면 처리되지 않은 예외가 발생하고 프레임워크가 구성 요소를 인스턴스화하는 것이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-149">An error in an executed constructor or a setter for any `[Inject]` property results in an unhandled exception and stops the framework from instantiating the component.</span></span> <span data-ttu-id="8f48b-150">생성자 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-150">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-webassembly"><span data-ttu-id="8f48b-151">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="8f48b-151">Lifecycle methods</span></span></h3>

<span data-ttu-id="8f48b-152">구성 요소의 수명 주기 동안 Blazor는 [수명 주기 메서드](xref:blazor/components/lifecycle#lifecycle-methods)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-152">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="8f48b-153">구성 요소가 수명 주기 메서드의 오류를 처리하려면 오류 처리 논리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-153">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="8f48b-154"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>가 제품을 가져오는 메서드를 호출하는 다음 예제에서는 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-154">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="8f48b-155">`ProductRepository.GetProductByIdAsync` 메서드에서 throw된 예외는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-155">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="8f48b-156">`catch` 블록이 실행될 때 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-156">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="8f48b-157">`loadFailed`가 사용자에게 오류 메시지를 표시하는 데 사용되는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-157">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="8f48b-158">오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-158">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly"><span data-ttu-id="8f48b-159">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="8f48b-159">Rendering logic</span></span></h3>

<span data-ttu-id="8f48b-160">Razor 구성 요소 파일(`.razor`)의 선언적 태그는 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>라는 C# 메서드로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-160">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="8f48b-161">구성 요소가 렌더링되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>가 실행되고, 렌더링된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명하는 데이터 구조를 구축합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-161">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="8f48b-162">렌더링 논리는 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-162">Rendering logic can throw an exception.</span></span> <span data-ttu-id="8f48b-163">이 시나리오의 예는 `@someObject.PropertyName`이 평가되지만 `@someObject`가 `null`인 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-163">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span>

<span data-ttu-id="8f48b-164">렌더링 논리에서 <xref:System.NullReferenceException>이 발생하지 않도록 하려면 해당 멤버에 액세스하기 전에 `null` 개체를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-164">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="8f48b-165">다음 예제에서 `person.Address`가 `null`인 경우 `person.Address` 속성에 액세스되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-165">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="8f48b-166">위의 코드는 `person`이 `null`이 아니라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-166">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="8f48b-167">일반적으로 이 코드의 구조에 따르면 구성 요소가 렌더링될 때 개체가 확실히 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-167">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="8f48b-168">이러한 경우에는 렌더링 논리에서 `null`이 있는지 확인하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-168">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="8f48b-169">이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`이 존재한다고 보장할 수 있습니다. 다음 예제에서 이를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-169">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly"><span data-ttu-id="8f48b-170">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="8f48b-170">Event handlers</span></span></h3>

<span data-ttu-id="8f48b-171">클라이언트 쪽 코드는 다음을 사용하여 이벤트 처리기를 만들 때 C# 코드의 호출을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-171">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="8f48b-172">기타 `@on...` 특성</span><span class="sxs-lookup"><span data-stu-id="8f48b-172">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="8f48b-173">이벤트 처리기 코드는 이러한 시나리오에서 처리되지 않은 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-173">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="8f48b-174">앱에서 외부 이유로 인해 실패할 수 있는 코드를 호출하는 경우 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-174">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="8f48b-175">사용자 코드에서 예외를 트랩 및 처리하지 않는 경우 프레임워크는 예외를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-175">If user code doesn't trap and handle the exception, the framework logs the exception.</span></span>

<h3 id="component-disposal-webassembly"><span data-ttu-id="8f48b-176">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="8f48b-176">Component disposal</span></span></h3>

<span data-ttu-id="8f48b-177">예를 들어, 사용자가 다른 페이지로 이동했으므로 UI에서 구성 요소가 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-177">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="8f48b-178"><xref:System.IDisposable?displayProperty=fullName>을 구현하는 구성 요소가 UI에서 제거되면 프레임워크는 구성 요소의 <xref:System.IDisposable.Dispose%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-178">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="8f48b-179">삭제 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅이 포함된 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-179">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="8f48b-180">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-180">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-webassembly"><span data-ttu-id="8f48b-181">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="8f48b-181">JavaScript interop</span></span></h3>

<span data-ttu-id="8f48b-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>는 .NET 코드가 사용자의 브라우저에서 JavaScript 런타임에 대한 비동기 호출을 수행할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="8f48b-183">다음 조건은 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>의 오류 처리에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-183">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="8f48b-184"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 동기적으로 실패하면 .NET 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-184">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="8f48b-185">예를 들어, 제공된 인수를 직렬화 할 수 없기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-185">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="8f48b-186">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-186">Developer code must catch the exception.</span></span>
* <span data-ttu-id="8f48b-187"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 비동기적으로 실패하면 .NET <xref:System.Threading.Tasks.Task>는 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-187">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="8f48b-188">예를 들어, JavaScript 쪽 코드에서 예외를 throw하거나 완료된 `Promise`를 `rejected`로 반환하기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-188">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="8f48b-189">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-189">Developer code must catch the exception.</span></span> <span data-ttu-id="8f48b-190">[`await`](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용하는 경우 오류 처리 및 로깅을 사용하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-190">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>
* <span data-ttu-id="8f48b-191">기본적으로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 호출은 특정 기간 내에 완료되어야 합니다. 그렇지 않으면 호출 시간이 초과됩니다. 기본 제한 시간은 1분입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-191">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="8f48b-192">제한 시간은 네트워크 연결이 끊어진 코드 또는 완료 메시지를 다시 전송하지 않는 JavaScript 코드를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-192">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="8f48b-193">호출 시간이 초과되면 결과 <xref:System.Threading.Tasks>는 <xref:System.OperationCanceledException>을 나타내며 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-193">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="8f48b-194">로깅을 사용하여 예외를 트랩하고 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-194">Trap and process the exception with logging.</span></span>

<span data-ttu-id="8f48b-195">마찬가지로 JavaScript 코드는 [`[JSInvokable]` 특성](xref:blazor/call-dotnet-from-javascript)으로 표시되는 .NET 메서드에 대한 호출을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-195">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="8f48b-196">이러한 .NET 메서드가 처리되지 않은 예외를 throw하면 JavaScript 쪽 `Promise`가 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-196">If these .NET methods throw an unhandled exception, the JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="8f48b-197">메서드 호출의 .NET 쪽 또는 JavaScript 쪽에서 오류 처리 코드를 사용하는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-197">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="8f48b-198">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-198">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a><span data-ttu-id="8f48b-199">고급 시나리오</span><span class="sxs-lookup"><span data-stu-id="8f48b-199">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="8f48b-200">재귀 렌더링</span><span class="sxs-lookup"><span data-stu-id="8f48b-200">Recursive rendering</span></span>

<span data-ttu-id="8f48b-201">구성 요소는 재귀적으로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-201">Components can be nested recursively.</span></span> <span data-ttu-id="8f48b-202">이것은 재귀적 데이터 구조를 나타내는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-202">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="8f48b-203">예를 들어, `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-203">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="8f48b-204">반복적으로 렌더링하는 경우 무한 재귀가 발생하는 코딩 패턴을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-204">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="8f48b-205">주기가 포함된 데이터 구조는 재귀적으로 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-205">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="8f48b-206">예를 들어, 자식에 자기 자신이 포함된 트리 노드를 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-206">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="8f48b-207">주기가 포함된 레이아웃 체인을 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-207">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="8f48b-208">예를 들어, 레이아웃만 있는 레이아웃은 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-208">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="8f48b-209">최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 항목(규칙)을 위반할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-209">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="8f48b-210">렌더링 중 무한 루프:</span><span class="sxs-lookup"><span data-stu-id="8f48b-210">Infinite loops during rendering:</span></span>

* <span data-ttu-id="8f48b-211">렌더링 프로세스가 계속 지속되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-211">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="8f48b-212">종료되지 않는 루프를 만드는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-212">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="8f48b-213">이 시나리오에서 스레드는 일반적으로 다음을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-213">In these scenarios, the thread usually attempts to:</span></span>

* <span data-ttu-id="8f48b-214">운영 체제에서 허용하는 만큼의 CPU 시간을 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-214">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="8f48b-215">클라이언트 메모리를 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-215">Consume an unlimited amount of client memory.</span></span> <span data-ttu-id="8f48b-216">무제한 메모리를 사용하는 것은 종료되지 않는 루프가 반복될 때마다 컬렉션에 항목을 추가하는 시나리오와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-216">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="8f48b-217">무한 재귀 패턴을 방지하려면 재귀 렌더링 코드에 적절한 중지 조건이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-217">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="8f48b-218">사용자 지정 렌더링 트리 논리</span><span class="sxs-lookup"><span data-stu-id="8f48b-218">Custom render tree logic</span></span>

<span data-ttu-id="8f48b-219">대부분의 Blazor 구성 요소는 Razor 구성 요소 파일(`.razor`)로 구현되며 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>에서 작동하여 출력을 렌더링하는 논리를 생성하도록 프레임워크에 의해 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-219">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="8f48b-220">그러나 개발자는 프로시저 C# 코드를 사용하여 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 수동으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-220">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="8f48b-221">자세한 내용은 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-221">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="8f48b-222">수동 렌더링 트리 작성기 논리를 사용하는 것은 일반적인 구성 요소 개발에는 권장되지 않는 안전하지 않은 고급 시나리오로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-222">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="8f48b-223"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 코드를 작성하는 경우 개발자는 코드의 정확성을 보장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-223">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="8f48b-224">예를 들어, 개발자는 다음을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-224">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="8f48b-225"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 및 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A>에 대한 호출은 올바르게 균형이 조정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-225">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="8f48b-226">특성은 올바른 위치에만 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-226">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="8f48b-227">잘못된 수동 렌더링 트리 작성기 논리가 있으면 크래시, 앱 중단, 보안 취약성을 포함하는 정의되지 않은 임의 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-227">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, app hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="8f48b-228">어셈블리 코드 또는 [MSIL(Microsoft Intermediate Language)](/dotnet/standard/managed-code) 명령을 직접 작성하는 것과 동일한 수준의 복잡성과 동일한 수준의 ‘위험’에서 수동 렌더링 트리 작성기 논리를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-228">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f48b-229">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8f48b-229">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

<span data-ttu-id="8f48b-230">&dagger;클라이언트 쪽 Blazor WebAssembly 앱이 로깅에 사용하는 백 엔드 ASP.NET Core 웹 API 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-230">&dagger;Applies to backend ASP.NET Core web API apps that client-side Blazor WebAssembly apps use for logging.</span></span>

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="8f48b-231">개발 중에 발생한 자세한 오류</span><span class="sxs-lookup"><span data-stu-id="8f48b-231">Detailed errors during development</span></span>

<span data-ttu-id="8f48b-232">Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-232">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="8f48b-233">오류가 발생하면 Blazor 앱의 화면 아래쪽에 연한 노란색 막대가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-233">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="8f48b-234">개발 중에 이 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-234">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="8f48b-235">프로덕션에서 이 막대는 오류가 발생했음을 알려 주고 브라우저를 새로 고치도록 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-235">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="8f48b-236">이 오류 처리 환경의 UI는 [Blazor 프로젝트 템플릿](xref:blazor/project-structure)의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-236">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="8f48b-237">Blazor Server 앱에서 `Pages/_Host.cshtml` 파일의 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-237">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

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

<span data-ttu-id="8f48b-238">사이트의 스타일시트(`wwwroot/css/site.css`)에 `blazor-error-ui` CSS 클래스의 `display: none` 스타일이 있기 때문에 `blazor-error-ui` 요소는 일반적으로 숨겨집니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-238">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the site's stylesheet (`wwwroot/css/site.css`).</span></span> <span data-ttu-id="8f48b-239">오류가 발생하면 프레임워크가 `display: block`을 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-239">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="8f48b-240">Blazor Server 자세한 회로 오류</span><span class="sxs-lookup"><span data-stu-id="8f48b-240">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="8f48b-241">클라이언트 쪽 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-241">Client-side errors don't include the call stack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="8f48b-242">개발 단계에서는 자세한 오류를 사용하도록 설정하여 클라이언트에서 중요한 회로 오류 정보를 볼 수 있도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-242">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="8f48b-243"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-243">Set <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> to `true`.</span></span> <span data-ttu-id="8f48b-244">자세한 내용과 예제는 <xref:blazor/fundamentals/signalr#circuit-handler-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-244">For more information and an example, see <xref:blazor/fundamentals/signalr#circuit-handler-options>.</span></span>

<span data-ttu-id="8f48b-245"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> 설정에 대한 대안은 앱의 개발 환경 설정 파일(`appsettings.Development.json`)에서 `DetailedErrors` 구성 키를 `true`로 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-245">An alternative to setting <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> is to set the `DetailedErrors` configuration key to `true` in the app's Development environment settings file (`appsettings.Development.json`).</span></span>  <span data-ttu-id="8f48b-246">또한 자세한 SignalR 로깅을 위해 [SignalR 서버 쪽 로깅](xref:signalr/diagnostics#server-side-logging)(`Microsoft.AspNetCore.SignalR`)을 [디버그](xref:Microsoft.Extensions.Logging.LogLevel) 또는 [추적](xref:Microsoft.Extensions.Logging.LogLevel)으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-246">Additionally, set [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="8f48b-247">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="8f48b-247">`appsettings.Development.json`:</span></span>

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

<span data-ttu-id="8f48b-248">개발/스테이징 환경 서버 또는 로컬 시스템에서 값이 `true`인 `ASPNETCORE_DETAILEDERRORS` 환경 변수를 사용하여 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> 구성 키를 `true`로 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-248">The <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> configuration key can also be set to `true` using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true` on Development/Staging environment servers or on your local system.</span></span>

> [!WARNING]
> <span data-ttu-id="8f48b-249">인터넷의 클라이언트에 오류 정보를 노출하는 것을 항상 피하세요. 보안상 위험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-249">Always avoid exposing error information to clients on the Internet, which is a security risk.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="8f48b-250">Blazor Server 앱이 처리되지 않은 예외에 반응하는 방법</span><span class="sxs-lookup"><span data-stu-id="8f48b-250">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="8f48b-251">Blazor Server는 상태 저장 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-251">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="8f48b-252">사용자가 앱과 상호 작용하는 동안 *회로* 라는 서버에 대한 연결이 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-252">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="8f48b-253">회로는 활성 구성 요소 인스턴스와 다음과 같은 상태의 여러 다양한 측면을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-253">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="8f48b-254">구성 요소의 가장 최근에 렌더링된 출력</span><span class="sxs-lookup"><span data-stu-id="8f48b-254">The most recent rendered output of components.</span></span>
* <span data-ttu-id="8f48b-255">클라이언트 쪽 이벤트에 의해 트리거될 수 있는 현재 이벤트 처리 대리자 세트</span><span class="sxs-lookup"><span data-stu-id="8f48b-255">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="8f48b-256">사용자가 여러 브라우저 탭에서 앱을 여는 경우 여러 개의 독립적인 회로를 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-256">If a user opens the app in multiple browser tabs, the user creates multiple independent circuits.</span></span>

<span data-ttu-id="8f48b-257">Blazor는 발생하는 대부분의 처리되지 않은 예외를 회로에 치명적인 것으로 취급합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-257">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="8f48b-258">처리되지 않은 예외로 인해 회로가 종료되는 경우 사용자는 페이지를 다시 로드하여 새 회로를 만드는 방식으로 앱과 계속 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-258">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="8f48b-259">다른 사용자 또는 다른 브라우저 탭을 위한 회로이면서 종료된 회로의 외부에 있는 회로는 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-259">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="8f48b-260">이 시나리오는 충돌하는 데스크톱 앱과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-260">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="8f48b-261">작동을 중단한 앱을 다시 시작해야 하지만 다른 앱은 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-261">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="8f48b-262">다음과 같은 이유로 인해 처리되지 않은 예외가 발생하면 프레임워크가 회로를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-262">The framework terminates a circuit when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="8f48b-263">처리되지 않은 예외는 종종 회로를 정의되지 않은 상태로 둡니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-263">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="8f48b-264">처리되지 않은 예외가 발생한 후에는 앱의 일반 작업을 보장할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-264">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="8f48b-265">회로가 정의되지 않은 상태로 계속되는 경우 앱에 보안 취약성이 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-265">Security vulnerabilities may appear in the app if the circuit continues in an undefined state.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="8f48b-266">개발자 코드에서 처리되지 않은 예외 관리</span><span class="sxs-lookup"><span data-stu-id="8f48b-266">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="8f48b-267">오류가 발생한 후에도 앱을 계속하려면 앱에 오류 처리 논리가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-267">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="8f48b-268">이 문서의 나중 섹션에서는 처리되지 않은 예외의 잠재적 원인을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-268">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="8f48b-269">프로덕션 환경에서는 UI에서 프레임워크 예외 메시지 또는 스택 추적을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-269">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="8f48b-270">예외 메시지 또는 스택 추적을 렌더링하면 다음이 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-270">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="8f48b-271">최종 사용자에게 중요한 정보를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-271">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="8f48b-272">악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상시킬 수 있는 앱의 약점을 찾아내도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-272">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="8f48b-273">전역 예외 처리</span><span class="sxs-lookup"><span data-stu-id="8f48b-273">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

<span data-ttu-id="8f48b-274">이 섹션의 접근 방식은 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 오류를 처리하기 때문에 오류가 발생하고 회로가 활성 상태를 유지할 경우 클라이언트와 서버 간의 SignalR 연결이 끊어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-274">Because the approaches in this section handle errors with a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, the SignalR connection between the client and server isn't broken when an error occurs and the circuit remains alive.</span></span> <span data-ttu-id="8f48b-275">처리되지 않은 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-275">Any unhandled exception is fatal to a circuit.</span></span> <span data-ttu-id="8f48b-276">자세한 내용은 [Blazor Server 앱이 처리되지 않은 예외에 반응하는 방법](#how-a-blazor-server-app-reacts-to-unhandled-exceptions)에 대한 이전 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-276">For more information, see the preceding section on [how a Blazor Server app reacts to unhandled exceptions](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="8f48b-277">영구 공급자가 있는 오류 로깅</span><span class="sxs-lookup"><span data-stu-id="8f48b-277">Log errors with a persistent provider</span></span>

<span data-ttu-id="8f48b-278">처리되지 않은 예외가 발생하는 경우 예외가 서비스 컨테이너에 구성된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-278">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="8f48b-279">기본적으로 Blazor 앱은 콘솔 로깅 공급자의 콘솔 출력에 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-279">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="8f48b-280">로그 크기와 로그 순환을 관리하는 공급자와 함께 서버의 좀 더 영구적인 위치에 로깅하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-280">Consider logging to a more permanent location on the server with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="8f48b-281">또는 앱이 [Azure Application Insights(Azure Monitor)](/azure/azure-monitor/app/app-insights-overview) 같은 APM(애플리케이션 성능 관리) 서비스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-281">Alternatively, the app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview).</span></span>

<span data-ttu-id="8f48b-282">개발하는 동안 Blazor Server 앱은 일반적으로 디버깅에 도움을 주기 위해 브라우저 콘솔에 예외의 전체 세부 정보를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-282">During development, a Blazor Server app usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="8f48b-283">프로덕션 환경에서는 자세한 오류가 클라이언트에 전송되지 않지만, 예외의 전체 세부 정보가 서버에 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-283">In production, detailed errors aren't sent to clients, but an exception's full details are logged on the server.</span></span>

<span data-ttu-id="8f48b-284">로깅할 인시던트 및 로깅된 인시던트의 심각도 수준을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-284">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="8f48b-285">악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-285">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="8f48b-286">예를 들어, 제품 정보를 표시하는 구성 요소의 URL에서 알 수 없는 `ProductId`가 제공되는 오류의 경우 인시던트를 로깅하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-286">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="8f48b-287">모든 오류가 로깅이 필요한 인시던트로 취급되는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-287">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="8f48b-288">자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-288">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="8f48b-289">&dagger;Blazor 앱용 웹 API 백 엔드 앱인 서버 쪽 ASP.NET Core 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-289">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="8f48b-290">오류가 발생할 수 있는 위치</span><span class="sxs-lookup"><span data-stu-id="8f48b-290">Places where errors may occur</span></span>

<span data-ttu-id="8f48b-291">프레임워크 및 앱 코드는 다음 위치 중 하나에서 처리되지 않은 예외를 트리거할 수 있습니다. 자세한 내용은 이 문서의 아래 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-291">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="8f48b-292">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="8f48b-292">Component instantiation</span></span>](#component-instantiation-server)
* [<span data-ttu-id="8f48b-293">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="8f48b-293">Lifecycle methods</span></span>](#lifecycle-methods-server)
* [<span data-ttu-id="8f48b-294">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="8f48b-294">Rendering logic</span></span>](#rendering-logic-server)
* [<span data-ttu-id="8f48b-295">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="8f48b-295">Event handlers</span></span>](#event-handlers-server)
* [<span data-ttu-id="8f48b-296">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="8f48b-296">Component disposal</span></span>](#component-disposal-server)
* [<span data-ttu-id="8f48b-297">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="8f48b-297">JavaScript interop</span></span>](#javascript-interop-server)
* [<span data-ttu-id="8f48b-298">Blazor Server 다시 렌더링</span><span class="sxs-lookup"><span data-stu-id="8f48b-298">Blazor Server rerendering</span></span>](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server"><span data-ttu-id="8f48b-299">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="8f48b-299">Component instantiation</span></span></h3>

<span data-ttu-id="8f48b-300">Blazor가 구성 요소의 인스턴스를 만들 경우</span><span class="sxs-lookup"><span data-stu-id="8f48b-300">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="8f48b-301">구성 요소의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-301">The component's constructor is invoked.</span></span>
* <span data-ttu-id="8f48b-302">[`@inject`](xref:mvc/views/razor#inject) 지시문 또는 [`[Inject]` 특성](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)을 통해 구성 요소 생성자에 제공된 singleton이 아닌 DI 서비스의 생성자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-302">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="8f48b-303">모든 `[Inject]` 속성에 대해 실행된 생성자 또는 setter가 처리되지 않은 예외를 throw하는 경우 Blazor Server 회로가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-303">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="8f48b-304">이 예외는 프레임워크에서 구성 요소를 인스턴스화할 수 없기 때문에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-304">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="8f48b-305">생성자 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-305">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-server"><span data-ttu-id="8f48b-306">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="8f48b-306">Lifecycle methods</span></span></h3>

<span data-ttu-id="8f48b-307">구성 요소의 수명 주기 동안 Blazor는 [수명 주기 메서드](xref:blazor/components/lifecycle#lifecycle-methods)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-307">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="8f48b-308">수명 주기 메서드가 동기적 또는 비동기적으로 예외를 throw하는 경우 이 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-308">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="8f48b-309">구성 요소가 수명 주기 메서드의 오류를 처리하려면 오류 처리 논리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-309">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="8f48b-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>가 제품을 가져오는 메서드를 호출하는 다음 예제에서는 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-310">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="8f48b-311">`ProductRepository.GetProductByIdAsync` 메서드에서 throw된 예외는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-311">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="8f48b-312">`catch` 블록이 실행될 때 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-312">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="8f48b-313">`loadFailed`가 사용자에게 오류 메시지를 표시하는 데 사용되는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-313">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="8f48b-314">오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-314">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server"><span data-ttu-id="8f48b-315">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="8f48b-315">Rendering logic</span></span></h3>

<span data-ttu-id="8f48b-316">Razor 구성 요소 파일(`.razor`)의 선언적 태그는 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>라는 C# 메서드로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-316">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="8f48b-317">구성 요소가 렌더링되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>가 실행되고, 렌더링된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명하는 데이터 구조를 구축합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-317">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="8f48b-318">렌더링 논리는 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-318">Rendering logic can throw an exception.</span></span> <span data-ttu-id="8f48b-319">이 시나리오의 예는 `@someObject.PropertyName`이 평가되지만 `@someObject`가 `null`인 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-319">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="8f48b-320">렌더링 논리에 따라 throw된 처리되지 않은 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-320">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="8f48b-321">렌더링 논리에서 <xref:System.NullReferenceException>이 발생하지 않도록 하려면 해당 멤버에 액세스하기 전에 `null` 개체를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-321">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="8f48b-322">다음 예제에서 `person.Address`가 `null`인 경우 `person.Address` 속성에 액세스되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-322">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="8f48b-323">위의 코드는 `person`이 `null`이 아니라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-323">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="8f48b-324">일반적으로 이 코드의 구조에 따르면 구성 요소가 렌더링될 때 개체가 확실히 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-324">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="8f48b-325">이러한 경우에는 렌더링 논리에서 `null`이 있는지 확인하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-325">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="8f48b-326">이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`이 존재한다고 보장할 수 있습니다. 다음 예제에서 이를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-326">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server"><span data-ttu-id="8f48b-327">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="8f48b-327">Event handlers</span></span></h3>

<span data-ttu-id="8f48b-328">클라이언트 쪽 코드는 다음을 사용하여 이벤트 처리기를 만들 때 C# 코드의 호출을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-328">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="8f48b-329">기타 `@on...` 특성</span><span class="sxs-lookup"><span data-stu-id="8f48b-329">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="8f48b-330">이벤트 처리기 코드는 이러한 시나리오에서 처리되지 않은 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-330">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="8f48b-331">이벤트 처리기가 처리되지 않은 예외(예: 데이터베이스 쿼리 실패)를 throw하는 경우 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-331">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="8f48b-332">앱에서 외부 이유로 인해 실패할 수 있는 코드를 호출하는 경우 오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-332">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="8f48b-333">사용자 코드에서 예외를 트랩 및 처리하지 않는 경우 프레임워크는 예외를 로깅하고 회로를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-333">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

<h3 id="component-disposal-server"><span data-ttu-id="8f48b-334">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="8f48b-334">Component disposal</span></span></h3>

<span data-ttu-id="8f48b-335">예를 들어, 사용자가 다른 페이지로 이동했으므로 UI에서 구성 요소가 제거될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-335">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="8f48b-336"><xref:System.IDisposable?displayProperty=fullName>을 구현하는 구성 요소가 UI에서 제거되면 프레임워크는 구성 요소의 <xref:System.IDisposable.Dispose%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-336">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="8f48b-337">구성 요소의 `Dispose` 메서드가 처리되지 않은 예외를 throw하는 경우 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-337">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="8f48b-338">삭제 논리에서 예외를 throw할 수 있는 경우 앱은 오류 처리 및 로깅이 포함된 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용하여 예외를 트랩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-338">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="8f48b-339">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-339">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-server"><span data-ttu-id="8f48b-340">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="8f48b-340">JavaScript interop</span></span></h3>

<span data-ttu-id="8f48b-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>는 .NET 코드가 사용자의 브라우저에서 JavaScript 런타임에 대한 비동기 호출을 수행할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="8f48b-342">다음 조건은 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>의 오류 처리에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-342">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="8f48b-343"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 동기적으로 실패하면 .NET 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-343">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="8f48b-344">예를 들어, 제공된 인수를 직렬화 할 수 없기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-344">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="8f48b-345">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-345">Developer code must catch the exception.</span></span> <span data-ttu-id="8f48b-346">이벤트 처리기 또는 구성 요소 수명 주기 메서드의 앱 코드가 예외를 처리하지 않는 경우 결과 예외는 Blazor Server 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-346">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="8f48b-347"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 비동기적으로 실패하면 .NET <xref:System.Threading.Tasks.Task>는 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-347">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="8f48b-348">예를 들어, JavaScript 쪽 코드에서 예외를 throw하거나 완료된 `Promise`를 `rejected`로 반환하기 때문에 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-348">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="8f48b-349">개발자 코드는 예외를 catch해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-349">Developer code must catch the exception.</span></span> <span data-ttu-id="8f48b-350">[`await`](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용하는 경우 오류 처리 및 로깅을 사용하여 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-350">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="8f48b-351">이 연산자를 사용하지 않는 경우 오류 코드는 Blazor Server 회로에 치명적인 처리되지 않은 예외를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-351">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="8f48b-352">기본적으로 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 호출은 특정 기간 내에 완료되어야 합니다. 그렇지 않으면 호출 시간이 초과됩니다. 기본 제한 시간은 1분입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-352">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="8f48b-353">제한 시간은 네트워크 연결이 끊어진 코드 또는 완료 메시지를 다시 전송하지 않는 JavaScript 코드를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-353">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="8f48b-354">호출 시간이 초과되면 결과 <xref:System.Threading.Tasks>는 <xref:System.OperationCanceledException>을 나타내며 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-354">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="8f48b-355">로깅을 사용하여 예외를 트랩하고 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-355">Trap and process the exception with logging.</span></span>

<span data-ttu-id="8f48b-356">마찬가지로 JavaScript 코드는 [`[JSInvokable]` 특성](xref:blazor/call-dotnet-from-javascript)으로 표시되는 .NET 메서드에 대한 호출을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-356">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="8f48b-357">이러한 .NET 메서드에서 처리되지 않은 예외를 throw하는 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-357">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="8f48b-358">이 예외는 Blazor Server 회로에 치명적으로 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-358">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="8f48b-359">JavaScript 쪽 `Promise`는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-359">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="8f48b-360">메서드 호출의 .NET 쪽 또는 JavaScript 쪽에서 오류 처리 코드를 사용하는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-360">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="8f48b-361">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-361">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server"><span data-ttu-id="8f48b-362">Blazor Server 미리 렌더링</span><span class="sxs-lookup"><span data-stu-id="8f48b-362">Blazor Server prerendering</span></span></h3>

<span data-ttu-id="8f48b-363">렌더링된 HTML 태그가 사용자의 초기 HTTP 요청 일부로 반환되도록 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 Blazor 구성 요소를 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-363">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="8f48b-364">이 작업은 다음을 통해 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-364">This works by:</span></span>

* <span data-ttu-id="8f48b-365">동일한 페이지의 일부인 미리 렌더링된 모든 구성 요소에 대해 새 회로 생성</span><span class="sxs-lookup"><span data-stu-id="8f48b-365">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="8f48b-366">초기 HTML 생성</span><span class="sxs-lookup"><span data-stu-id="8f48b-366">Generating the initial HTML.</span></span>
* <span data-ttu-id="8f48b-367">사용자의 브라우저가 동일한 서버에 대해 SignalR 연결을 다시 설정할 때까지 회로를 `disconnected`로 처리.</span><span class="sxs-lookup"><span data-stu-id="8f48b-367">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="8f48b-368">연결이 설정되면 회로의 상호 작용이 다시 시작되고 구성 요소의 HTML 태그가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-368">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="8f48b-369">사전 렌더링 동안 구성 요소가 처리되지 않은 예외를 throw하는 경우(예: 수명 주기 방법 동안 또는 렌더링 논리에서)</span><span class="sxs-lookup"><span data-stu-id="8f48b-369">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="8f48b-370">이 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-370">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="8f48b-371"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미의 호출 스택에 대해 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-371">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="8f48b-372">따라서 개발자 코드에서 예외를 명시적으로 catch하지 않으면 전체 HTTP 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-372">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="8f48b-373">일반적인 경우에는 사전 렌더링에 실패하면 작업 구성 요소를 렌더링할 수 없기 때문에 구성 요소를 계속 빌드 및 렌더링하는 것은 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-373">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="8f48b-374">렌더링 중에 발생할 수 있는 오류를 허용하려면 예외를 throw할 수 있는 오류 처리 논리를 구성 요소 내부에 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-374">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="8f48b-375">오류 처리 및 로깅과 함께 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-375">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="8f48b-376">[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미를 래핑하는 대신, <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 태그 도우미에 의해 렌더링되는 구성 요소에 오류 처리 논리를 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-376">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="8f48b-377">고급 시나리오</span><span class="sxs-lookup"><span data-stu-id="8f48b-377">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="8f48b-378">재귀 렌더링</span><span class="sxs-lookup"><span data-stu-id="8f48b-378">Recursive rendering</span></span>

<span data-ttu-id="8f48b-379">구성 요소는 재귀적으로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-379">Components can be nested recursively.</span></span> <span data-ttu-id="8f48b-380">이것은 재귀적 데이터 구조를 나타내는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-380">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="8f48b-381">예를 들어, `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-381">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="8f48b-382">반복적으로 렌더링하는 경우 무한 재귀가 발생하는 코딩 패턴을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-382">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="8f48b-383">주기가 포함된 데이터 구조는 재귀적으로 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-383">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="8f48b-384">예를 들어, 자식에 자기 자신이 포함된 트리 노드를 렌더링하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-384">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="8f48b-385">주기가 포함된 레이아웃 체인을 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-385">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="8f48b-386">예를 들어, 레이아웃만 있는 레이아웃은 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-386">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="8f48b-387">최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 항목(규칙)을 위반할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-387">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="8f48b-388">렌더링 중 무한 루프:</span><span class="sxs-lookup"><span data-stu-id="8f48b-388">Infinite loops during rendering:</span></span>

* <span data-ttu-id="8f48b-389">렌더링 프로세스가 계속 지속되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-389">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="8f48b-390">종료되지 않는 루프를 만드는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-390">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="8f48b-391">이 시나리오에서 영향을 받는 Blazor Server 회로는 실패하고 스레드는 일반적으로 다음을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-391">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="8f48b-392">운영 체제에서 허용하는 만큼의 CPU 시간을 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-392">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="8f48b-393">서버 메모리를 무제한으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-393">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="8f48b-394">무제한 메모리를 사용하는 것은 종료되지 않는 루프가 반복될 때마다 컬렉션에 항목을 추가하는 시나리오와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-394">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="8f48b-395">무한 재귀 패턴을 방지하려면 재귀 렌더링 코드에 적절한 중지 조건이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-395">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="8f48b-396">사용자 지정 렌더링 트리 논리</span><span class="sxs-lookup"><span data-stu-id="8f48b-396">Custom render tree logic</span></span>

<span data-ttu-id="8f48b-397">대부분의 Blazor 구성 요소는 Razor 구성 요소 파일(`.razor`)로 구현되며 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>에서 작동하여 출력을 렌더링하는 논리를 생성하도록 프레임워크에 의해 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-397">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="8f48b-398">그러나 개발자는 프로시저 C# 코드를 사용하여 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 수동으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-398">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="8f48b-399">자세한 내용은 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-399">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="8f48b-400">수동 렌더링 트리 작성기 논리를 사용하는 것은 일반적인 구성 요소 개발에는 권장되지 않는 안전하지 않은 고급 시나리오로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-400">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="8f48b-401"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 코드를 작성하는 경우 개발자는 코드의 정확성을 보장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-401">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="8f48b-402">예를 들어, 개발자는 다음을 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-402">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="8f48b-403"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 및 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A>에 대한 호출은 올바르게 균형이 조정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-403">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="8f48b-404">특성은 올바른 위치에만 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-404">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="8f48b-405">잘못된 수동 렌더링 트리 작성기 논리가 있으면 충돌, 서버 중단 및 보안 취약성을 포함하는 정의되지 않은 임의 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-405">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="8f48b-406">어셈블리 코드 또는 [MSIL(Microsoft Intermediate Language)](/dotnet/standard/managed-code) 명령을 직접 작성하는 것과 동일한 수준의 복잡성과 동일한 수준의 ‘위험’에서 수동 렌더링 트리 작성기 논리를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="8f48b-406">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f48b-407">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="8f48b-407">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="8f48b-408">&dagger;Blazor 앱용 웹 API 백 엔드 앱인 서버 쪽 ASP.NET Core 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8f48b-408">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

::: zone-end
