---
title: ASP.NET Core Blazor WebAssembly 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 990882c03ddc14c664aa8da0518fb36087199aca
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550519"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="b7583-103">ASP.NET Core Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="b7583-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="b7583-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b7583-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="b7583-105">Blazor WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="b7583-106">또한 다음과 같은 IDE(통합 개발 환경)를 사용하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="b7583-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7583-107">Visual Studio</span></span>
* <span data-ttu-id="b7583-108">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7583-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="b7583-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7583-109">Visual Studio Code</span></span>

<span data-ttu-id="b7583-110">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-110">Available scenarios include:</span></span>

* <span data-ttu-id="b7583-111">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b7583-112">IDE에서 디버깅 지원으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="b7583-113">코드를 한 단계씩 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-113">Single-step through the code.</span></span>
* <span data-ttu-id="b7583-114">IDE에서 바로 가기 키를 사용하여 코드 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="b7583-115">‘로컬’ 창에서 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="b7583-116">JavaScript와 .NET 간의 호출 체인을 포함한 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="b7583-117">현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-117">For now, you *can't*:</span></span>

* <span data-ttu-id="b7583-118">처리되지 않은 예외에서 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b7583-119">디버그 프록시가 실행되기 전에 앱을 시작하는 동안 중단점에 적중합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7583-120">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="b7583-121">비로컬 시나리오(예: [Linux용 Windows 하위 시스템(WSL)](/windows/wsl/) 또는 [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline))에서 디버그합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7583-122">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="b7583-122">Prerequisites</span></span>

