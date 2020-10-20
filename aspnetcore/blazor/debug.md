---
title: ASP.NET Core Blazor WebAssembly 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: b8dd272d673e84b45a39272531385ebfd1d06175
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900988"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly 디버그

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다. 또한 다음과 같은 IDE(통합 개발 환경)를 사용하여 앱을 디버그할 수 있습니다.

* Visual Studio
* Mac용 Visual Studio
* Visual Studio Code

사용 가능한 시나리오는 다음과 같습니다.

* 중단점을 설정하고 제거합니다.
* IDE에서 디버깅 지원으로 앱을 실행합니다.
* 코드를 한 단계씩 실행합니다.
* IDE에서 바로 가기 키를 사용하여 코드 실행을 계속합니다.
* ‘로컬’ 창에서 지역 변수의 값을 확인합니다.
* JavaScript와 .NET 간의 호출 체인을 포함한 호출 스택을 확인합니다.

현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.

* 처리되지 않은 예외에서 중단합니다.
* 디버그 프록시가 실행되기 전에 앱을 시작하는 동안 중단점에 적중합니다. 여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.

## <a name="prerequisites"></a>사전 요구 사항

디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.

* Google Chrome(버전 70 이상)(기본값)
* Microsoft Edge(버전 80 이상)

Mac용 Visual Studio에는 버전 8.8(빌드 1532) 이상이 필요합니다.

1. **Mac용 Visual Studio 다운로드** 단추([Microsoft: Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) 페이지)를 선택하여 Mac용 Visual Studio 최신 릴리스를 설치합니다.
1. Visual Studio 내에서 ‘미리 보기’ 채널을 선택합니다. 자세한 내용은 [Mac용 Visual Studio의 미리 보기 버전 설치](/visualstudio/mac/install-preview)를 참조하세요.

> [!NOTE]
> macOS의 Apple Safari는 현재 지원되지 않습니다.

## <a name="enable-debugging"></a>디버깅 사용

기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 `launchSettings.json` 파일을 업데이트합니다.

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

업데이트하면 `launchSettings.json` 파일이 다음 예와 비슷해집니다.

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri` 속성은

* Blazor WebAssembly 앱이라는 것을 검색하도록 IDE를 설정합니다.
* Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.

시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면:

1. 새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.
1. <kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.

   > [!NOTE]
   > **디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다. 디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.

1. `*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.
1. 브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.
1. Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.
1. <kbd>F5</kbd> 키를 눌러 실행을 계속합니다.

Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.

1. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.
1. `Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.
1. `Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.
1. <kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.
1. <kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.

> [!NOTE]
> 앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**. 여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.

앱이 `/` 이외의 다른 [앱 기본 경로](xref:blazor/host-and-deploy/index#app-base-path)에서 호스팅되는 경우 `Properties/launchSettings.json`에서 앱 기본 경로를 반영하여 다음 속성을 업데이트합니다.

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* 각 프로필의 `inspectUri`:

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

위의 설정에서 자리 표시자는 다음과 같습니다.

* `{INSECURE PORT}`: 안전하지 않은 포트입니다. 기본적으로 임의 값이 제공되지만 사용자 지정 포트가 허용됩니다.
* `{APP BASE PATH}`: 앱의 기본 경로입니다.
* `{SECURE PORT}`: 보안 포트입니다. 기본적으로 임의 값이 제공되지만 사용자 지정 포트가 허용됩니다.
* `{PROFILE 1, 2, ... N}`: 시작 설정 프로필입니다. 일반적으로 앱은 기본적으로 둘 이상의 프로필을 지정합니다(예: Kestrel 서버에서 사용되는 프로젝트 프로필 및 IIS Express에 대한 프로필).

다음 예제에서 앱은 `wwwroot/index.html`에서 `<base href="/OAT/">`로 구성된 앱 기본 경로를 사용하여 `/OAT`에서 호스팅됩니다.

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Blazor WebAssembly 앱에서의 사용자 지정 앱 기본 경로 사용에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">독립 실행형 Blazor WebAssembly 디버그</h2>

1. VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.

   디버깅을 사용하기 위해 추가 설정이 필요하다는 알림이 표시될 수 있습니다.

   > Blazor WebAssembly 애플리케이션을 디버그하려면 추가 설정이 필요합니다.

   이러한 알림이 표시되면 다음을 수행합니다.

   * 최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)이 설치되어 있는지 확인합니다. 설치된 확장을 검사하려면 메뉴 모음에서 **보기** > **확장**을 열거나 **작업** 사이드바에서 **확장** 아이콘을 선택합니다.
   * JavaScript 미리 보기 디버깅이 사용되는지 확인합니다. 메뉴 모음에서 설정을 엽니다(**파일** > **기본 설정** > **설정**). `debug preview` 키워드를 사용하여 검색합니다. 검색 결과에서 **디버그 > JavaScript: 미리 보기 사용** 확인란이 선택되어 있는지 확인합니다. 미리 보기 디버깅을 사용하도록 설정하는 옵션이 없는 경우 최신 버전의 VS Code로 업그레이드하거나 [JavaScript 디버거 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)(VS Code 버전 1.46 이전)을 설치합니다.
   * 윈도우를 다시 로드합니다.

