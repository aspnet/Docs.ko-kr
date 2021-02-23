---
title: ASP.NET Core Blazor SignalR 지침
author: guardrex
description: Blazor SignalR 연결을 구성하고 관리하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279856"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a>ASP.NET Core Blazor SignalR 지침

ASP.NET Core SignalR 구성에 대한 일반 지침은 설명서 <xref:signalr/introduction> 영역의 항목을 참조하세요. [호스트된 Blazor WebAssembly 솔루션에 추가된](xref:tutorials/signalr-blazor) SignalR을 구성하려면 <xref:signalr/configuration#configure-server-options>을 참조하세요.

## <a name="circuit-handler-options"></a>회로 처리기 옵션

‘이 섹션은 Blazor Server에 적용됩니다.’

다음 표에 표시된 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>로 Blazor Server 회로를 구성합니다.

| 옵션 | 기본값 | 설명 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | 회로에서 처리되지 않은 예외가 발생하거나 JS interop를 통한 .NET 메서드 호출로 인해 예외가 발생하는 경우 자세한 예외 메시지를 JavaScript로 보냅니다. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | 지정된 서버에서 한 번에 메모리에 저장하는 연결되지 않은 최대 회로 수입니다. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3분 | 연결이 끊긴 회로가 메모리에서 해제되기 전까지 메모리에 저장되는 최대 시간입니다. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1분 | 비동기 JavaScript 함수 호출이 시간 초과할 때까지 서버가 대기하는 최대 시간입니다. |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | 임의의 시간에 서버가 강력한 재연결을 지원하기 위해 회로 하나당 메모리에 저장하는 승인되지 않은 최대 렌더링 일괄 처리 수입니다. 제한에 도달하면 클라이언트에서 하나 이상의 일괄 처리를 승인할 때까지 서버가 새 렌더링 일괄 처리의 생성을 중지합니다. |

<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>에 대한 옵션 대리자로 `Startup.ConfigureServices`의 옵션을 구성합니다. 다음 예제에서는 위의 표에 표시된 기본 옵션 값을 할당합니다.

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>를 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>와 함께 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions>를 사용합니다. 옵션 설명은 <xref:signalr/configuration#configure-server-options>을 참조하세요. 다음 예제에서는 기본 옵션 값을 할당합니다.

```csharp
using System;

...

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR 인증에 대한 원본 간 협상

‘이 섹션은 Blazor WebAssembly에 적용됩니다.’

cookie 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.

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

* <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.

## <a name="reflect-the-connection-state-in-the-ui"></a>UI에 연결 상태 반영

‘이 섹션은 Blazor Server에 적용됩니다.’

클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다. 다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.

UI를 사용자 지정하려면 `_Host.cshtml` Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에 다음을 추가합니다.

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.

| CSS 클래스                       | 표시&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 연결이 끊어졌습니다. 클라이언트가 다시 연결하는 중입니다. 모달을 표시합니다. |
| `components-reconnect-hide`     | 서버에 대해 활성 연결이 다시 설정되었습니다. 모달을 숨깁니다. |
| `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결하지 못했습니다. 다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다. |
| `components-reconnect-rejected` | 다시 연결이 거부되었습니다. 서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다. 앱을 다시 로드하려면 `location.reload()`를 호출합니다. 이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.<ul><li>서버 쪽 회로에서 크래시가 발생한 경우</li><li>서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우. 사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</li><li>서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</li></ul> |

## <a name="render-mode"></a>렌더링 모드

::: moniker range=">= aspnetcore-5.0"

‘이 섹션은 호스트된 Blazor WebAssembly 및 Blazor Server에 적용됩니다.’

Blazor 앱은 기본적으로 서버에서 UI를 미리 렌더링하도록 설정됩니다. 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

‘이 섹션은 Blazor Server에 적용됩니다.’

Blazor Server 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다. 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a>Blazor 회로 초기화

‘이 섹션은 Blazor Server에 적용됩니다.’