<span data-ttu-id="b7583-123">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b7583-124">Google Chrome(버전 70 이상)(기본값)</span><span class="sxs-lookup"><span data-stu-id="b7583-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="b7583-125">Microsoft Edge(버전 80 이상)</span><span class="sxs-lookup"><span data-stu-id="b7583-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="b7583-126">방화벽 또는 프록시가 디버그 프록시(`NodeJS` 프로세스)와의 통신을 차단하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="b7583-127">자세한 내용은 [방화벽 구성](#firewall-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="b7583-128">Mac용 Visual Studio에는 버전 8.8(빌드 1532) 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-128">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="b7583-129">**Mac용 Visual Studio 다운로드** 단추([Microsoft: Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) 페이지)를 선택하여 Mac용 Visual Studio 최신 릴리스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-129">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="b7583-130">Visual Studio 내에서 ‘미리 보기’ 채널을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-130">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="b7583-131">자세한 내용은 [Mac용 Visual Studio의 미리 보기 버전 설치](/visualstudio/mac/install-preview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-131">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="b7583-132">macOS의 Apple Safari는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-132">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="b7583-133">디버깅 사용</span><span class="sxs-lookup"><span data-stu-id="b7583-133">Enable debugging</span></span>

<span data-ttu-id="b7583-134">기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 `launchSettings.json` 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-134">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b7583-135">업데이트하면 `launchSettings.json` 파일이 다음 예와 비슷해집니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-135">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b7583-136">`inspectUri` 속성은</span><span class="sxs-lookup"><span data-stu-id="b7583-136">The `inspectUri` property:</span></span>

* <span data-ttu-id="b7583-137">Blazor WebAssembly 앱이라는 것을 검색하도록 IDE를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-137">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b7583-138">Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-138">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="b7583-139">시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-139">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7583-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7583-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b7583-141">Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면:</span><span class="sxs-lookup"><span data-stu-id="b7583-141">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b7583-142">새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-142">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b7583-143"><kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-143">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b7583-144">**디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-144">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="b7583-145">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-145">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="b7583-146">`*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-146">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="b7583-147">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-147">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="b7583-148">Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-148">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="b7583-149"><kbd>F5</kbd> 키를 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-149">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b7583-150">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-150">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="b7583-151"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-151">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b7583-152">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-152">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b7583-153">`Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-153">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="b7583-154"><kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-154">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="b7583-155"><kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="b7583-156">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b7583-156">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7583-157">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-157">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="b7583-158">앱이 `/` 이외의 다른 [앱 기본 경로](xref:blazor/host-and-deploy/index#app-base-path)에서 호스팅되는 경우 `Properties/launchSettings.json`에서 앱 기본 경로를 반영하여 다음 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-158">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="b7583-159">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="b7583-159">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="b7583-160">각 프로필의 `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="b7583-160">`inspectUri` of each profile:</span></span>

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

<span data-ttu-id="b7583-161">위의 설정에서 자리 표시자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-161">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="b7583-162">`{INSECURE PORT}`: 안전하지 않은 포트입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-162">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="b7583-163">기본적으로 임의 값이 제공되지만 사용자 지정 포트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="b7583-164">`{APP BASE PATH}`: 앱의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-164">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="b7583-165">`{SECURE PORT}`: 보안 포트입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-165">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="b7583-166">기본적으로 임의 값이 제공되지만 사용자 지정 포트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-166">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="b7583-167">`{PROFILE 1, 2, ... N}`: 시작 설정 프로필입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-167">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="b7583-168">일반적으로 앱은 기본적으로 둘 이상의 프로필을 지정합니다(예: Kestrel 서버에서 사용되는 프로젝트 프로필 및 IIS Express에 대한 프로필).</span><span class="sxs-lookup"><span data-stu-id="b7583-168">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="b7583-169">다음 예제에서 앱은 `wwwroot/index.html`에서 `<base href="/OAT/">`로 구성된 앱 기본 경로를 사용하여 `/OAT`에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-169">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="b7583-170">Blazor WebAssembly 앱에서의 사용자 지정 앱 기본 경로 사용에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-170">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7583-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7583-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="b7583-172">독립 실행형 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="b7583-172">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="b7583-173">VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-173">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="b7583-174">디버깅을 사용하기 위해 추가 설정이 필요하다는 알림이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-174">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="b7583-175">Blazor WebAssembly 애플리케이션을 디버그하려면 추가 설정이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-175">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="b7583-176">이러한 알림이 표시되면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-176">If you receive the notification:</span></span>

   * <span data-ttu-id="b7583-177">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-177">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="b7583-178">설치된 확장을 검사하려면 메뉴 모음에서 **보기** > **확장** 을 열거나 **작업** 사이드바에서 **확장** 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-178">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="b7583-179">JavaScript 미리 보기 디버깅이 사용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-179">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="b7583-180">메뉴 모음에서 설정을 엽니다(**파일** > **기본 설정** > **설정**).</span><span class="sxs-lookup"><span data-stu-id="b7583-180">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="b7583-181">`debug preview` 키워드를 사용하여 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-181">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="b7583-182">검색 결과에서 **디버그 > JavaScript: 미리 보기 사용** 확인란이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-182">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="b7583-183">미리 보기 디버깅을 사용하도록 설정하는 옵션이 없는 경우 최신 버전의 VS Code로 업그레이드하거나 [JavaScript 디버거 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)(VS Code 버전 1.46 이전)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-183">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="b7583-184">윈도우를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-184">Reload the window.</span></span>

1. <span data-ttu-id="b7583-185"><kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-185">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b7583-186">**디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-186">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="b7583-187">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-187">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="b7583-188">메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-188">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="b7583-189">독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-189">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="b7583-190">`*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-190">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="b7583-191">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-191">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="b7583-192">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b7583-192">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7583-193">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-193">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="b7583-194">호스트된 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="b7583-194">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="b7583-195">VS Code에서 호스트된 Blazor WebAssembly 앱의 솔루션 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-195">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="b7583-196">프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-196">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="b7583-197">**예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-197">Select **Yes**.</span></span>

   > <span data-ttu-id="b7583-198">빌드 및 디버그에 필요한 자산이 ‘{APPLICATION NAME}’에서 누락되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-198">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="b7583-199">추가할까요?</span><span class="sxs-lookup"><span data-stu-id="b7583-199">Add them?</span></span>

1. <span data-ttu-id="b7583-200">창의 맨 위에 있는 명령 팔레트에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-200">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="b7583-201">`launch.json` 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-201">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="b7583-202">기존 디버깅 세션에 연결</span><span class="sxs-lookup"><span data-stu-id="b7583-202">Attach to an existing debugging session</span></span>

<span data-ttu-id="b7583-203">실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 `launch.json` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-203">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="b7583-204">독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-204">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="b7583-205">전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-205">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="b7583-206">구성 옵션 시작</span><span class="sxs-lookup"><span data-stu-id="b7583-206">Launch configuration options</span></span>

<span data-ttu-id="b7583-207">`blazorwasm` 디버그 형식(`.vscode/launch.json`)에 대해 지원되는 시작 구성 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-207">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="b7583-208">옵션</span><span class="sxs-lookup"><span data-stu-id="b7583-208">Option</span></span>    | <span data-ttu-id="b7583-209">설명</span><span class="sxs-lookup"><span data-stu-id="b7583-209">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="b7583-210">`launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-210">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="b7583-211">디버깅할 때 브라우저에서 열 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-211">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="b7583-212">기본값은 `https://localhost:5001`입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-212">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="b7583-213">디버깅 세션을 시작하는 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-213">The browser to launch for the debugging session.</span></span> <span data-ttu-id="b7583-214">`edge` 또는 `chrome`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-214">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="b7583-215">기본값은 `chrome`입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-215">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="b7583-216">JS 디버거에서 로그를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-216">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="b7583-217">로그를 생성하려면 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-217">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="b7583-218">호스트된 Blazor WebAssembly 앱을 시작하고 디버그하는 경우 `true`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-218">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="b7583-219">웹 서버의 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-219">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="b7583-220">하위 경로에서 앱이 제공되는 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-220">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="b7583-221">디버깅 세션이 연결되기를 기다릴 밀리초 수입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-221">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="b7583-222">기본값은 30,000 밀리초(30초)입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-222">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="b7583-223">호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-223">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="b7583-224">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-224">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="b7583-225">앱을 시작할 작업 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-225">The working directory to launch the app under.</span></span> <span data-ttu-id="b7583-226">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-226">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="b7583-227">시작된 프로세스에 제공할 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-227">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="b7583-228">`hosted`가 `true`로 설정된 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-228">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="b7583-229">시작 구성 예제</span><span class="sxs-lookup"><span data-stu-id="b7583-229">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="b7583-230">독립 실행형 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="b7583-230">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="b7583-231">지정된 URL에서 실행 중인 앱에 연결</span><span class="sxs-lookup"><span data-stu-id="b7583-231">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="b7583-232">Microsoft Edge를 사용하여 호스트된 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="b7583-232">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="b7583-233">브라우저 구성의 기본값은 Google Chrome입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-233">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="b7583-234">디버깅에 Microsoft Edge를 사용하는 경우 `browser`를 `edge`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-234">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="b7583-235">Google Chrome을 사용하려면 `browser` 옵션을 설정하거나 옵션의 값을 `chrome`로 설정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-235">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="b7583-236">앞의 예에서 `MyHostedApp.Server.dll`은  서버 앱의 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-236">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="b7583-237">`.vscode` 폴더는 `Client`, `Server` 및 `Shared` 폴더 옆의 솔루션 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-237">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7583-238">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7583-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b7583-239">Mac용 Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-239">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="b7583-240">새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-240">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b7583-241"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-241">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b7583-242">**디버깅하지 않고 시작**(<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-242">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="b7583-243">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-243">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="b7583-244">디버깅 세션용으로 Google Chrome 또는 Microsoft Edge 브라우저를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-244">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="b7583-245">`*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-245">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="b7583-246">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-246">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="b7583-247">Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-247">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="b7583-248"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-248">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="b7583-249">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-249">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="b7583-250"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-250">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b7583-251">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-251">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b7583-252">`Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-252">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="b7583-253"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속한 다음 `WeatherForecastController`에서 서버의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-253">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="b7583-254"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="b7583-255">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="b7583-255">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="b7583-256">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-256">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="b7583-257">자세한 내용은 [Mac용 Visual Studio를 사용하여 디버깅](/visualstudio/mac/debugging)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-257">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="b7583-258">브라우저에서 디버그</span><span class="sxs-lookup"><span data-stu-id="b7583-258">Debug in the browser</span></span>

<span data-ttu-id="b7583-259">‘이 섹션의 지침은 Windows에서 실행되는 Google Chrome 및 Microsoft Edge에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="b7583-259">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="b7583-260">개발 환경에서 앱의 디버그 빌드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-260">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b7583-261">브라우저를 시작하고 앱의 URL(예: `https://localhost:5001`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-261">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="b7583-262">브라우저에서 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 눌러 원격 디버깅을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-262">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="b7583-263">브라우저는 원격 디버깅을 사용하도록 설정하고 실행해야 합니다(기본값 아님).</span><span class="sxs-lookup"><span data-stu-id="b7583-263">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="b7583-264">원격 디버깅을 사용하지 않도록 설정하면 디버깅 포트가 열려 있는 브라우저를 시작하라는 지침과 함께 **디버깅 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-264">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="b7583-265">브라우저에 대한 지침을 다릅니다. 그러면 새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-265">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="b7583-266">이전 브라우저 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-266">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="b7583-267">원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 이전 단계에 나온 디버깅 바로 가기 키를 사용하면 새 디버거 탭이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-267">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="b7583-268">잠시 후 **소스** 탭에는 `file://` 노드 내에 있는 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-268">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="b7583-269">구성 요소 코드(`.razor` 파일) 및 C# 코드 파일(`.cs`)에서 사용자가 설정하는 중단점은 코드가 실행될 때 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-269">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="b7583-270">중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-270">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="b7583-271">Blazor에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-271">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b7583-272">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-272">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b7583-273">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-273">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b7583-274">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="b7583-274">Browser source maps</span></span>

<span data-ttu-id="b7583-275">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-275">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b7583-276">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-276">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b7583-277">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-277">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="b7583-278">방화벽 구성</span><span class="sxs-lookup"><span data-stu-id="b7583-278">Firewall configuration</span></span>

<span data-ttu-id="b7583-279">방화벽이 디버그 프록시와의 통신을 차단하는 경우 브라우저와 `NodeJS` 프로세스 간의 통신을 허용하는 방화벽 예외 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-279">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="b7583-280">보안 취약점 생성을 방지하려면 방화벽 구성을 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-280">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="b7583-281">보안 지침을 신중하게 적용하고, 보안 모범 사례를 따르고, 방화벽 제조업체에서 발급한 경고를 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-281">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="b7583-282">`NodeJS` 프로세스와의 개방형 통신 허용:</span><span class="sxs-lookup"><span data-stu-id="b7583-282">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="b7583-283">방화벽의 기능과 구성에 따라 모든 연결에 노드 서버를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-283">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="b7583-284">네트워크에 따라 위험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-284">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="b7583-285">**개발자 컴퓨터에서만 권장됩니다.**</span><span class="sxs-lookup"><span data-stu-id="b7583-285">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="b7583-286">가능하면 **신뢰할 수 있는 네트워크 또는 비공개 네트워크** 의 `NodeJS` 프로세스와의 개방형 통신만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-286">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="b7583-287">[Windows 방화벽](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 구성 지침은 [인바운드 프로그램 또는 서비스 규칙 만들기](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-287">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="b7583-288">자세한 내용은 Windows 방화벽 설명서 집합의 [고급 보안이 포함된 Windows Defender 방화벽](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 및 관련 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-288">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b7583-289">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b7583-289">Troubleshoot</span></span>

<span data-ttu-id="b7583-290">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-290">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="b7583-291">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-291">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b7583-292">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-292">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="b7583-293">ASP.NET Core HTTPS 개발 인증서를 설치하고 신뢰할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-293">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="b7583-294">자세한 내용은 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-294">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="b7583-295">Visual Studio를 사용하려면 **도구** > **옵션** > **디버깅** > **일반** 에서 **ASP.NET에 대해 JavaScript 디버깅 사용(Chrome, Edge 및 IE)** 옵션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-295">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="b7583-296">이것은 Visual Studio의 기본 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-296">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="b7583-297">디버깅이 작동하지 않는 경우 옵션이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-297">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="b7583-298">사용자 환경에서 HTTP 프록시를 사용하는 경우 `localhost`가 프록시 바이패스 설정에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-298">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="b7583-299">이 작업을 수행하려면 다음 중 하나에서 `NO_PROXY` 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-299">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="b7583-300">프로젝트의 `launchSettings.json` 파일에서.</span><span class="sxs-lookup"><span data-stu-id="b7583-300">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="b7583-301">모든 앱에 적용할 사용자 또는 시스템 환경 변수 수준에서.</span><span class="sxs-lookup"><span data-stu-id="b7583-301">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="b7583-302">환경 변수를 사용하는 경우 변경을 적용하려면 Visual Studio를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-302">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="b7583-303">방화벽 또는 프록시가 디버그 프록시(`NodeJS` 프로세스)와의 통신을 차단하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-303">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="b7583-304">자세한 내용은 [방화벽 구성](#firewall-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b7583-304">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="b7583-305">`OnInitialized{Async}`의 중단점이 적중되지 않음</span><span class="sxs-lookup"><span data-stu-id="b7583-305">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="b7583-306">Blazor 프레임워크의 디버깅 프록시는 시작하는 데 약간 시간이 걸리므로 [`OnInitialized{Async}` 수명 주기 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 적중되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-306">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="b7583-307">메서드 본문의 시작 부분에 지연을 추가하여 중단점이 적중될 때까지 디버그 프록시가 시작될 시간 여유를 주는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-307">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="b7583-308">[`if` 컴파일러 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)에 따라 지연을 포함하여 앱의 릴리스 빌드에 지연이 존재하지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-308">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="b7583-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="b7583-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="b7583-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="b7583-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="b7583-311">Visual Studio(Windows) 시간 제한</span><span class="sxs-lookup"><span data-stu-id="b7583-311">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="b7583-312">Visual Studio에서 시간 제한에 도달했다는 메시지가 표시되며 디버그 어댑터가 실행되지 않는 예외가 throw되는 경우 레지스트리 설정을 사용하여 시간 제한을 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-312">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="b7583-313">이전 명령의 `{TIMEOUT}` 자리 표시자는 밀리초 단위입니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-313">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="b7583-314">예를 들어 1분은 `60000`으로 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7583-314">For example, one minute is assigned as `60000`.</span></span>