1. <kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.

   > [!NOTE]
   > **디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다. 디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.

1. 메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.

1. 독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다.

1. `*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.

1. 브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.

> [!NOTE]
> 앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**. 여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.

## <a name="debug-hosted-no-locblazor-webassembly"></a>호스트된 Blazor WebAssembly 디버그

1. VS Code에서 호스트된 Blazor WebAssembly 앱의 솔루션 폴더를 엽니다.

1. 프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다. **예**를 선택합니다.

   > 빌드 및 디버그에 필요한 자산이 ‘{APPLICATION NAME}’에서 누락되었습니다. 추가할까요?

1. 창의 맨 위에 있는 명령 팔레트에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.

`launch.json` 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.

## <a name="attach-to-an-existing-debugging-session"></a>기존 디버깅 세션에 연결

실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 `launch.json` 파일을 만듭니다.

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> 독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다. 전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다.

## <a name="launch-configuration-options"></a>구성 옵션 시작

`blazorwasm` 디버그 형식(`.vscode/launch.json`)에 대해 지원되는 시작 구성 옵션은 다음과 같습니다.

| 옵션    | 설명 |
| --------- | ----------- |
| `request` | `launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다. |
| `url`     | 디버깅할 때 브라우저에서 열 URL입니다. 기본값은 `https://localhost:5001`입니다. |
| `browser` | 디버깅 세션을 시작하는 브라우저입니다. `edge` 또는 `chrome`로 설정합니다. 기본값은 `chrome`입니다. |
| `trace`   | JS 디버거에서 로그를 생성하는 데 사용됩니다. 로그를 생성하려면 `true`로 설정합니다. |
| `hosted`  | 호스트된 Blazor WebAssembly 앱을 시작하고 디버그하는 경우 `true`로 설정해야 합니다. |
| `webRoot` | 웹 서버의 절대 경로를 지정합니다. 하위 경로에서 앱이 제공되는 경우 설정해야 합니다. |
| `timeout` | 디버깅 세션이 연결되기를 기다릴 밀리초 수입니다. 기본값은 30,000 밀리초(30초)입니다. |
| `program` | 호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다. `hosted`가 `true`일 경우 설정해야 합니다. |
| `cwd`     | 앱을 시작할 작업 디렉터리입니다. `hosted`가 `true`일 경우 설정해야 합니다. |
| `env`     | 시작된 프로세스에 제공할 환경 변수입니다. `hosted`가 `true`로 설정된 경우에만 적용됩니다. |

## <a name="example-launch-configurations"></a>시작 구성 예제

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>독립 실행형 Blazor WebAssembly 앱 시작 및 디버그

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>지정된 URL에서 실행 중인 앱에 연결

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Microsoft Edge를 사용하여 호스트된 Blazor WebAssembly 앱 시작 및 디버그

브라우저 구성의 기본값은 Google Chrome입니다. 디버깅에 Microsoft Edge를 사용하는 경우 `browser`를 `edge`로 설정합니다. Google Chrome을 사용하려면 `browser` 옵션을 설정하거나 옵션의 값을 `chrome`로 설정하지 마세요.

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

앞의 예에서 `MyHostedApp.Server.dll`은  서버 앱의 어셈블리입니다. `.vscode` 폴더는 `Client`, `Server` 및 `Shared` 폴더 옆의 솔루션 폴더에 있습니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

Mac용 Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면 다음을 수행합니다.

1. 새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.
1. <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 디버거에서 앱을 실행합니다.

   > [!NOTE]
   > **디버깅하지 않고 시작**(<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>)은 지원되지 않습니다. 디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.

   > [!IMPORTANT]
   > 디버깅 세션용으로 Google Chrome 또는 Microsoft Edge 브라우저를 선택해야 합니다.

