---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: ASP.NET Core Blazor 호스팅 모델 구성의 추가 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 075bcc68fd2dff0ebf2cfceacec24fde8c818603
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326534"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor 호스팅 모델 구성

[Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) 및 [Luke Latham](https://github.com/guardrex)

이 문서에서는 호스팅 모델 구성에 대해 설명합니다.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR 인증에 대한 원본 간 협상

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

‘이 섹션은 Blazor Server에 적용됩니다.’

Blazor Server 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다. 이는 `_Host.cshtml` Razor 페이지에서 설정합니다.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.

* 페이지에 미리 렌더링할지 여부
* 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

| 렌더링 모드 | 설명 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server 앱의 마커를 렌더링합니다. 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 구성 요소를 정적 HTML에 렌더링합니다. |

정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.

## <a name="initialize-the-no-locblazor-circuit"></a>Blazor 회로 초기화

‘이 섹션은 Blazor Server에 적용됩니다.’

`Pages/_Host.cshtml` 파일에서 Blazor Server 앱의 [SignalR 회로](xref:blazor/hosting-models#circuits)의 수동 시작을 구성합니다.

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* `Blazor.start`를 호출하는 스크립트를 `blazor.server.js` 스크립트의 태그 뒤, 닫는 `</body>` 태그 안에 넣습니다.

`autostart`를 사용하지 않도록 설정하면 회로에 종속되지 않는 앱의 일부분도 정상적으로 작동합니다. 예를 들어 클라이언트 쪽 라우팅이 작동합니다. 그러나 회로에 종속되는 모든 측면은 `Blazor.start`가 호출된 다음에야 작동합니다. 설정된 회로가 없으면 앱 동작을 예측할 수 없습니다. 예를 들어 회로의 연결이 끊어지면 구성 요소 메서드가 실행되지 않습니다.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>문서가 준비되면 Blazor 초기화

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

### <a name="configure-the-no-locsignalr-client"></a>SignalR 클라이언트 구성

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
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
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

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>클라이언트에서 Blazor 회로 연결 끊기

기본적으로 [`unload` 페이지 이벤트](https://developer.mozilla.org/docs/Web/API/Window/unload_event)가 트리거될 때 Blazor 회로의 연결이 끊깁니다. 클라이언트에서 다른 시나리오에 대한 회로의 연결을 끊으려면 적절한 이벤트 처리기에서 `Blazor.disconnect`를 호출합니다. 다음 예제에서는 페이지가 숨겨질 때([`pagehide` 이벤트](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 회로의 연결이 끊깁니다.

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a>HTML `<head>` 태그 요소에 영향

‘이 섹션은 Blazor WebAssembly 및 Blazor Server의 예정된 ASP.NET Core 5.0 릴리스에 적용됩니다.’

렌더링된 `Title`, `Link` 및 `Meta` 구성 요소는 HTML `<head>` 태그 요소에서 데이터를 추가하거나 업데이트합니다.

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

앞의 예제에서 `{TITLE}`, `{URL}` 및 `{DESCRIPTION}`의 자리 표시자는 문자열 값, Razor 변수 또는 Razor 식입니다.

다음 특성이 적용됩니다.

* 서버 쪽 사전 렌더링이 지원됩니다.
* `Value` 매개 변수는 `Title` 구성 요소의 유일하게 유효한 매개 변수입니다.
* `Meta` 및 `Link` 구성 요소에 제공되는 HTML 특성은 [추가 특성](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)에서 캡처되고 렌더링된 HTML 태그로 전달됩니다.
* 여러 `Title` 구성 요소의 경우 페이지 제목은 렌더링된 마지막 `Title` 구성 요소의 `Value`를 반영합니다.
* 특성이 동일한 여러 `Meta` 또는 `Link` 구성 요소가 포함된 경우 `Meta` 또는 `Link` 구성 요소마다 정확히 하나의 HTML 태그가 렌더링됩니다. 두 개의 `Meta` 또는 `Link` 구성 요소는 렌더링된 동일한 HTML 태그를 참조할 수 없습니다.
* 기존 `Meta` 또는 `Link` 구성 요소의 매개 변수에 대한 변경 내용은 렌더링된 HTML 태그에 반영됩니다.
* `Link` 또는 `Meta` 구성 요소가 더 이상 렌더링되지 않아 프레임워크에서 삭제되는 경우 렌더링된 HTML 태그가 제거됩니다.

프레임워크 구성 요소 중 하나가 자식 구성 요소에서 사용되는 경우 렌더링된 HTML 태그는 프레임워크 구성 요소를 포함하는 자식 구성 요소가 렌더링되는 한 부모 구성 요소의 다른 모든 자식 구성 요소에 영향을 미칩니다. 자식 구성 요소에서 이러한 프레임워크 구성 요소 중 하나를 사용하는 것과 `wwwroot/index.html` 또는 `Pages/_Host.cshtml`에 HTML 태그를 배치하는 것의 차이는 프레임워크 구성 요소의 렌더링된 HTML 태그입니다.

* 애플리케이션 상태에 의해 수정될 수 있습니다. 하드 코드된 HTML 태그는 애플리케이션 상태에 의해 수정될 수 없습니다.
* 부모 구성 요소가 더 이상 렌더링되지 않으면 HTML `<head>`에서 제거됩니다.

::: moniker-end

## <a name="static-files"></a>정적 파일

‘이 섹션은 Blazor Server에 적용됩니다.’

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>를 사용하여 추가 파일 매핑을 만들거나 다른 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 구성하려면 다음 방법 중 **하나** 를 사용합니다. 다음 예제에서 `{EXTENSION}` 자리 표시자는 파일 확장명이고 `{CONTENT TYPE}` 자리 표시자는 콘텐츠 형식입니다.

* <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 사용하여 `Startup.ConfigureServices`(`Startup.cs`)에서 [DI(종속성 주입)](xref:blazor/fundamentals/dependency-injection)를 통해 옵션을 구성합니다.

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

  이 방법에서는 `blazor.server.js`를 처리하는 데 사용되는 것과 동일한 파일 공급자를 구성하므로 사용자 지정 구성이 `blazor.server.js` 처리를 방해하지 않는지 확인해야 합니다. 예를 들어 `provider.Mappings.Remove(".js")`를 통해 공급자를 구성하여 JavaScript 파일에 대한 매핑을 제거하지 마세요.

* `Startup.Configure`(`Startup.cs`)에서 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>에 대한 두 호출을 사용합니다.
  * <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>를 사용하여 첫 번째 호출에서 사용자 지정 파일 공급자를 구성합니다.
  * 두 번째 미들웨어는 Blazor 프레임워크에서 제공하는 기본 정적 파일 구성을 사용하는 `blazor.server.js`를 처리합니다.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>을 사용해 사용자 지정 정적 파일 미들웨어를 실행하여 `_framework/blazor.server.js` 처리를 방해하지 않게 할 수 있습니다.

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/logging/index>
