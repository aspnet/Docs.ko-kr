---
title: ASP.NET Core Blazor SignalR 지침
author: guardrex
description: Blazor SignalR 연결을 구성하고 관리하는 방법을 알아봅니다.
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
uid: blazor/fundamentals/signalr
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 63dfd93fbc42a869211bc5cd481a8dbee6eb6c91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118917"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="80a60-103">ASP.NET Core Blazor SignalR 지침</span><span class="sxs-lookup"><span data-stu-id="80a60-103">ASP.NET Core Blazor SignalR guidance</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="80a60-104">이 문서에서는 Blazor 앱에서 SignalR 연결을 구성하고 관리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-104">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="80a60-105">ASP.NET Core SignalR 구성에 대한 일반 지침은 설명서 <xref:signalr/introduction> 영역의 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="80a60-106">[호스트된 Blazor WebAssembly 솔루션에 추가된](xref:tutorials/signalr-blazor) SignalR을 구성하려면 <xref:signalr/configuration#configure-server-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="80a60-107">SignalR 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="80a60-107">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="80a60-108">cookie 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="80a60-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests.</span></span>

  <span data-ttu-id="80a60-110">`IncludeRequestCredentialsMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="80a60-110">`IncludeRequestCredentialsMessageHandler.cs`:</span></span>

  ```csharp
  using System.Net.Http;
  using System.Threading;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Components.WebAssembly.Http;

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