1. `*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.
1. 브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.
1. Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.
1. <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속합니다.

Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.

1. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.
1. `Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.
1. `Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.
1. <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속한 다음 `WeatherForecastController`에서 서버의 중단점을 적중시킵니다.
1. <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.

> [!NOTE]
> 앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**. 여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.

자세한 내용은 [Mac용 Visual Studio를 사용하여 디버깅](/visualstudio/mac/debugging)을 참조하세요.

---

## <a name="debug-in-the-browser"></a>브라우저에서 디버그

‘이 섹션의 지침은 Windows에서 실행되는 Google Chrome 및 Microsoft Edge에 적용됩니다.’

1. 개발 환경에서 앱의 디버그 빌드를 실행합니다.

1. 브라우저를 시작하고 앱의 URL(예: `https://localhost:5001`)로 이동합니다.

1. 브라우저에서 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 눌러 원격 디버깅을 시작할 수 있습니다.

   브라우저는 원격 디버깅을 사용하도록 설정하고 실행해야 합니다(기본값 아님). 원격 디버깅을 사용하지 않도록 설정하면 디버깅 포트가 열려 있는 브라우저를 시작하라는 지침과 함께 **디버깅 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 렌더링됩니다. 브라우저에 대한 지침을 다릅니다. 그러면 새 브라우저 창이 열립니다. 이전 브라우저 창을 닫습니다.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. 원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 이전 단계에 나온 디버깅 바로 가기 키를 사용하면 새 디버거 탭이 열립니다.

1. 잠시 후 **소스** 탭에는 `file://` 노드 내에 있는 앱의 .NET 어셈블리 목록이 표시됩니다.

1. 구성 요소 코드(`.razor` 파일) 및 C# 코드 파일(`.cs`)에서 사용자가 설정하는 중단점은 코드가 실행될 때 적중됩니다. 중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.

Blazor에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다. 디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다. 프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.

## <a name="browser-source-maps"></a>브라우저 소스 맵

브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다. 그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다. 대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.

## <a name="troubleshoot"></a>문제 해결

오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.

* **디버거** 탭에서 브라우저의 개발자 도구를 엽니다. 콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.
* ASP.NET Core HTTPS 개발 인증서를 설치하고 신뢰할 수 있는지 확인합니다. 자세한 내용은 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>를 참조하세요.
* Visual Studio를 사용하려면 **도구** > **옵션** > **디버깅** > **일반**에서 **ASP.NET에 대해 JavaScript 디버깅 사용(Chrome, Edge 및 IE)** 옵션이 필요합니다. 이것은 Visual Studio의 기본 설정입니다. 디버깅이 작동하지 않는 경우 옵션이 선택되어 있는지 확인합니다.
* 사용자 환경에서 HTTP 프록시를 사용하는 경우 `localhost`가 프록시 바이패스 설정에 포함되는지 확인합니다. 이 작업을 수행하려면 다음 중 하나에서 `NO_PROXY` 환경 변수를 설정합니다.
  * 프로젝트의 `launchSettings.json` 파일에서.
  * 모든 앱에 적용할 사용자 또는 시스템 환경 변수 수준에서. 환경 변수를 사용하는 경우 변경을 적용하려면 Visual Studio를 다시 시작합니다.

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>`OnInitialized{Async}`의 중단점이 적중되지 않음

Blazor 프레임워크의 디버깅 프록시는 시작하는 데 약간 시간이 걸리므로 [`OnInitialized{Async}` 수명 주기 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 적중되지 않을 수 있습니다. 메서드 본문의 시작 부분에 지연을 추가하여 중단점이 적중될 때까지 디버그 프록시가 시작될 시간 여유를 주는 것이 좋습니다. [`if` 컴파일러 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)에 따라 지연을 포함하여 앱의 릴리스 빌드에 지연이 존재하지 않도록 할 수 있습니다.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Visual Studio(Windows) 시간 제한

Visual Studio에서 시간 제한에 도달했다는 메시지가 표시되며 디버그 어댑터가 실행되지 않는 예외가 throw되는 경우 레지스트리 설정을 사용하여 시간 제한을 조정할 수 있습니다.

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

이전 명령의 `{TIMEOUT}` 자리 표시자는 밀리초 단위입니다. 예를 들어 1분은 `60000`으로 할당됩니다.
