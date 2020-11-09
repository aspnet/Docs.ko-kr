---
title: 'ASP.NET Core Blazor 호스팅 모델 구성'
author: guardrex
description: 'ASP.NET Core Blazor 호스팅 모델 구성의 추가 시나리오에 대해 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: f8b6e65424948aaa7b28023497bbbf2a1ceb47dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056051"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="bc54e-103">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="bc54e-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="bc54e-104">[Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bc54e-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bc54e-105">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="bc54e-106">SignalR 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="bc54e-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="bc54e-107">‘이 섹션은 Blazor WebAssembly에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="bc54e-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="bc54e-108">cookie 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="bc54e-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="bc54e-110"><xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="bc54e-111">자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bc54e-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="bc54e-112">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="bc54e-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="bc54e-113">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="bc54e-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="bc54e-114">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="bc54e-115">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="bc54e-116">UI를 사용자 지정하려면 `_Host.cshtml` Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="bc54e-117">앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에 다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="bc54e-118">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="bc54e-119">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="bc54e-119">CSS class</span></span>                       | <span data-ttu-id="bc54e-120">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="bc54e-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="bc54e-121">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-121">A lost connection.</span></span> <span data-ttu-id="bc54e-122">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="bc54e-123">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="bc54e-124">서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="bc54e-125">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="bc54e-126">네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="bc54e-127">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="bc54e-128">다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-128">Reconnection rejected.</span></span> <span data-ttu-id="bc54e-129">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="bc54e-130">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="bc54e-131">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="bc54e-132">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="bc54e-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="bc54e-133">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="bc54e-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="bc54e-134">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="bc54e-135">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="bc54e-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="bc54e-136">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="bc54e-136">Render mode</span></span>

<span data-ttu-id="bc54e-137">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="bc54e-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="bc54e-138">Blazor Server 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="bc54e-139">이는 `_Host.cshtml` Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="bc54e-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="bc54e-141">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="bc54e-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="bc54e-142">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="bc54e-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="bc54e-143">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="bc54e-143">Render mode</span></span> | <span data-ttu-id="bc54e-144">설명</span><span class="sxs-lookup"><span data-stu-id="bc54e-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="bc54e-145">구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bc54e-146">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="bc54e-147">Blazor Server 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bc54e-148">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-148">Output from the component isn't included.</span></span> <span data-ttu-id="bc54e-149">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="bc54e-150">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="bc54e-151">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="bc54e-152">Blazor 회로 초기화</span><span class="sxs-lookup"><span data-stu-id="bc54e-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="bc54e-153">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="bc54e-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="bc54e-154">`Pages/_Host.cshtml` 파일에서 Blazor Server 앱의 [SignalR 회로](xref:blazor/hosting-models#circuits)의 수동 시작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="bc54e-155">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="bc54e-156">`Blazor.start`를 호출하는 스크립트를 `blazor.server.js` 스크립트의 태그 뒤, 닫는 `</body>` 태그 안에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="bc54e-157">`autostart`를 사용하지 않도록 설정하면 회로에 종속되지 않는 앱의 일부분도 정상적으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="bc54e-158">예를 들어 클라이언트 쪽 라우팅이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="bc54e-159">그러나 회로에 종속되는 모든 측면은 `Blazor.start`가 호출된 다음에야 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="bc54e-160">설정된 회로가 없으면 앱 동작을 예측할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="bc54e-161">예를 들어 회로의 연결이 끊어지면 구성 요소 메서드가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="bc54e-162">문서가 준비되면 Blazor 초기화</span><span class="sxs-lookup"><span data-stu-id="bc54e-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="bc54e-163">문서가 준비되었을 때 Blazor 앱을 초기화하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-163">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="bc54e-164">수동 시작의 결과로 생성되는 `Promise`에 연결</span><span class="sxs-lookup"><span data-stu-id="bc54e-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="bc54e-165">JS interop 초기화와 같은 추가 작업을 수행하려면 `then`을 사용하여 수동 Blazor 앱 시작의 결과로 생성되는 `Promise`에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="bc54e-166">SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="bc54e-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="bc54e-167">로깅</span><span class="sxs-lookup"><span data-stu-id="bc54e-167">Logging</span></span>

<span data-ttu-id="bc54e-168">SignalR 클라이언트 로깅을 구성하려면 클라이언트 작성기의 로그 수준으로 `configureLogging`을 호출하는 구성 개체(`configureSignalR`)를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="bc54e-169">앞의 예에서 `information`은 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>의 로그 수준과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="bc54e-170">다시 연결 처리기 수정</span><span class="sxs-lookup"><span data-stu-id="bc54e-170">Modify the reconnection handler</span></span>