`Pages/_Host.cshtml` 파일에서 Blazor Server 앱의 [SignalR 회로](xref:blazor/hosting-models#circuits)의 수동 시작을 구성합니다.

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* `Blazor.start`를 호출하는 스크립트를 `blazor.server.js` 스크립트의 태그 뒤, 닫는 `</body>` 태그 안에 넣습니다.

`autostart`를 사용하지 않도록 설정하면 회로에 종속되지 않는 앱의 일부분도 정상적으로 작동합니다. 예를 들어 클라이언트 쪽 라우팅이 작동합니다. 그러나 회로에 종속되는 모든 측면은 `Blazor.start`가 호출된 다음에야 작동합니다. 설정된 회로가 없으면 앱 동작을 예측할 수 없습니다. 예를 들어 회로의 연결이 끊어지면 구성 요소 메서드가 실행되지 않습니다.

### <a name="initialize-blazor-when-the-document-is-ready"></a>문서가 준비되면 Blazor 초기화

문서가 준비되었을 때 Blazor 앱을 초기화하려면 다음을 수행합니다.

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>수동 시작의 결과로 생성되는 `Promise`에 연결

JS interop 초기화와 같은 추가 작업을 수행하려면 `then`을 사용하여 수동 Blazor 앱 시작의 결과로 생성되는 `Promise`에 연결합니다.

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

### <a name="configure-the-signalr-client"></a>SignalR 클라이언트 구성

#### <a name="logging"></a>로깅

SignalR 클라이언트 로깅을 구성하려면 클라이언트 작성기의 로그 수준으로 `configureLogging`을 호출하는 구성 개체(`configureSignalR`)를 전달합니다.

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

앞의 예에서 `information`은 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>의 로그 수준과 동일합니다.

### <a name="modify-the-reconnection-handler"></a>다시 연결 처리기 수정

다음과 같은 사용자 지정 동작에 대한 다시 연결 처리기의 회로 연결 이벤트를 수정할 수 있습니다.

* 연결이 삭제되는 경우 사용자에게 알립니다.
* 회로가 연결된 경우 클라이언트에서 로깅을 수행합니다.

연결 이벤트를 수정하려면 다음과 같은 연결 변경 내용에 대한 콜백을 등록합니다.

* 끊어진 연결은 `onConnectionDown`을 사용합니다.
* 설정된 연결/다시 설정된 연결은 `onConnectionUp`을 사용합니다.

`onConnectionDown`과 `onConnectionUp`을 **모두** 다음과 같이 지정해야 합니다.

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>다시 연결 다시 시도 횟수 및 간격 조정

다시 연결 다시 시도 횟수 및 간격을 조정하려면 다시 시도 횟수(`maxRetries`) 및 각 다시 시도에 허용되는 기간(밀리초)(`retryIntervalMilliseconds`)을 설정합니다.

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

## <a name="hide-or-replace-the-reconnection-display"></a>다시 연결 표시 숨기기 또는 바꾸기

다시 연결 표시를 숨기려면 다시 연결 처리기의 `_reconnectionDisplay`를 빈 개체(`{}` 또는 `new Object()`)로 설정합니다.

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

다시 연결 표시를 바꾸려면 앞의 예에서 `_reconnectionDisplay`를 표시할 요소로 설정합니다.

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

자리 표시자 `{ELEMENT ID}`는 표시할 HTML 요소의 ID입니다.

::: moniker range=">= aspnetcore-5.0"

앱의 CSS(`wwwroot/css/site.css`)에서 모달 요소에 대해 `transition-delay` 속성을 설정하여 다시 연결 표시가 나타나기 전의 지연을 사용자 지정합니다. 다음 예제에서는 전환 지연 시간을 500ms(기본값)에서 1,000ms(1초)로 설정합니다.

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>클라이언트에서 Blazor 회로 연결 끊기

기본적으로 [`unload` 페이지 이벤트](https://developer.mozilla.org/docs/Web/API/Window/unload_event)가 트리거될 때 Blazor 회로의 연결이 끊깁니다. 클라이언트에서 다른 시나리오에 대한 회로의 연결을 끊으려면 적절한 이벤트 처리기에서 `Blazor.disconnect`를 호출합니다. 다음 예제에서는 페이지가 숨겨질 때([`pagehide` 이벤트](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 회로의 연결이 끊깁니다.

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

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

## <a name="additional-resources"></a>추가 리소스

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Blazor Server 재연결 이벤트 및 구성 요소 수명 주기 이벤트](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