* <span data-ttu-id="80a60-111">허브 연결이 구축되면 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-111">Where a hub connection is built, assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  HubConnectionBuilder hubConnecton;

  ...

  hubConnecton = new HubConnectionBuilder()
      .WithUrl(new Uri(NavigationManager.ToAbsoluteUri("/chathub")), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

  <span data-ttu-id="80a60-112">앞의 예제는 `/chathub`의 절대 URI 주소에 대해 허브 연결 URL을 구성하는데, 이것은 `Index` 구성 요소(`Pages/Index.razor`)의 [Blazor와 함께SignalR 사용 자습서](xref:tutorials/signalr-blazor)에서 사용된 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-112">The preceding example configures the hub connection URL to the absolute URI address at `/chathub`, which is the URL used in the [SignalR with Blazor tutorial](xref:tutorials/signalr-blazor) in the `Index` component (`Pages/Index.razor`).</span></span> <span data-ttu-id="80a60-113">문자열(예: `https://signalr.example.com`) 또는 [구성](xref:blazor/fundamentals/configuration)을 통해 URI를 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-113">The URI can also be set via a string, for example `https://signalr.example.com`, or via [configuration](xref:blazor/fundamentals/configuration).</span></span>

<span data-ttu-id="80a60-114">자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-114">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a><span data-ttu-id="80a60-115">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="80a60-115">Render mode</span></span>

<span data-ttu-id="80a60-116">SignalR을 사용하는 Blazor WebAssembly 앱이 서버에서 미리 렌더링하도록 구성된 경우, 서버에 대한 클라이언트 연결이 설정되기 전에 미리 렌더링이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-116">If a Blazor WebAssembly app that uses SignalR is configured to prerender on the server, prerendering occurs before the client connection to the server is established.</span></span> <span data-ttu-id="80a60-117">자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-117">For more information, see the following articles:</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="80a60-118">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="80a60-118">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="80a60-119">이 문서에서는 Blazor 앱에서 SignalR 연결을 구성하고 관리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-119">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="80a60-120">ASP.NET Core SignalR 구성에 대한 일반 지침은 설명서 <xref:signalr/introduction> 영역의 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-120">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="80a60-121">[호스트된 Blazor WebAssembly 솔루션에 추가된](xref:tutorials/signalr-blazor) SignalR을 구성하려면 <xref:signalr/configuration#configure-server-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-121">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="80a60-122">회로 처리기 옵션</span><span class="sxs-lookup"><span data-stu-id="80a60-122">Circuit handler options</span></span>

<span data-ttu-id="80a60-123">다음 표에 표시된 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>로 Blazor Server 회로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-123">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="80a60-124">옵션</span><span class="sxs-lookup"><span data-stu-id="80a60-124">Option</span></span> | <span data-ttu-id="80a60-125">기본값</span><span class="sxs-lookup"><span data-stu-id="80a60-125">Default</span></span> | <span data-ttu-id="80a60-126">설명</span><span class="sxs-lookup"><span data-stu-id="80a60-126">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="80a60-127">회로에서 처리되지 않은 예외가 발생하거나 JS interop를 통한 .NET 메서드 호출로 인해 예외가 발생하는 경우 자세한 예외 메시지를 JavaScript로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-127">Send detailed exception messages to JavaScript when an unhandled exception occurs on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="80a60-128">100</span><span class="sxs-lookup"><span data-stu-id="80a60-128">100</span></span> | <span data-ttu-id="80a60-129">서버에서 한 번에 메모리에 저장하는 연결되지 않은 최대 회로 수입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-129">Maximum number of disconnected circuits that the server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="80a60-130">3분</span><span class="sxs-lookup"><span data-stu-id="80a60-130">3 minutes</span></span> | <span data-ttu-id="80a60-131">연결이 끊긴 회로가 메모리에서 해제되기 전까지 메모리에 저장되는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-131">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="80a60-132">1분</span><span class="sxs-lookup"><span data-stu-id="80a60-132">1 minute</span></span> | <span data-ttu-id="80a60-133">비동기 JavaScript 함수 호출이 시간 초과할 때까지 서버가 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-133">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="80a60-134">10</span><span class="sxs-lookup"><span data-stu-id="80a60-134">10</span></span> | <span data-ttu-id="80a60-135">임의의 시간에 서버가 강력한 재연결을 지원하기 위해 회로 하나당 메모리에 저장하는 승인되지 않은 최대 렌더링 일괄 처리 수입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-135">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="80a60-136">제한에 도달하면 클라이언트에서 하나 이상의 일괄 처리를 승인할 때까지 서버가 새 렌더링 일괄 처리의 생성을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-136">After reaching the limit, the server stops producing new render batches until one or more batches are acknowledged by the client.</span></span> |

<span data-ttu-id="80a60-137"><xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>에 대한 옵션 대리자로 `Startup.ConfigureServices`의 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-137">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="80a60-138">다음 예제에서는 위의 표에 표시된 기본 옵션 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-138">The following example assigns the default option values shown in the preceding table.</span></span> <span data-ttu-id="80a60-139">`Startup.cs`가 <xref:System> 네임스페이스(`using System;`)를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-139">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="80a60-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="80a60-140">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="80a60-141"><xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>를 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>와 함께 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-141">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="80a60-142">옵션 설명은 <xref:signalr/configuration#configure-server-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-142">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="80a60-143">다음 예제에서는 기본 옵션 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-143">The following example assigns the default option values.</span></span> <span data-ttu-id="80a60-144">`Startup.cs`가 <xref:System> 네임스페이스(`using System;`)를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-144">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="80a60-145">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="80a60-145">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="80a60-146">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="80a60-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="80a60-147">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="80a60-148">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="80a60-149">UI를 사용자 지정하려면 `_Host.cshtml` Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page.</span></span>

<span data-ttu-id="80a60-150">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-150">`Pages/_Host.cshtml`:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="80a60-151">사이트의 스타일시트에 다음 CSS 스타일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-151">Add the following CSS styles to the site's stylesheet.</span></span>

<span data-ttu-id="80a60-152">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="80a60-152">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="80a60-153">다음 표에서는 Blazor 프레임워크에 의해 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-153">The following table describes the CSS classes applied to the `components-reconnect-modal` element by the Blazor framework.</span></span>

| <span data-ttu-id="80a60-154">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="80a60-154">CSS class</span></span>                       | <span data-ttu-id="80a60-155">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="80a60-155">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="80a60-156">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-156">A lost connection.</span></span> <span data-ttu-id="80a60-157">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-157">The client is attempting to reconnect.</span></span> <span data-ttu-id="80a60-158">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-158">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="80a60-159">서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-159">An active connection is re-established to the server.</span></span> <span data-ttu-id="80a60-160">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-160">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="80a60-161">네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-161">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="80a60-162">다시 연결을 시도하려면 JavaScript에서 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-162">To attempt reconnection, call `window.Blazor.reconnect()` in JavaScript.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="80a60-163">다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-163">Reconnection rejected.</span></span> <span data-ttu-id="80a60-164">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-164">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="80a60-165">앱을 다시 로드하려면 JavaScript에서 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-165">To reload the app, call `location.reload()` in JavaScript.</span></span> <span data-ttu-id="80a60-166">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-166">This connection state may result when:</span></span><ul><li><span data-ttu-id="80a60-167">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="80a60-167">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="80a60-168">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="80a60-168">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="80a60-169">사용자 구성 요소의 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-169">Instances of the user's components are disposed.</span></span></li><li><span data-ttu-id="80a60-170">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="80a60-170">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="80a60-171">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="80a60-171">Render mode</span></span>

<span data-ttu-id="80a60-172">기본적으로 Blazor Server 앱은 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-172">By default, Blazor Server apps prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="80a60-173">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80a60-173">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="80a60-174">Blazor 회로 초기화</span><span class="sxs-lookup"><span data-stu-id="80a60-174">Initialize the Blazor circuit</span></span>

<span data-ttu-id="80a60-175">`Pages/_Host.cshtml` 파일에서 Blazor Server 앱의 [SignalR 회로](xref:blazor/hosting-models#circuits)의 수동 시작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-175">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="80a60-176">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-176">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="80a60-177">`Blazor.start`를 호출하는 스크립트를 `blazor.server.js` 스크립트의 태그 뒤, 닫는 `</body>` 태그 안에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-177">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="80a60-178">`autostart`를 사용하지 않도록 설정하면 회로에 종속되지 않는 앱의 일부분도 정상적으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-178">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="80a60-179">예를 들어 클라이언트 쪽 라우팅이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-179">For example, client-side routing is operational.</span></span> <span data-ttu-id="80a60-180">그러나 회로에 종속되는 모든 측면은 `Blazor.start`가 호출된 다음에야 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-180">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="80a60-181">설정된 회로가 없으면 앱 동작을 예측할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-181">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="80a60-182">예를 들어 회로의 연결이 끊어지면 구성 요소 메서드가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-182">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="80a60-183">문서가 준비되면 Blazor 초기화</span><span class="sxs-lookup"><span data-stu-id="80a60-183">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="80a60-184">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-184">`Pages/_Host.cshtml`:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="80a60-185">수동 시작의 결과로 생성되는 `Promise`에 연결</span><span class="sxs-lookup"><span data-stu-id="80a60-185">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="80a60-186">JS interop 초기화와 같은 추가 작업을 수행하려면 [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)을 사용하여 수동 Blazor 앱 시작의 결과로 생성되는 [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-186">To perform additional tasks, such as JS interop initialization, use [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) to chain to the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) that results from a manual Blazor app start.</span></span>

<span data-ttu-id="80a60-187">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-187">`Pages/_Host.cshtml`:</span></span>

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

### <a name="configure-signalr-client-logging"></a><span data-ttu-id="80a60-188">SignalR 클라이언트 로깅 구성</span><span class="sxs-lookup"><span data-stu-id="80a60-188">Configure SignalR client logging</span></span>

<span data-ttu-id="80a60-189">클라이언트 작성기에서 로그 수준을 사용하여 `configureLogging`을 호출하는 `configureSignalR` 구성 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-189">On the client builder, pass in the `configureSignalR` configuration object that calls `configureLogging` with the log level.</span></span>

<span data-ttu-id="80a60-190">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-190">`Pages/_Host.cshtml`:</span></span>

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

<span data-ttu-id="80a60-191">앞의 예에서 `information`은 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>의 로그 수준과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-191">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="80a60-192">다시 연결 처리기 수정</span><span class="sxs-lookup"><span data-stu-id="80a60-192">Modify the reconnection handler</span></span>

<span data-ttu-id="80a60-193">다음과 같은 사용자 지정 동작에 대한 다시 연결 처리기의 회로 연결 이벤트를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-193">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="80a60-194">연결이 삭제되는 경우 사용자에게 알립니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-194">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="80a60-195">회로가 연결된 경우 클라이언트에서 로깅을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-195">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="80a60-196">연결 이벤트를 수정하려면 다음과 같은 연결 변경 내용에 대한 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-196">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="80a60-197">끊어진 연결은 `onConnectionDown`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-197">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="80a60-198">설정된 연결/다시 설정된 연결은 `onConnectionUp`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-198">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="80a60-199">**`onConnectionDown`과 `onConnectionUp`을 모두 지정해야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="80a60-199">**Both `onConnectionDown` and `onConnectionUp` must be specified.**</span></span>

<span data-ttu-id="80a60-200">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-200">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="80a60-201">다시 연결 다시 시도 횟수 및 간격 조정</span><span class="sxs-lookup"><span data-stu-id="80a60-201">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="80a60-202">다시 연결 다시 시도 횟수 및 간격을 조정하려면 다시 시도 횟수(`maxRetries`) 및 각 다시 시도에 허용되는 기간(밀리초)(`retryIntervalMilliseconds`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-202">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

<span data-ttu-id="80a60-203">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-203">`Pages/_Host.cshtml`:</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="80a60-204">다시 연결 표시 숨기기 또는 바꾸기</span><span class="sxs-lookup"><span data-stu-id="80a60-204">Hide or replace the reconnection display</span></span>

<span data-ttu-id="80a60-205">다시 연결 표시를 숨기려면 다시 연결 처리기의 `_reconnectionDisplay`를 빈 개체(`{}` 또는 `new Object()`)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-205">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

<span data-ttu-id="80a60-206">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="80a60-206">`Pages/_Host.cshtml`:</span></span>

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

<span data-ttu-id="80a60-207">다시 연결 표시를 바꾸려면 앞의 예에서 `_reconnectionDisplay`를 표시할 요소로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-207">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="80a60-208">자리 표시자 `{ELEMENT ID}`는 표시할 HTML 요소의 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-208">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="80a60-209">사이트의 CSS에서 모달 요소에 대해 `transition-delay` 속성을 설정하여 다시 연결 표시가 나타날 때까지 지연 시간을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-209">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the site's CSS for the modal element.</span></span> <span data-ttu-id="80a60-210">다음 예제에서는 전환 지연 시간을 500ms(기본값)에서 1,000ms(1초)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-210">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second).</span></span>

<span data-ttu-id="80a60-211">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="80a60-211">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="80a60-212">클라이언트에서 Blazor 회로 연결 끊기</span><span class="sxs-lookup"><span data-stu-id="80a60-212">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="80a60-213">기본적으로 [`unload` 페이지 이벤트](https://developer.mozilla.org/docs/Web/API/Window/unload_event)가 트리거될 때 Blazor 회로의 연결이 끊깁니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-213">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="80a60-214">클라이언트에서 다른 시나리오에 대한 회로의 연결을 끊으려면 적절한 이벤트 처리기에서 `Blazor.disconnect`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-214">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="80a60-215">다음 예제에서는 페이지가 숨겨질 때([`pagehide` 이벤트](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 회로의 연결이 끊깁니다.</span><span class="sxs-lookup"><span data-stu-id="80a60-215">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="80a60-216">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="80a60-216">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="80a60-217">Blazor Server 재연결 이벤트 및 구성 요소 수명 주기 이벤트</span><span class="sxs-lookup"><span data-stu-id="80a60-217">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