<span data-ttu-id="bc54e-171">다음과 같은 사용자 지정 동작에 대한 다시 연결 처리기의 회로 연결 이벤트를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="bc54e-172">연결이 삭제되는 경우 사용자에게 알립니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="bc54e-173">회로가 연결된 경우 클라이언트에서 로깅을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="bc54e-174">연결 이벤트를 수정하려면 다음과 같은 연결 변경 내용에 대한 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="bc54e-175">끊어진 연결은 `onConnectionDown`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="bc54e-176">설정된 연결/다시 설정된 연결은 `onConnectionUp`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="bc54e-177">`onConnectionDown`과 `onConnectionUp`을 **모두** 다음과 같이 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="bc54e-178">다시 연결 다시 시도 횟수 및 간격 조정</span><span class="sxs-lookup"><span data-stu-id="bc54e-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="bc54e-179">다시 연결 다시 시도 횟수 및 간격을 조정하려면 다시 시도 횟수(`maxRetries`) 및 각 다시 시도에 허용되는 기간(밀리초)(`retryIntervalMilliseconds`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="bc54e-180">다시 연결 표시 숨기기 또는 바꾸기</span><span class="sxs-lookup"><span data-stu-id="bc54e-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="bc54e-181">다시 연결 표시를 숨기려면 다시 연결 처리기의 `_reconnectionDisplay`를 빈 개체(`{}` 또는 `new Object()`)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="bc54e-182">다시 연결 표시를 바꾸려면 앞의 예에서 `_reconnectionDisplay`를 표시할 요소로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="bc54e-183">자리 표시자 `{ELEMENT ID}`는 표시할 HTML 요소의 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bc54e-184">앱의 CSS(`wwwroot/css/site.css`)에서 모달 요소에 대해 `transition-delay` 속성을 설정하여 다시 연결 표시가 나타나기 전의 지연을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="bc54e-185">다음 예제에서는 전환 지연 시간을 500ms(기본값)에서 1,000ms(1초)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="bc54e-186">클라이언트에서 Blazor 회로 연결 끊기</span><span class="sxs-lookup"><span data-stu-id="bc54e-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="bc54e-187">기본적으로 [`unload` 페이지 이벤트](https://developer.mozilla.org/docs/Web/API/Window/unload_event)가 트리거될 때 Blazor 회로의 연결이 끊깁니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="bc54e-188">클라이언트에서 다른 시나리오에 대한 회로의 연결을 끊으려면 적절한 이벤트 처리기에서 `Blazor.disconnect`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="bc54e-189">다음 예제에서는 페이지가 숨겨질 때([`pagehide` 이벤트](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 회로의 연결이 끊깁니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="bc54e-190">정적 파일</span><span class="sxs-lookup"><span data-stu-id="bc54e-190">Static files</span></span>

<span data-ttu-id="bc54e-191">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="bc54e-191">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="bc54e-192"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>를 사용하여 추가 파일 매핑을 만들거나 다른 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-192">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="bc54e-193">다음 예제에서 `{EXTENSION}` 자리 표시자는 파일 확장명이고 `{CONTENT TYPE}` 자리 표시자는 콘텐츠 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-193">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="bc54e-194"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 사용하여 `Startup.ConfigureServices`(`Startup.cs`)에서 [DI(종속성 주입)](xref:blazor/fundamentals/dependency-injection)를 통해 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-194">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="bc54e-195">이 방법에서는 `blazor.server.js`를 처리하는 데 사용되는 것과 동일한 파일 공급자를 구성하므로 사용자 지정 구성이 `blazor.server.js` 처리를 방해하지 않는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-195">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="bc54e-196">예를 들어 `provider.Mappings.Remove(".js")`를 통해 공급자를 구성하여 JavaScript 파일에 대한 매핑을 제거하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="bc54e-196">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="bc54e-197">`Startup.Configure`(`Startup.cs`)에서 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>에 대한 두 호출을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-197">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="bc54e-198"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 사용하여 첫 번째 호출에서 사용자 지정 파일 공급자를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-198">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="bc54e-199">두 번째 미들웨어는 Blazor 프레임워크에서 제공하는 기본 정적 파일 구성을 사용하는 `blazor.server.js`를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-199">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="bc54e-200"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>을 사용해 사용자 지정 정적 파일 미들웨어를 실행하여 `_framework/blazor.server.js` 처리를 방해하지 않게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bc54e-200">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="bc54e-201">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="bc54e-201">Additional resources</span></span>

* <xref:fundamentals/logging/index>
