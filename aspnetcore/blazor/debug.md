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
ms.openlocfilehash: adf22001e7d9b8ee4f36456cd4b07d2791a7331f
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395152"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="e22e2-103">ASP.NET Core Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="e22e2-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="e22e2-104">Blazor WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-104">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="e22e2-105">또한 다음과 같은 IDE(통합 개발 환경)를 사용하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-105">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="e22e2-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e22e2-106">Visual Studio</span></span>
* <span data-ttu-id="e22e2-107">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e22e2-107">Visual Studio for Mac</span></span>
* <span data-ttu-id="e22e2-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e22e2-108">Visual Studio Code</span></span>

<span data-ttu-id="e22e2-109">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-109">Available scenarios include:</span></span>

* <span data-ttu-id="e22e2-110">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-110">Set and remove breakpoints.</span></span>
* <span data-ttu-id="e22e2-111">IDE에서 디버깅 지원으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-111">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="e22e2-112">코드를 한 단계씩 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-112">Single-step through the code.</span></span>
* <span data-ttu-id="e22e2-113">IDE에서 바로 가기 키를 사용하여 코드 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-113">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="e22e2-114">‘로컬’ 창에서 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-114">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="e22e2-115">JavaScript와 .NET 간의 호출 체인을 포함한 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-115">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="e22e2-116">현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-116">For now, you *can't*:</span></span>

* <span data-ttu-id="e22e2-117">처리되지 않은 예외에서 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-117">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="e22e2-118">디버그 프록시가 실행되기 전에 앱을 시작하는 동안 중단점에 적중합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-118">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="e22e2-119">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-119">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="e22e2-120">비로컬 시나리오(예: [Linux용 Windows 하위 시스템(WSL)](/windows/wsl/) 또는 [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline))에서 디버그합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-120">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="e22e2-121">예를 들어 [`dotnet watch run`](xref:tutorials/dotnet-watch)으로 앱을 실행하여 디버그하는 동안 호스트된 Blazor WebAssembly 솔루션의 백 엔드 `*Server*` 앱을 자동으로 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-121">Automatically rebuild the backend `*Server*` app of a hosted Blazor WebAssembly solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e22e2-122">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="e22e2-122">Prerequisites</span></span>

<span data-ttu-id="e22e2-123">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="e22e2-124">Google Chrome(버전 70 이상)(기본값)</span><span class="sxs-lookup"><span data-stu-id="e22e2-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="e22e2-125">Microsoft Edge(버전 80 이상)</span><span class="sxs-lookup"><span data-stu-id="e22e2-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="e22e2-126">방화벽 또는 프록시가 디버그 프록시(`NodeJS` 프로세스)와의 통신을 차단하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="e22e2-127">자세한 내용은 [방화벽 구성](#firewall-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="e22e2-128">Visual Studio Code 사용자는 다음 확장이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-128">Visual Studio Code users require the following extensions:</span></span>

* [<span data-ttu-id="e22e2-129">Visual Studio Code용 C# 확장</span><span class="sxs-lookup"><span data-stu-id="e22e2-129">C# for Visual Studio Code Extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="e22e2-130">[Blazor WASM 디버깅 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion)(Visual Studio Code용 C# 확장 버전 1.23.9 이상을 사용하는 경우)</span><span class="sxs-lookup"><span data-stu-id="e22e2-130">[Blazor WASM Debugging Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) (when using the C# for Visual Studio Code Extension version 1.23.9 or later)</span></span>

<span data-ttu-id="e22e2-131">VS Code에서 프로젝트를 연 후, 디버깅을 사용하기 위해 추가 설정이 필요하다는 알림이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-131">After opening a project in VS Code, you may receive a notification that additional setup is required to enable debugging.</span></span> <span data-ttu-id="e22e2-132">요청하는 경우 Visual Studio Marketplace에서 필요한 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-132">If requested, install the required extensions from the Visual Studio Marketplace.</span></span> <span data-ttu-id="e22e2-133">설치된 확장을 검사하려면 메뉴 모음에서 **보기** > **확장** 을 열거나 **작업** 사이드바에서 **확장** 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-133">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>

<span data-ttu-id="e22e2-134">Mac용 Visual Studio에는 버전 8.8(빌드 1532) 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-134">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="e22e2-135">**Mac용 Visual Studio 다운로드** 단추([Microsoft: Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) 페이지)를 선택하여 Mac용 Visual Studio 최신 릴리스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-135">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="e22e2-136">Visual Studio 내에서 ‘미리 보기’ 채널을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-136">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="e22e2-137">자세한 내용은 [Mac용 Visual Studio의 미리 보기 버전 설치](/visualstudio/mac/install-preview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-137">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="e22e2-138">macOS의 Apple Safari는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-138">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="e22e2-139">디버깅 사용</span><span class="sxs-lookup"><span data-stu-id="e22e2-139">Enable debugging</span></span>

<span data-ttu-id="e22e2-140">기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 `launchSettings.json` 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-140">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="e22e2-141">업데이트하면 `launchSettings.json` 파일이 다음 예와 비슷해집니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-141">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="e22e2-142">`inspectUri` 속성은</span><span class="sxs-lookup"><span data-stu-id="e22e2-142">The `inspectUri` property:</span></span>

* <span data-ttu-id="e22e2-143">Blazor WebAssembly 앱이라는 것을 검색하도록 IDE를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-143">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="e22e2-144">Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-144">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="e22e2-145">시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-145">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e22e2-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e22e2-146">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e22e2-147">Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면:</span><span class="sxs-lookup"><span data-stu-id="e22e2-147">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="e22e2-148">호스트된 새 Blazor WebAssembly 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-148">Create a new hosted Blazor WebAssembly solution.</span></span>
1. <span data-ttu-id="e22e2-149"><kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-149">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="e22e2-150">**디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-150">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="e22e2-151">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-151">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="e22e2-152">`*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-152">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="e22e2-153">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-153">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="e22e2-154">Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-154">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="e22e2-155"><kbd>F5</kbd> 키를 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-155">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="e22e2-156">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-156">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="e22e2-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-157">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="e22e2-158">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-158">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="e22e2-159">`Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-159">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="e22e2-160"><kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-160">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="e22e2-161"><kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-161">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="e22e2-162">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="e22e2-162">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="e22e2-163">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-163">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="e22e2-164">앱이 `/` 이외의 다른 [앱 기본 경로](xref:blazor/host-and-deploy/index#app-base-path)에서 호스팅되는 경우 `Properties/launchSettings.json`에서 앱 기본 경로를 반영하여 다음 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-164">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="e22e2-165">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="e22e2-165">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="e22e2-166">각 프로필의 `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="e22e2-166">`inspectUri` of each profile:</span></span>

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

<span data-ttu-id="e22e2-167">위의 설정에서 자리 표시자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-167">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="e22e2-168">`{INSECURE PORT}`: 안전하지 않은 포트입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-168">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="e22e2-169">기본적으로 임의 값이 제공되지만 사용자 지정 포트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-169">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="e22e2-170">`{APP BASE PATH}`: 앱의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-170">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="e22e2-171">`{SECURE PORT}`: 보안 포트입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-171">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="e22e2-172">기본적으로 임의 값이 제공되지만 사용자 지정 포트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-172">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="e22e2-173">`{PROFILE 1, 2, ... N}`: 시작 설정 프로필입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-173">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="e22e2-174">일반적으로 앱은 기본적으로 둘 이상의 프로필을 지정합니다(예: Kestrel 서버에서 사용되는 프로젝트 프로필 및 IIS Express에 대한 프로필).</span><span class="sxs-lookup"><span data-stu-id="e22e2-174">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="e22e2-175">다음 예제에서 앱은 `wwwroot/index.html`에서 `<base href="/OAT/">`로 구성된 앱 기본 경로를 사용하여 `/OAT`에서 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-175">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="e22e2-176">Blazor WebAssembly 앱에서의 사용자 지정 앱 기본 경로 사용에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-176">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e22e2-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e22e2-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="e22e2-178">독립 실행형 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="e22e2-178">Debug standalone Blazor WebAssembly</span></span></h2>

<span data-ttu-id="e22e2-179">`.vscode` 폴더에서 VS Code 자산 구성에 대한 자세한 내용은 <xref:blazor/tooling>의 **Linux** 운영 체제 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-179">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="e22e2-180">VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-180">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="e22e2-181">디버깅을 사용하기 위해 추가 설정이 필요하다는 알림이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-181">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="e22e2-182">Blazor WebAssembly 애플리케이션을 디버그하려면 추가 설정이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-182">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="e22e2-183">이러한 알림이 표시되면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-183">If you receive the notification:</span></span>

   * <span data-ttu-id="e22e2-184">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-184">Confirm that the latest [C# for Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="e22e2-185">설치된 확장을 검사하려면 메뉴 모음에서 **보기** > **확장** 을 열거나 **작업** 사이드바에서 **확장** 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-185">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="e22e2-186">[Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) **버전 1.23.9 이상** 을 사용하는 경우 최신 [Blazor WASM 디버깅 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion)이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-186">When using the [C# for Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) **version 1.23.9 or later**, confirm that the latest [Blazor WASM Debugging Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.blazorwasm-companion) is installed.</span></span> <span data-ttu-id="e22e2-187">설치된 확장을 검사하려면 메뉴 모음에서 **보기** > **확장** 을 열거나 **작업** 사이드바에서 **확장** 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-187">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="e22e2-188">JavaScript 미리 보기 디버깅이 사용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-188">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="e22e2-189">메뉴 모음에서 설정을 엽니다(**파일** > **기본 설정** > **설정**).</span><span class="sxs-lookup"><span data-stu-id="e22e2-189">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="e22e2-190">`debug preview` 키워드를 사용하여 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-190">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="e22e2-191">검색 결과에서 **디버그 > JavaScript: 미리 보기 사용** 의 확인란을 선택하거나 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-191">In the search results, set or confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="e22e2-192">미리 보기 디버깅을 사용하도록 설정하는 옵션이 없는 경우 최신 버전의 VS Code로 업그레이드하거나 [JavaScript 디버거 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)(VS Code 버전 1.46 이전)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-192">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="e22e2-193">윈도우를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-193">Reload the window.</span></span>

1. <span data-ttu-id="e22e2-194"><kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-194">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="e22e2-195">**디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-195">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="e22e2-196">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-196">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="e22e2-197">메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-197">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="e22e2-198">독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-198">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="e22e2-199">`*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-199">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="e22e2-200">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-200">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="e22e2-201">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="e22e2-201">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="e22e2-202">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-202">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="e22e2-203">호스트된 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="e22e2-203">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="e22e2-204">VS Code에서 호스트된  **솔루션 폴더의 `Client`** Blazor 프로젝트 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-204">Open the **`Client`** project folder of the hosted Blazor solution folder in VS Code.</span></span>

1. <span data-ttu-id="e22e2-205">프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-205">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="e22e2-206">**예** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-206">Select **Yes**.</span></span>

   > <span data-ttu-id="e22e2-207">빌드 및 디버그에 필요한 자산이 ‘{APPLICATION NAME}’에서 누락되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-207">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="e22e2-208">추가할까요?</span><span class="sxs-lookup"><span data-stu-id="e22e2-208">Add them?</span></span>

   <span data-ttu-id="e22e2-209">`.vscode` 폴더에서 VS Code 자산 구성에 대한 자세한 내용은 <xref:blazor/tooling>의 **Linux** 운영 체제 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-209">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="e22e2-210">창의 맨 위에 있는 명령 팔레트에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-210">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="e22e2-211">`launch.json` 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-211">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="e22e2-212">기존 디버깅 세션에 연결</span><span class="sxs-lookup"><span data-stu-id="e22e2-212">Attach to an existing debugging session</span></span>

<span data-ttu-id="e22e2-213">실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 `launch.json` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-213">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="e22e2-214">독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-214">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="e22e2-215">전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-215">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="e22e2-216">구성 옵션 시작</span><span class="sxs-lookup"><span data-stu-id="e22e2-216">Launch configuration options</span></span>

<span data-ttu-id="e22e2-217">`blazorwasm` 디버그 형식(`.vscode/launch.json`)에 대해 지원되는 시작 구성 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-217">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="e22e2-218">옵션</span><span class="sxs-lookup"><span data-stu-id="e22e2-218">Option</span></span>    | <span data-ttu-id="e22e2-219">설명</span><span class="sxs-lookup"><span data-stu-id="e22e2-219">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="e22e2-220">`launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-220">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="e22e2-221">디버깅할 때 브라우저에서 열 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-221">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="e22e2-222">기본값은 `https://localhost:5001`입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-222">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="e22e2-223">디버깅 세션을 시작하는 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-223">The browser to launch for the debugging session.</span></span> <span data-ttu-id="e22e2-224">`edge` 또는 `chrome`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-224">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="e22e2-225">기본값은 `chrome`입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-225">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="e22e2-226">JS 디버거에서 로그를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-226">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="e22e2-227">로그를 생성하려면 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-227">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="e22e2-228">호스트된 Blazor WebAssembly 앱을 시작하고 디버그하는 경우 `true`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-228">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="e22e2-229">웹 서버의 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-229">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="e22e2-230">하위 경로에서 앱이 제공되는 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-230">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="e22e2-231">디버깅 세션이 연결되기를 기다릴 밀리초 수입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-231">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="e22e2-232">기본값은 30,000 밀리초(30초)입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-232">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="e22e2-233">호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-233">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="e22e2-234">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-234">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="e22e2-235">앱을 시작할 작업 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-235">The working directory to launch the app under.</span></span> <span data-ttu-id="e22e2-236">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-236">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="e22e2-237">시작된 프로세스에 제공할 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-237">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="e22e2-238">`hosted`가 `true`로 설정된 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-238">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="e22e2-239">시작 구성 예제</span><span class="sxs-lookup"><span data-stu-id="e22e2-239">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="e22e2-240">독립 실행형 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="e22e2-240">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="e22e2-241">지정된 URL에서 실행 중인 앱에 연결</span><span class="sxs-lookup"><span data-stu-id="e22e2-241">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-blazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="e22e2-242">Microsoft Edge를 사용하여 호스트된 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="e22e2-242">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="e22e2-243">브라우저 구성의 기본값은 Google Chrome입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-243">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="e22e2-244">디버깅에 Microsoft Edge를 사용하는 경우 `browser`를 `edge`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-244">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="e22e2-245">Google Chrome을 사용하려면 `browser` 옵션을 설정하거나 옵션의 값을 `chrome`로 설정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-245">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="e22e2-246">앞의 예에서 `MyHostedApp.Server.dll`은  서버 앱의 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-246">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="e22e2-247">`.vscode` 폴더는 `Client`, `Server` 및 `Shared` 폴더 옆의 솔루션 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-247">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e22e2-248">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e22e2-248">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e22e2-249">Mac용 Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-249">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="e22e2-250">새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-250">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="e22e2-251"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-251">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="e22e2-252">**디버깅하지 않고 시작**(<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-252">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="e22e2-253">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-253">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="e22e2-254">디버깅 세션용으로 Google Chrome 또는 Microsoft Edge 브라우저를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-254">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="e22e2-255">`*Client*` 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-255">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="e22e2-256">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-256">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="e22e2-257">Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-257">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="e22e2-258"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-258">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="e22e2-259">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-259">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="e22e2-260"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-260">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="e22e2-261">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-261">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="e22e2-262">`Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-262">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="e22e2-263"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속한 다음 `WeatherForecastController`에서 서버의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-263">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="e22e2-264"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-264">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="e22e2-265">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="e22e2-265">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="e22e2-266">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-266">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="e22e2-267">자세한 내용은 [Mac용 Visual Studio를 사용하여 디버깅](/visualstudio/mac/debugging)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-267">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="e22e2-268">브라우저에서 디버그</span><span class="sxs-lookup"><span data-stu-id="e22e2-268">Debug in the browser</span></span>

<span data-ttu-id="e22e2-269">‘이 섹션의 지침은 Windows에서 실행되는 Google Chrome 및 Microsoft Edge에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="e22e2-269">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="e22e2-270">개발 환경에서 앱의 디버그 빌드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-270">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="e22e2-271">브라우저를 시작하고 앱의 URL(예: `https://localhost:5001`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-271">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="e22e2-272">브라우저에서 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 눌러 원격 디버깅을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-272">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="e22e2-273">브라우저는 원격 디버깅을 사용하도록 설정하고 실행해야 합니다(기본값 아님).</span><span class="sxs-lookup"><span data-stu-id="e22e2-273">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="e22e2-274">원격 디버깅을 사용하지 않도록 설정하면 디버깅 포트가 열려 있는 브라우저를 시작하라는 지침과 함께 **디버깅 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-274">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="e22e2-275">브라우저에 대한 지침을 다릅니다. 그러면 새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-275">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="e22e2-276">이전 브라우저 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-276">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="e22e2-277">원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 이전 단계에 나온 디버깅 바로 가기 키를 사용하면 새 디버거 탭이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-277">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="e22e2-278">잠시 후 **소스** 탭에는 `file://` 노드 내에 있는 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-278">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="e22e2-279">구성 요소 코드(`.razor` 파일) 및 C# 코드 파일(`.cs`)에서 사용자가 설정하는 중단점은 코드가 실행될 때 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-279">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="e22e2-280">중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-280">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="e22e2-281">Blazor에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-281">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="e22e2-282">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-282">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="e22e2-283">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-283">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="e22e2-284">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="e22e2-284">Browser source maps</span></span>

<span data-ttu-id="e22e2-285">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-285">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="e22e2-286">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-286">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="e22e2-287">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-287">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="e22e2-288">방화벽 구성</span><span class="sxs-lookup"><span data-stu-id="e22e2-288">Firewall configuration</span></span>

<span data-ttu-id="e22e2-289">방화벽이 디버그 프록시와의 통신을 차단하는 경우 브라우저와 `NodeJS` 프로세스 간의 통신을 허용하는 방화벽 예외 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-289">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="e22e2-290">보안 취약점 생성을 방지하려면 방화벽 구성을 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-290">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="e22e2-291">보안 지침을 신중하게 적용하고, 보안 모범 사례를 따르고, 방화벽 제조업체에서 발급한 경고를 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-291">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="e22e2-292">`NodeJS` 프로세스와의 개방형 통신 허용:</span><span class="sxs-lookup"><span data-stu-id="e22e2-292">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="e22e2-293">방화벽의 기능과 구성에 따라 모든 연결에 노드 서버를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-293">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="e22e2-294">네트워크에 따라 위험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-294">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="e22e2-295">**개발자 컴퓨터에서만 권장됩니다.**</span><span class="sxs-lookup"><span data-stu-id="e22e2-295">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="e22e2-296">가능하면 **신뢰할 수 있는 네트워크 또는 비공개 네트워크** 의 `NodeJS` 프로세스와의 개방형 통신만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-296">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="e22e2-297">[Windows 방화벽](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 구성 지침은 [인바운드 프로그램 또는 서비스 규칙 만들기](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-297">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="e22e2-298">자세한 내용은 Windows 방화벽 설명서 집합의 [고급 보안이 포함된 Windows Defender 방화벽](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) 및 관련 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-298">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e22e2-299">문제 해결</span><span class="sxs-lookup"><span data-stu-id="e22e2-299">Troubleshoot</span></span>

<span data-ttu-id="e22e2-300">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-300">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="e22e2-301">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-301">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="e22e2-302">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-302">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="e22e2-303">ASP.NET Core HTTPS 개발 인증서를 설치하고 신뢰할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-303">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="e22e2-304">자세한 내용은 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-304">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="e22e2-305">Visual Studio를 사용하려면 **도구** > **옵션** > **디버깅** > **일반** 에서 **ASP.NET에 대해 JavaScript 디버깅 사용(Chrome, Edge 및 IE)** 옵션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-305">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="e22e2-306">이것은 Visual Studio의 기본 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-306">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="e22e2-307">디버깅이 작동하지 않는 경우 옵션이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-307">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="e22e2-308">사용자 환경에서 HTTP 프록시를 사용하는 경우 `localhost`가 프록시 바이패스 설정에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-308">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="e22e2-309">이 작업을 수행하려면 다음 중 하나에서 `NO_PROXY` 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-309">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="e22e2-310">프로젝트의 `launchSettings.json` 파일에서.</span><span class="sxs-lookup"><span data-stu-id="e22e2-310">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="e22e2-311">모든 앱에 적용할 사용자 또는 시스템 환경 변수 수준에서.</span><span class="sxs-lookup"><span data-stu-id="e22e2-311">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="e22e2-312">환경 변수를 사용하는 경우 변경을 적용하려면 Visual Studio를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-312">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="e22e2-313">방화벽 또는 프록시가 디버그 프록시(`NodeJS` 프로세스)와의 통신을 차단하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-313">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="e22e2-314">자세한 내용은 [방화벽 구성](#firewall-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e22e2-314">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="e22e2-315">`OnInitialized{Async}`의 중단점이 적중되지 않음</span><span class="sxs-lookup"><span data-stu-id="e22e2-315">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="e22e2-316">Blazor 프레임워크의 디버깅 프록시는 시작하는 데 약간 시간이 걸리므로 [`OnInitialized{Async}` 수명 주기 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 적중되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-316">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="e22e2-317">메서드 본문의 시작 부분에 지연을 추가하여 중단점이 적중될 때까지 디버그 프록시가 시작될 시간 여유를 주는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-317">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="e22e2-318">[`if` 컴파일러 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)에 따라 지연을 포함하여 앱의 릴리스 빌드에 지연이 존재하지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-318">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="e22e2-319"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="e22e2-319"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="e22e2-320"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="e22e2-320"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="e22e2-321">Visual Studio(Windows) 시간 제한</span><span class="sxs-lookup"><span data-stu-id="e22e2-321">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="e22e2-322">Visual Studio에서 시간 제한에 도달했다는 메시지가 표시되며 디버그 어댑터가 실행되지 않는 예외가 throw되는 경우 레지스트리 설정을 사용하여 시간 제한을 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-322">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="e22e2-323">이전 명령의 `{TIMEOUT}` 자리 표시자는 밀리초 단위입니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-323">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="e22e2-324">예를 들어 1분은 `60000`으로 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="e22e2-324">For example, one minute is assigned as `60000`.</span></span>
